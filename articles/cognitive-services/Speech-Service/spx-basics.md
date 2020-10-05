---
title: Základy rozpoznávání řeči pro rozhraní příkazového řádku
titleSuffix: Azure Cognitive Services
description: Naučte se používat příkazový nástroj pro rozpoznávání řeči pro práci se službou Speech bez kódu a minimálním nastavením.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e859ac13c72ed07d3f57da6e61fd6d9f827f0fca
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88854902"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Seznamte se se základy rozpoznávání řeči v rozhraní příkazového řádku

V tomto článku se seznámíte se základními vzory používání funkce Speech CLI, což je nástroj příkazového řádku pro použití služby Speech bez psaní kódu. Můžete rychle otestovat hlavní funkce služby Speech, aniž byste museli vytvářet vývojová prostředí nebo psát kód, abyste zjistili, jestli je možné vaše případy použití vhodně splnit. Kromě toho je prostředí příkazového řádku pro rozpoznávání řeči připravené na produkční prostředí a je možné ho použít k automatizaci jednoduchých pracovních postupů ve službě pro rozpoznávání řeči pomocí `.bat` skriptů nebo prostředí.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Základní použití

V této části najdete několik základních příkazů SPX, které jsou často užitečné pro testování a experimentování při prvním spuštění. Začněte tím, že v nástroji spustíte následující příkaz, a to tak, že zobrazíte pomocníka s integrovaným nástrojem.

```shell
spx
```

Všimněte si **:** témata nápovědy uvedená vpravo od parametrů příkazu. Tyto příkazy můžete zadat, chcete-li získat podrobnou nápovědu k dílčím příkazům.

Témata nápovědy můžete hledat podle klíčového slova. Zadejte například následující příkaz, který zobrazí seznam příkladů použití funkce Speech CLI:

```shell
spx help find --topics "examples"
```

Zadejte následující příkaz pro zobrazení možností příkazu rozpoznat:

```shell
spx help recognize
```

Teď použijte službu Speech k provedení některých funkcí rozpoznávání řeči pomocí výchozího mikrofonu, a to spuštěním následujícího příkazu.

```shell
spx recognize --microphone
```

Po zadání příkazu zahájí SPX naslouchání zvuku na aktuálním aktivním vstupním zařízení a zastaví se po stisknutí klávesy `ENTER` . Zaznamenaný hlas se pak rozpozná a převede na text ve výstupu konzoly. Syntéza textu na řeč je také snadné používat rozhraní příkazového řádku pro rozpoznávání řeči. 

Spuštěním následujícího příkazu přejdete do zadaného textu jako vstup a výstupem syntetizového rozpoznávání řeči na aktuální aktivní výstupní zařízení.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Kromě rozpoznávání řeči a syntézy můžete také překlad řeči provést pomocí rozhraní příkazového řádku pro rozpoznávání řeči. Podobně jako u příkazu pro rozpoznávání řeči výše spusťte následující příkaz, který zachytí zvuk z výchozího mikrofonu a provede převod na text v cílovém jazyce.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

V tomto příkazu zadáte jak zdroj (jazyk pro překlad) **, tak i**cíl (jazyk **pro překlad)**. Použití `--microphone` argumentu naposlouchá zvuk na aktuálním aktivním vstupním zařízení a zastaví se po stisknutí klávesy `ENTER` . Výstupem je textový překlad do cílového jazyka zapsaný do textového souboru.

> [!NOTE]
> Seznam všech podporovaných jazyků a jejich odpovídajících kódů národního prostředí najdete v článku věnovaném [jazykům a národním prostředím](language-support.md) .

## <a name="batch-operations"></a>Dávkové operace

Příkazy v předchozí části jsou skvělé pro rychlé zobrazení fungování služby Speech. Pokud však vyhodnotit, jestli se můžou vaše případy použití splnit, budete pravděpodobně muset provádět dávkové operace s vámi již existujícím rozsahem vstupu, abyste viděli, jak služba zpracovává nejrůznější scénáře. V této části se dozvíte, jak:

* Spuštění rozpoznávání řeči služby Batch v adresáři zvukových souborů
* Iterování prostřednictvím `.tsv` souboru a spuštění syntézy textu na řeč v dávce

## <a name="batch-speech-recognition"></a>Rozpoznávání řeči Batch

Pokud máte adresář se zvukovými soubory, je k dispozici pro rychlé rozpoznávání řeči pomocí funkce Speech CLI. Jednoduše spusťte následující příkaz, přejděte do adresáře pomocí `--files` příkazu. V tomto příkladu se připojíte `\*.wav` k adresáři, abyste porozpoznali všechny `.wav` soubory přítomné v adresáři dir. Kromě toho zadejte `--threads` argument pro spuštění rozpoznávání na 10 paralelních vláknech.

> [!NOTE]
> `--threads`Argument lze také použít v další části `spx synthesize` příkazů a dostupná vlákna budou záviset na procesoru a jeho aktuálním procentu zatížení.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Rozpoznaný výstup řeči je zapsán k `speech_output.tsv` použití `--output file` argumentu. Následuje příklad struktury výstupních souborů.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>Syntéza textu na řeč dávky

Nejjednodušší způsob, jak spustit dávkový převod textu na řeč, je vytvořit nový `.tsv` soubor (s oddělovači) a použít ho `--foreach` v rozhraní PŘÍKAZového řádku pro rozpoznávání řeči. Vezměte v úvahu následující soubor `text_synthesis.tsv` :

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Dále spustíte příkaz, který odkazuje na `text_synthesis.tsv` , provede shrnutí jednotlivých `text` polí a zapíše výsledek do odpovídající `audio.output` cesty jako `.wav` soubor. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Tento příkaz je ekvivalentem běhu `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **pro každý** záznam v `.tsv` souboru. Poznamenejte si pár věcí:

* Záhlaví sloupců `audio.output` a, které `text` odpovídají argumentům příkazového řádku `--audio output` a v `--text` uvedeném pořadí. Argumenty příkazového řádku s více částmi `--audio output` by měly být naformátovány v souboru bez mezer, žádné úvodní pomlčky a tečky oddělující řetězce, např. `audio.output` . Všechny ostatní existující argumenty příkazového řádku lze přidat do souboru jako další sloupce pomocí tohoto modelu.
* Pokud je tento soubor formátován tímto způsobem, není nutné předávat žádné další argumenty `--foreach` .
* Jednotlivé hodnoty můžete oddělit `.tsv` pomocí **karty**.

Nicméně pokud máte `.tsv` soubor podobný následujícímu příkladu se záhlavími sloupců, která **neodpovídají** argumentům příkazového řádku:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

Tyto názvy polí můžete přepsat na správné argumenty pomocí následující syntaxe ve `--foreach` volání. Toto je stejné volání jako výše.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Další kroky

* Dokončete rychlé zprovoznění [rozpoznávání řeči](./quickstarts/speech-to-text-from-microphone.md) nebo [řeči](./quickstarts/text-to-speech.md) pomocí sady SDK.
