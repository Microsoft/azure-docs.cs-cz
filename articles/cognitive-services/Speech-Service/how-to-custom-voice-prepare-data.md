---
title: How to prepare data for Custom Voice - Speech Service
titleSuffix: Azure Cognitive Services
description: Create a custom voice for your brand with Speech Service. You provide studio recordings and the associated scripts, the service generates a unique voice model tuned to the recorded voice. Use this voice to synthesize speech in your products, tools, and applications.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: a954118cd0697213674bb9981f0d94100488fb38
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464503"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Prepare data to create a custom voice

When you're ready to create a custom text-to-speech voice for your application, the first step is to gather audio recordings and associated scripts to start training the voice model. The service uses this data to create a unique voice tuned to match the voice in the recordings. After you've trained the voice, you can start synthesizing speech in your applications.

You can start with a small amount of data to create a proof of concept. However, the more data that you provide, the more natural your custom voice will sound. Before you can train your own text-to-speech voice model, you'll need audio recordings and the associated text transcriptions. On this page, we'll review data types, how they are used, and how to manage each.

## <a name="data-types"></a>Typy dat

A voice training dataset includes audio recordings, and a text file with the associated transcriptions. Each audio file should contain a single utterance (a single sentence or a single turn for a dialog system), and be less than 15 seconds long.

In some cases, you may not have the right dataset ready and will want to test the custom voice training with available audio files, short or long, with or without transcripts. We provide tools (beta) to help you segment your audio into utterances and prepare transcripts using the [Batch Transcription API](batch-transcription.md).

This table lists data types and how each is used to create a custom text-to-speech voice model.

| Data type | Popis | When to use | Additional service required | Quantity for training a model | Locale(s) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Individual utterances + matching transcript** | A collection (.zip) of audio files (.wav) as individual utterances. Each audio file should be 15 seconds or less in length, paired with a formatted transcript (.txt). | Professional recordings with matching transcripts | Ready for training. | No hard requirement for en-US and zh-CN. More than 2,000+ distinct utterances for other locales. | [All Custom Voice locales](language-support.md#customization) |
| **Long audio + transcript (beta)** | A collection (.zip) of long, unsegmented audio files (longer than 20 seconds), paired with a transcript (.txt) that contains all spoken words. | You have audio files and matching transcripts, but they are not segmented into utterances. | Segmentation (using batch transcription).<br>Audio format transformation where required. | No hard requirement  | [All Custom Voice locales](language-support.md#customization) |
| **Audio only (beta)** | A collection (.zip) of audio files without a transcript. | You only have audio files available, without transcripts. | Segmentation + transcript generation (using batch transcription).<br>Audio format transformation where required.| No hard requirement | [All Custom Voice locales](language-support.md#customization) |

Files should be grouped by type into a dataset and uploaded as a zip file. Each dataset can only contain a single data type.

> [!NOTE]
> The maximum number of datasets allowed to be imported per subscription is 10 .zip files for free subscription (F0) users and 500 for standard subscription (S0) users.

## <a name="individual-utterances--matching-transcript"></a>Individual utterances + matching transcript

You can prepare recordings of individual utterances and the matching transcript in two ways. Either write a script and have it read by a voice talent or use publicly available audio and transcribe it to text. If you do the latter, edit disfluencies from the audio files, such as "um" and other filler sounds, stutters, mumbled words, or mispronunciations.

To produce a good voice font, create the recordings in a quiet room with a high-quality microphone. Consistent volume, speaking rate, speaking pitch, and expressive mannerisms of speech are essential.

> [!TIP]
> To create a voice for production use, we recommend you use a professional recording studio and voice talent. For more information, see [How to record voice samples for a custom voice](record-custom-voice-samples.md).

### <a name="audio-files"></a>Audio files

Each audio file should contain a single utterance (a single sentence or a single turn of a dialog system), less than 15 seconds long. All files must be in the same spoken language. Multi-language custom text-to-speech voices are not supported, with the exception of the Chinese-English bi-lingual. Each audio file must have a unique numeric filename with the filename extension .wav.

Při přípravě zvuku postupujte podle těchto pokynů.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (.wav), grouped into a .zip file |
| Sampling rate | At least 16,000 Hz |
| Sample format | PCM, 16-bit |
| Název souboru | Numeric, with .wav extension. No duplicate file names allowed. |
| Délka zvuku | Shorter than 15 seconds |
| Formát archivu | .zip |
| Maximální velikost archivu | 2048 MB |

> [!NOTE]
> .wav files with a sampling rate lower than 16,000 Hz will be rejected. If a .zip file contains .wav files with different sample rates, only those equal to or higher than 16,000 Hz will be imported. The portal currently imports .zip archives up to 200 MB. However, multiple archives can be uploaded.

### <a name="transcripts"></a>Přepisů

The transcription file is a plain text file. Use these guidelines to prepare your transcriptions.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | Plain text (.txt) |
| Formát kódování | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE, or UTF-16-BE. For zh-CN, ANSI/ASCII and UTF-8 encodings are not supported. |
| Počet promluv na řádek | **One** - Each line of the transcription file should contain the name of one of the audio files, followed by the corresponding transcription. Název souboru a přepis by měly být oddělené tabulátorem (\t). |
| Maximální velikost souboru | 2048 MB |

Below is an example of how the transcripts are organized utterance by utterance in one .txt file:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
It’s important that the transcripts are 100% accurate transcriptions of the corresponding audio. Errors in the transcripts will introduce quality loss during the training.

> [!TIP]
> When building production text-to-speech voices, select utterances (or write scripts) that take into account both phonetic coverage and efficiency. Having trouble getting the results you want? [Contact the Custom Voice](mailto:speechsupport@microsoft.com) team to find out more about having us consult.

## <a name="long-audio--transcript-beta"></a>Long audio + transcript (beta)

In some cases, you may not have segmented audio available. We provide a service (beta) through the custom voice portal to help you segment long audio files and create transcriptions. Keep in mind, this service will be charged toward your speech-to-text subscription usage.

> [!NOTE]
> Služba segmentace dlouhého zvuku bude využívat funkci dávkového přepisu pro převod řeči na text, která podporuje pouze uživatele se standardním předplatným (S0). During the processing of the segmentation, your audio files and the transcripts will also be sent to the Custom Speech service to refine the recognition model so the accuracy can be improved for your data. No data will be retained during this process. After the segmentation is done, only the utterances segmented and their mapping transcripts will be stored for your downloading and training.

### <a name="audio-files"></a>Audio files

Follow these guidelines when preparing audio for segmentation.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (. wav) se vzorkovací frekvencí alespoň 16 kHz-16 bitů v PCM nebo. mp3 s přenosovou rychlostí minimálně 256 KB/s, seskupenou do souboru. zip |
| Název souboru | Podporované znaky ASCII a Unicode. No duplicate names allowed. |
| Délka zvuku | Déle než 20 sekund |
| Formát archivu | .zip |
| Maximální velikost archivu | 2048 MB |

Všechny zvukové soubory by se měly seskupovat do souboru ZIP. Soubory. wav a soubory. mp3 můžete vložit do jednoho zvukového souboru ZIP. Můžete například nahrát soubor ZIP obsahující zvukový soubor s názvem "kingstory. wav", 45-Second-Long a jiný zvuk s názvem "queenstory. mp3", 200-Second-Long. Všechny soubory. mp3 se po zpracování transformují do formátu. wav.

### <a name="transcripts"></a>Přepisů

Přepisy musí být připravené ke specifikacím uvedeným v této tabulce. Každý zvukový soubor musí odpovídat přepisu.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | Prostý text (. txt) seskupený do souboru. zip |
| Název souboru | Použít stejný název jako shodný zvukový soubor |
| Formát kódování | Pouze UTF-8-BOM |
| Počet promluv na řádek | Bez omezení |
| Maximální velikost souboru | 2048 MB |

Všechny soubory přepisů v tomto datovém typu by se měly seskupovat do souboru ZIP. Například jste nahráli soubor ZIP obsahující zvukový soubor s názvem "kingstory. wav", 45 sekund dlouhý a druhý s názvem "queenstory. mp3", 200 sekund. Budete potřebovat nahrát další soubor zip, který obsahuje dva přepisy, jeden s názvem ' kingstory. txt ', druhý soubor ' queenstory. txt '. V každém souboru s prostým textem vám poskytneme úplný přepis pro odpovídající zvuk.

Po úspěšném nahrání datové sady vám pomůžeme segmentovat zvukový soubor na projevy na základě poskytnutého přepisu. Můžete kontrolovat segmentované projevy a vyhovující přepisy stažením datové sady. K segmentované projevy se automaticky přiřazují jedinečná ID. Je důležité, abyste se ujistili, že přepisy, které poskytnete, jsou 100% přesné. Chyby v přepisech můžou snížit přesnost během segmentace zvuku a dále zavádět ztráty kvality ve fázi školení, která přichází později.

## <a name="audio-only-beta"></a>Jenom zvuk (beta verze)

Pokud pro zvukové nahrávky nemáte nějaké přepisy, nahrajte data pomocí možnosti **jenom zvuk** . Náš systém vám může přispět k segmentování a přepisovatí vašich zvukových souborů. Mějte na paměti, že se tato služba bude počítat se svým používáním předplatného pro převod řeči na text.

Při přípravě zvuku postupujte podle těchto pokynů.

> [!NOTE]
> Služba segmentace dlouhého zvuku bude využívat funkci dávkového přepisu pro převod řeči na text, která podporuje pouze uživatele se standardním předplatným (S0).

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (. wav) se vzorkovací frekvencí alespoň 16 kHz-16 bitů v PCM nebo. mp3 s přenosovou rychlostí minimálně 256 KB/s, seskupenou do souboru. zip |
| Název souboru | Podporované znaky ASCII a Unicode. Není povolený žádný duplicitní název. |
| Délka zvuku | Déle než 20 sekund |
| Formát archivu | .zip |
| Maximální velikost archivu | 2048 MB |

Všechny zvukové soubory by se měly seskupovat do souboru ZIP. Po úspěšném nahrání datové sady vám pomůžeme segmentovat zvukový soubor na projevy v závislosti na naší službě pro přepis služby Speech Batch. K segmentované projevy se automaticky přiřazují jedinečná ID. Pomocí rozpoznávání řeči budou vygenerovány vyhovující přepisy. Všechny soubory. mp3 se po zpracování transformují do formátu. wav. Můžete kontrolovat segmentované projevy a vyhovující přepisy stažením datové sady.

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastního hlasu](how-to-custom-voice-create-voice.md)
- [Příručka: záznam ukázek hlasu](record-custom-voice-samples.md)
