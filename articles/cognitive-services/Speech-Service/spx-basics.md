---
title: Rychlý Start funkce Speech CLI – služba Speech
titleSuffix: Azure Cognitive Services
description: Začněte s Azure Speech CLI. Můžete pracovat se službami Speech, jako je řeč, převod textu na řeč a překlady řeči bez psaní kódu.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 53138a22c58e89ade4af234630e9429a19738a6a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556464"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Začínáme s rozhraním příkazového řádku Azure Speech

V tomto článku se dozvíte, jak používat rozpoznávání řeči, rozhraní příkazového řádku, abyste měli přístup ke službám Speech, jako je řeč, převod textu na řeč a překladu řeči bez psaní kódu. Rozhraní příkazového řádku pro rozpoznávání řeči je připravené pro práci a je možné ho použít k automatizaci jednoduchých pracovních postupů ve službě pro rozpoznávání řeči pomocí `.bat` skriptů nebo prostředí.

V tomto článku se předpokládá, že máte praktické znalosti z příkazového řádku, terminálu nebo PowerShellu.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Základní použití

V této části najdete několik základních příkazů SPX, které jsou často užitečné pro testování a experimentování při prvním spuštění. Začněte tím, že v nástroji spustíte následující příkaz, a to tak, že zobrazíte pomocníka s integrovaným nástrojem.

```console
spx
```

Témata nápovědy můžete hledat podle klíčového slova. Zadejte například následující příkaz, který zobrazí seznam příkladů použití funkce Speech CLI:

```console
spx help find --topics "examples"
```

Zadejte následující příkaz pro zobrazení možností příkazu rozpoznat:

```console
spx help recognize
```

Další příkazy Help uvedené v pravém sloupci Tyto příkazy můžete zadat, chcete-li získat podrobnou nápovědu o dílčích příkazech.

## <a name="speech-to-text-speech-recognition"></a>Převod řeči na text (rozpoznávání řeči)

Pojďme použít rozpoznávání řeči k převodu řeči na text (rozpoznávání řeči) pomocí výchozího mikrofonu vašeho systému. Po zadání příkazu zahájí SPX naslouchání zvuku na aktuálním aktivním vstupním zařízení a při stisknutí klávesy **ENTER** se zastaví. Zaznamenaný hlas se pak rozpozná a převede na text ve výstupu konzoly.

>[!IMPORTANT]
> Pokud používáte kontejner Docker, `--microphone` nebude fungovat.

Spusťte tento příkaz:

```console
spx recognize --microphone
```

Pomocí rozhraní příkazového řádku pro rozpoznávání řeči můžete také rozpoznávat řeč ze zvukového souboru.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Pokud rozpoznávání řeči rozpoznáváte ze zvukového souboru v kontejneru Docker, ujistěte se, že je zvukový soubor umístěný v adresáři, který jste připojili v předchozím kroku.

Nezapomeňte, že pokud se zablokuje nebo chcete získat další informace o možnostech rozpoznávání rozpoznávání řeči, stačí zadat:

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Převod textu na řeč (syntéza řeči)

Spuštěním následujícího příkazu převezmete text jako vstup a výstupem syntetizového rozpoznávání řeči na aktuální aktivní výstupní zařízení (například reproduktory počítače).

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Syntetizované výstupy můžete také uložit do souboru. V tomto příkladu vytvoříme soubor s názvem `my-sample.wav` v adresáři, ve kterém se příkaz spustí.

```console
spx synthesize --text "Enjoy using the Speech CLI." --audio output my-sample.wav
```

Tyto příklady předpokládají, že testujete v angličtině. Podporujeme ale syntézu řeči v mnoha jazycích. Pomocí tohoto příkazu můžete stáhnout úplný seznam hlasů nebo si můžete navštívit [stránku podpory jazyků](./language-support.md).

```console
spx synthesize --voices
```

Tady je postup, jak můžete použít některý z hlasů, které jste zjistili.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Nezapomeňte, že pokud se zablokuje nebo chcete získat další informace o možnostech syntézy pro rozpoznávání řeči, stačí zadat:

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Převod řeči na text

Pomocí rozhraní příkazového řádku pro rozpoznávání řeči můžete také převod řeči na text. Spuštěním tohoto příkazu zachytíte zvuk z výchozího mikrofonu a výstupem překladu jako text. Mějte na paměti, že je nutné `source` `target` pomocí příkazu předat jazyk a `translate` .

```console
spx translate --microphone --source en-US --target ru-RU
```

Při překladu do více jazyků, s použitím samostatných kódů jazyka `;` .

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Pokud chcete uložit výstup svého překladu, použijte `--output` příznak. V tomto příkladu si přečtete také ze souboru.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> Seznam všech podporovaných jazyků a jejich odpovídajících kódů národního prostředí najdete v článku věnovaném [jazykům a národním prostředím](language-support.md) .

Nezapomeňte, že pokud se zablokuje nebo chcete získat další informace o možnostech překladu rozhraní příkazového řádku, stačí zadat:

```console
spx help translate
```

## <a name="next-steps"></a>Další kroky

* [Možnosti konfigurace CLI služby Speech](./spx-data-store-configuration.md)
* [Operace dávkového zpracování pomocí funkce CLI](./spx-batch-operations.md)
