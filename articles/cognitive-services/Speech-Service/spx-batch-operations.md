---
title: Rozpoznávání řeči – služba Batch Operations – Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se, jak pomocí rozhraní příkazového řádku (rozpoznávání řeči) Batch Speech to text (rozpoznávání řeči), dávkovat text na řeč (syntézu řeči).
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540054"
---
# <a name="speech-cli-batch-operations"></a>Dávkové operace funkce Speech CLI

Běžné úlohy při používání služeb Azure Speech Services jsou dávkové operace. V tomto článku se dozvíte, jak pomocí rozhraní příkazového řádku pro rozpoznávání řeči (rozpoznávání řeči) Batch Speech to text (rozpoznávání řeči), dávkovat text na řeč. Konkrétně se dozvíte, jak:

* Spuštění rozpoznávání řeči služby Batch v adresáři zvukových souborů
* Spuštění funkce Batch Speech syntézy pomocí iterace v `.tsv` souboru

## <a name="batch-speech-to-text-speech-recognition"></a>Dávkové zpracování řeči na text (rozpoznávání řeči)

Služba rozpoznávání řeči se často používá k rozpoznávání řeči ze zvukových souborů. V tomto příkladu se dozvíte, jak iterovat přes adresář pomocí rozpoznávání řeči a zachytit výstup rozpoznávání pro každý `.wav` soubor. `--files`Příznak se používá k nasměrování v adresáři, ve kterém jsou uložené zvukové soubory, a zástupný znak `*.wav` slouží k oznámení, že rozhraní příkazového řádku pro rozpoznávání řeči spustí rozpoznávání u všech souborů s příponou `.wav` . Výstup pro každý soubor pro rozpoznávání je zapsán jako hodnota oddělená tabulátorem v `speech_output.tsv` .

> [!NOTE]
> `--threads`Argument lze také použít v další části `spx synthesize` příkazů a dostupná vlákna budou záviset na procesoru a jeho aktuálním procentu zatížení.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Následuje příklad struktury výstupních souborů.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Dávkový převod textu na řeč (syntéza řeči)

Nejjednodušší způsob, jak spustit dávkový převod textu na řeč, je vytvořit nový `.tsv` soubor (s oddělovači) a použít ho `--foreach` v rozhraní PŘÍKAZového řádku pro rozpoznávání řeči. V `.tsv` tomto příkladu můžete vytvořit soubor pomocí oblíbeného textového editoru a zavolat ho `text_synthesis.tsv` :

>[!IMPORTANT]
> Když kopírujete obsah tohoto textového souboru, ujistěte se, **že v souboru není** mezera mezi umístěním souboru a textem. V některých případech se při kopírování obsahu z tohoto příkladu záložky převedou na mezery, které způsobí, že `spx` příkaz selže při spuštění.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Dále spustíte příkaz, který odkazuje na `text_synthesis.tsv` , provede shrnutí jednotlivých `text` polí a zapíše výsledek do odpovídající `audio.output` cesty jako `.wav` soubor.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Tento příkaz je ekvivalentem běhu `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **pro každý** záznam v `.tsv` souboru.

Poznamenejte si pár věcí:

* Záhlaví sloupců, `audio.output` a `text` odpovídají argumentům příkazového řádku `--audio output` a v `--text` uvedeném pořadí. Argumenty příkazového řádku s více částmi `--audio output` by měly být naformátovány v souboru bez mezer, žádné úvodní pomlčky a tečky oddělující řetězce, například `audio.output` . Všechny ostatní existující argumenty příkazového řádku lze přidat do souboru jako další sloupce pomocí tohoto modelu.
* Pokud je tento soubor formátován tímto způsobem, není nutné předávat žádné další argumenty `--foreach` .
* Jednotlivé hodnoty můžete oddělit `.tsv` pomocí **karty**.

Nicméně pokud máte `.tsv` soubor podobný následujícímu příkladu se záhlavími sloupců, která **neodpovídají** argumentům příkazového řádku:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Tyto názvy polí můžete přepsat na správné argumenty pomocí následující syntaxe ve `--foreach` volání. Toto je stejné volání jako výše.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Další kroky

* [Přehled CLI služby Speech](./spx-overview.md)
* [Rychlý Start pro Speech CLI](./spx-basics.md)
