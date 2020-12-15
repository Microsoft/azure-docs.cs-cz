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
ms.openlocfilehash: 1b92d1b5853d6b794ebdcf0e2052b8f15081d608
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507570"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Seznamte se se základy rozpoznávání řeči v rozhraní příkazového řádku

V tomto článku se seznámíte se základními vzory používání funkce Speech CLI, což je nástroj příkazového řádku pro použití služby Speech bez psaní kódu. Můžete rychle otestovat hlavní funkce služby Speech, aniž byste museli vytvářet vývojová prostředí nebo psát kód, abyste zjistili, jestli je možné vaše případy použití vhodně splnit. Rozhraní příkazového řádku pro rozpoznávání řeči je připravené pro práci a je možné ho použít k automatizaci jednoduchých pracovních postupů ve službě pro rozpoznávání řeči pomocí `.bat` skriptů nebo prostředí.

V tomto článku se předpokládá, že máte praktické znalosti z příkazového řádku, terminálu nebo PowerShellu.

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

Teď použijeme rozpoznávání řeči k rozpoznávání řeči pomocí výchozího mikrofonu vašeho systému. 

>[!WARNING]
> Pokud používáte kontejner Docker, tento příkaz nebude fungovat.

Spusťte tento příkaz:

```shell
spx recognize --microphone
```

Pomocí rozhraní příkazového řádku pro rozpoznávání řeči můžete také rozpoznávat řeč ze zvukového souboru.

```shell
spx recognize --file /path/to/file.wav
```
> [!TIP]
> Pokud rozpoznávání řeči rozpoznáváte ze zvukového souboru v kontejneru Docker, ujistěte se, že je zvukový soubor umístěný v adresáři, který jste připojili v předchozím kroku.

Po zadání příkazu zahájí SPX naslouchání zvuku na aktuálním aktivním vstupním zařízení a zastaví se po stisknutí klávesy `ENTER` . Zaznamenaný hlas se pak rozpozná a převede na text ve výstupu konzoly. Syntéza textu na řeč je také snadné používat rozhraní příkazového řádku pro rozpoznávání řeči. 

Spuštěním následujícího příkazu přejdete do zadaného textu jako vstup a výstupem syntetizového rozpoznávání řeči na aktuální aktivní výstupní zařízení.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Kromě rozpoznávání řeči a syntézy můžete také překlad řeči provést pomocí rozhraní příkazového řádku pro rozpoznávání řeči. Podobně jako u příkazu pro rozpoznávání řeči výše spusťte následující příkaz, který zachytí zvuk z výchozího mikrofonu a provede převod na text v cílovém jazyce.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

V tomto příkazu zadáte jak zdroj (jazyk pro překlad) **, tak i** cíl (jazyk **pro překlad)**. Použití `--microphone` argumentu naposlouchá zvuk na aktuálním aktivním vstupním zařízení a zastaví se po stisknutí klávesy `ENTER` . Výstupem je textový překlad do cílového jazyka zapsaný do textového souboru.

> [!NOTE]
> Seznam všech podporovaných jazyků a jejich odpovídajících kódů národního prostředí najdete v článku věnovaném [jazykům a národním prostředím](language-support.md) .

### <a name="configuration-files-in-the-datastore"></a>Konfigurační soubory v úložišti dat

Chování rozhraní příkazového řádku pro rozpoznávání řeči se může spoléhat na nastavení v konfiguračních souborech, které můžete v rámci volání rozhraní příkazového řádku CLI použít \@ symbol.
Funkce Speech CLI uloží nové nastavení do nového `./spx/data` podadresáře, který vytvoří v aktuálním pracovním adresáři.
Při hledání hodnoty konfigurace vyhledá funkce rozpoznávání řeči v aktuálním pracovním adresáři, pak v úložišti dat v `./spx/data` a v dalších dataobchodech, včetně konečného úložiště dat jen pro čtení v `spx` binárním souboru.
Dřív jste používali úložiště dat `@key` a uložili `@region` hodnoty a, takže jste je nemuseli zadávat při každém volání příkazového řádku.
Můžete také použít konfigurační soubory k uložení vlastních nastavení konfigurace nebo je dokonce použít k předávání adres URL nebo jiného dynamického obsahu vygenerovaného za běhu.

V této části se dozvíte, jak pomocí konfiguračního souboru v místním úložišti dat ukládat a načítat nastavení příkazů pomocí `spx config` a jak uložit výstup z rozhraní příkazového řádku pro rozpoznávání řeči pomocí `--output` Možnosti.

Následující příklad vymaže `@my.defaults` konfigurační soubor, přidá páry klíč-hodnota pro **klíč** a **oblast** v souboru a použije konfiguraci při volání `spx recognize` .

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Můžete také zapisovat dynamický obsah do konfiguračního souboru. Například následující příkaz vytvoří vlastní model řeči a uloží adresu URL nového modelu do konfiguračního souboru. Následující příkaz počká, dokud model na této adrese URL není připravený k použití, než se vrátí.

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

Následující příklad zapíše dvě adresy URL do `@my.datasets.txt` konfiguračního souboru.
V tomto scénáři `--output` může zahrnovat volitelné klíčové slovo **Add** pro vytvoření konfiguračního souboru nebo připojení k existujícímu.


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Další podrobnosti o souborech úložiště dat, včetně použití výchozích konfiguračních souborů ( `@spx.default` , `@default.config` a `@*.default.config` pro výchozí nastavení příkazu), získáte, když zadáte tento příkaz:

```shell
spx help advanced setup
```

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

## <a name="synthesize-speech-to-a-file"></a>Vysyntetizátorování řeči v souboru

Spuštěním následujícího příkazu změňte výstup z vašeho mluvčího na `.wav` soubor.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Rozhraní příkazového řádku pro rozpoznávání řeči vytvoří v angličtině přirozený jazyk `greetings.wav` .
V systému Windows můžete přehrát zvukový soubor zadáním `start greetings.wav` .


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

* Dokončete rychlé zprovoznění [rozpoznávání řeči](get-started-speech-to-text.md?pivots=programmer-tool-spx) nebo [řeči](get-started-text-to-speech.md?pivots=programmer-tool-spx) pomocí funkce CLI.
