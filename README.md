const { Client, GatewayIntentBits } = require("discord.js");
require("dotenv").config();

const client = new Client({
  intents: [GatewayIntentBits.Guilds]
});

const TOKEN = process.env.TOKEN;
const CANAL_NOMBRE = "general"; // Cambiá por el nombre de tu canal
const INTERVALO_MS = 20 * 60 * 1000; // 20 minutos

let cerrado = false;

client.once("ready", () => {
  console.log(`✅ Bot conectado como ${client.user.tag}`);
  alternarCanal();
  setInterval(alternarCanal, INTERVALO_MS);
});

async function alternarCanal() {
  try {
    const guild = client.guilds.cache.first();
    if (!guild) return console.log("❌ No se encontró el servidor.");
    const canal = guild.channels.cache.find(c => c.name === CANAL_NOMBRE);
    if (!canal) return console.log(`❌ No se encontró el canal "${CANAL_NOMBRE}".`);

    const everyone = guild.roles.everyone;
    if (!cerrado) {
      await canal.permissionOverwrites.edit(everyone, { SendMessages: false });
      console.log(`🔒 Canal "${CANAL_NOMBRE}" cerrado.`);
      cerrado = true;
    } else {
      await canal.permissionOverwrites.edit(everyone, { SendMessages: true });
      console.log(`🔓 Canal "${CANAL_NOMBRE}" abierto.`);
      cerrado = false;
    }
  } catch (error) {
    console.error("❗ Error:", error);
  }
}

client.login(TOKEN);
