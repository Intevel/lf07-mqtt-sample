<template>
  <div class="flex justify-end">
    <img src="/frame.png" alt="QR-Code" class="w-16 h-16" />
  </div>
  <div class="flex justify-center h-screen items-center text-center flex-col">
    <h1 class="font-extrabold text-2xl md:text-4xl mb-2">
      Lernfeld 07 - MQTT Beispiel
    </h1>
    <p class="text-gray-800">Verbindet euch über den QR-Code</p>
    <div class="mt-8" v-if="temperatureOne">
      <h2>
        Aktuelle Temperatur 1:
        <span class="text-blue-600 font-bold">{{ temperatureOne }} </span>
      </h2>
    </div>
    <div class="mt-4" v-if="temperatureTwo">
      <h2>
        Aktuelle Temperatur 2:
        <span class="text-blue-600 font-bold">{{ temperatureTwo }} </span>
      </h2>
    </div>
    <div class="mt-8" v-else>
      <h2>Warte auf Nachrichten...</h2>
    </div>
    <div class="mt-8 text-red-500" v-if="error">
      {{ error }}
    </div>
  </div>
</template>

<script setup lang="ts">
import * as mqtt from "mqtt/dist/mqtt.min";

const temperatureOne = ref("");
const temperatureTwo = ref("");
const error = ref<string | undefined>(undefined);

const url = "mqtt://172.20.199.158:9911";
const topic = "temperature1";
const topic2 = "temperature2";

const client = mqtt.connect("mqtt://172.20.199.158:9911", { protocol: "ws" });

client.on("connect", function (err1) {
  console.log("Verbunden mit MQTT-Broker");
  if (err1) {
    error.value = `Fehler beim Verbinden mit dem MQTT-Broker: ${err1}`;
    console.error(`Fehler beim Verbinden mit dem MQTT-Broker: ${err1}`);
  }

  // Dem Topic beitreten
  client.subscribe(topic, function (err) {
    if (!err) {
      console.log(`Erfolgreich dem Topic ${topic} beigetreten`);
    } else {
      error.value = `Fehler beim Beitritt zum Topic ${topic}: ${err}`;
      console.error(`Fehler beim Beitritt zum Topic ${topic}: ${err}`);
    }
  });

  // Dem Topic beitreten
  client.subscribe(topic2, function (err) {
    if (!err) {
      console.log(`Erfolgreich dem Topic ${topic} beigetreten`);
    } else {
      error.value = `Fehler beim Beitritt zum Topic ${topic}: ${err}`;
      console.error(`Fehler beim Beitritt zum Topic ${topic}: ${err}`);
    }
  });
});

// Handler für eingehende Nachrichten
client.on("message", function (topic, message) {
  if (topic === "temperature1") {
    temperatureOne.value = message.toString();
    error.value = undefined;
    console.log(`Neue Nachricht im Topic ${topic}: ${message.toString()}`);
  }
});

client.on("message", function (topic, message) {
  if (topic === "temperature2") {
    temperatureTwo.value = message.toString();
    error.value = undefined;
    console.log(`Neue Nachricht im Topic ${topic}: ${message.toString()}`);
  }
});
</script>
