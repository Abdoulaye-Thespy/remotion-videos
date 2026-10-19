# Remotion video

<p align="center">
  <a href="https://github.com/remotion-dev/logo">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://github.com/remotion-dev/logo/raw/main/animated-logo-banner-dark.apng">
      <img alt="Animated Remotion Logo" src="https://github.com/remotion-dev/logo/raw/main/animated-logo-banner-light.gif">
    </picture>
  </a>
</p>

Welcome to your Remotion project!

## Commands

**Install Dependencies**

```console
npm i --loglevel=error
```

**Start Preview**

```console
npm run dev
```

**Render video**

```console
npx remotion render
```

## Générer les voix off

Les textes à lire sont dans [`voiceover-fr.md`](./voiceover-fr.md). La scène 1 ne contient que la musique; la narration commence en scène 2 à 00:12. La première ligne du tableau correspond à la scène 2. Pour générer la scène `N` (de 2 à 16), prendre la ligne `N - 1` du tableau (par exemple, la scène 7 utilise la ligne `00:42–00:48`) et enregistrer le MP3 sous `public/voiceover/scene-NN.mp3`.

Les voix du projet sont générées avec Node et Edge TTS, en français avec la voix **Henri** (`fr-FR-HenriNeural`). Réglages habituels : débit `10%`, format `AUDIO_24KHZ_96KBITRATE_MONO_MP3`, durée maximale de 6 secondes par scène. Si une phrase dépasse six secondes, augmenter son débit jusqu’à ce qu’elle tienne dans sa scène (la scène 10 utilise `35%`). Le paquet utilisé est `@andresaya/edge-tts` (installation temporaire : `npm install --prefix /tmp/freelanceconnect-tts @andresaya/edge-tts`).

Exemple de génération pour une scène ; remplacer `7` par le numéro voulu :

```js
const { EdgeTTS, Constants } = require("/tmp/freelanceconnect-tts/node_modules/@andresaya/edge-tts");
const { readFile, writeFile } = require("node:fs/promises");

(async () => {
  const markdown = await readFile("voiceover-fr.md", "utf8");
  const lines = markdown
    .split(/\r?\n/)
    .map((row) => row.match(/^\|\s*\d{2}:\d{2}[–-]\d{2}:\d{2}\s*\|\s*(.*?)\s*\|$/)?.[1])
    .filter(Boolean);
  const scene = 7; // la scène 1 contient uniquement la musique
  const text = lines[scene - 2]; // la première ligne correspond à la scène 2
  const tts = new EdgeTTS({ timeout: 90000 });
  const result = await tts.generate(text, "fr-FR-HenriNeural", {
    rate: scene === 10 ? "35%" : "10%",
    outputFormat: Constants.OUTPUT_FORMAT.AUDIO_24KHZ_96KBITRATE_MONO_MP3,
  });
  if (result.duration > 6) throw new Error(`Scène ${scene} trop longue : ${result.duration.toFixed(2)} s`);
  await writeFile(`public/voiceover/scene-${String(scene).padStart(2, "0")}.mp3`, result.audio);
  console.log(`Scène ${scene} : ${result.duration.toFixed(2)} s`);
})().catch((error) => {
  console.error(error);
  process.exit(1);
});
```

**Upgrade Remotion**

```console
npx remotion upgrade
```

## Docs

Get started with Remotion by reading the [fundamentals page](https://www.remotion.dev/docs/the-fundamentals).

## Help

We provide help on our [Discord server](https://discord.gg/6VzzNDwUwV).

## Issues

Found an issue with Remotion? [File an issue here](https://github.com/remotion-dev/remotion/issues/new).

## License

Note that for some entities a company license is needed. [Read the terms here](https://github.com/remotion-dev/remotion/blob/main/LICENSE.md).
