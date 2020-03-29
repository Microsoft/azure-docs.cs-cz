---
title: Tvorba zvukového obsahu - služba řeči
titleSuffix: Azure Cognitive Services
description: Vytváření zvukového obsahu je online nástroj, který umožňuje přizpůsobit a doladit výstup společnosti Microsoft pro převod textu na řeč pro vaše aplikace a produkty.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: ab0d2b8d95b4cb5996dd93fa0bb24085c9de26d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331532"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Zlepšete syntézu pomocí vytváření zvukového obsahu

[Vytváření zvukového obsahu](https://aka.ms/audiocontentcreation) je online nástroj, který umožňuje přizpůsobit a doladit výstup společnosti Microsoft pro převod textu na řeč pro vaše aplikace a produkty. Pomocí tohoto nástroje můžete doladit veřejné a vlastní hlasy pro přesnější přirozené výrazy a spravovat výstup v cloudu.

Nástroj pro vytváření zvukového obsahu je založen na [jazyce SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md) Pro zjednodušení přizpůsobení a ladění umožňuje vytváření zvukového obsahu vizuálně kontrolovat výstupy převodu textu na řeč v reálném čase.

## <a name="how-does-it-work"></a>Jak to funguje?

Tento diagram znázorňuje kroky, které je třeba vyladit a exportovat vlastní výstupy řeči na text. Další informace o jednotlivých krocích najdete v následujících odkazech.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Prvním krokem je [vytvoření účtu Azure, registrace prostředku řeči a získání klíče předplatného](#create-a-speech-resource). Po vytvoření klíče předplatného jej můžete použít k volání služby Řeč a k přístupu k [vytváření zvukového obsahu](https://aka.ms/audiocontentcreation).
2. [Vytvořte soubor optimalizace zvuku](#create-an-audio-tuning-file) pomocí prostého textu nebo SSML.
3. Vyberte hlas a jazyk, který chcete naladit. Vytváření zvukového obsahu zahrnuje všechny [hlasy společnosti Microsoft pro převod textu na řeč](language-support.md#text-to-speech). Můžete použít standardní, neurální, nebo svůj vlastní hlas.
   >[!NOTE]
   > Gated přístup je k dispozici pro vlastní neurální hlasy, které vám umožní vytvořit hlasy s vysokým rozlišením podobné přirozeně znějící řeči. Další podrobnosti naleznete v tématu [Gating process](https://aka.ms/ignite2019/speech/ethics).

4. Zkontrolujte výchozí výsledek. Pak pomocí nástroje pro ladění upravte výslovnost, výšku, rychlost, intonaci, hlasový styl a další. Úplný seznam možností naleznete v tématu [Speech Synthesis Markup Language](speech-synthesis-markup.md).
5. Uložte a [exportujte naladěný zvuk](#export-tuned-audio). Když uložíte ladicí stopu do systému, můžete pokračovat v práci a itéru na výstupu. Když jste s výstupem spokojeni, můžete pomocí funkce exportu vytvořit úlohu vytváření zvuku. Můžete sledovat stav úlohy exportu a stáhnout výstup pro použití s aplikacemi a produkty.
6. Posledním krokem je použití vlastního vyladěného hlasu ve vašich aplikacích a produktech.

## <a name="create-a-speech-resource"></a>Vytvoření prostředku řeči

Podle těchto kroků vytvořte prostředek řeči a propojte jej se studiem Speech Studio.

1. Podle těchto pokynů [se zaregistrujte k účtu Azure](get-started.md#new-resource) a [vytvořte prostředek řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource). Ujistěte se, že vaše cenová úroveň je nastavena na **S0**. Pokud používáte jeden z neurální hlasy, ujistěte se, že vytvoříte prostředek v [podporované oblasti](regions.md#standard-and-neural-voices).
2. Přihlaste se k [vytváření zvukového obsahu](https://aka.ms/audiocontentcreation).
3. Vyberte existující projekt nebo klepněte na **vytvořit nový**.
4. Předplatné můžete kdykoli upravit pomocí možnosti **Nastavení,** která se nachází v horní nánosy.

## <a name="create-an-audio-tuning-file"></a>Vytvoření souboru optimalizace zvuku

Existují dva způsoby, jak dostat obsah do nástroje pro vytváření zvukového obsahu.

**Možnost 1:**

1. Po přihlášení k [vytváření zvukového obsahu](https://aka.ms/audiocontentcreation)vytvořte nový soubor optimalizace zvuku klepnutím na **možnost Optimalizace zvuku.**
2. Po otevření okna pro úpravy můžete zadat až 10 000 znaků.
3. Nezapomeň zachránit.

**Možnost č. 2:**

1. Po přihlášení k [vytváření zvukového obsahu](https://aka.ms/audiocontentcreation)klepněte na **tlačítko Nahrát** a importujte jeden nebo více textových souborů. Je podporován prostý text i SSML.
2. Při nahrávání textových souborů se ujistěte, že obsah tyto požadavky splňuje.

   | Vlastnost | Hodnota / poznámky |
   |----------|---------------|
   | Formát souboru | Prostý text (.txt)<br/> Text SSML (.txt)<br/> Soubory ZIP nejsou podporovány. |
   | Formát kódování | UTF-8 |
   | Název souboru | Každý soubor musí mít jedinečný název. Duplikáty nejsou podporovány. |
   | Délka textu | Textové soubory nesmí přesáhnout 10 000 znaků. |
   | Omezení SSML | Každý soubor SSML může obsahovat pouze jeden kus SSML. |

### <a name="plain-text-example"></a>Příklad prostého textu

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Příklad textu SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Export vyladěného zvuku

Po kontrole zvukového výstupu a spokojenosti s laděním a úpravou můžete zvuk exportovat.

1. V nástroji [pro vytváření zvukového obsahu](https://aka.ms/audiocontentcreation) klikněte na **Exportovat** a vytvořte úlohu vytváření zvuku.
2. Zvolte výstupní formát naladěného zvuku. Seznam podporovaných formátů a vzorkovacích rychlostí je k dispozici níže.
3. Stav úkolu můžete zobrazit na kartě **Exportovat úkol.** Pokud se úkol nezdaří, podívejte se na stránku s podrobnými informacemi o úplné sestavě.
4. Po dokončení úkolu je zvuk k dispozici ke stažení na kartě **Knihovna zvuku.**
5. Klepněte na tlačítko **Stáhnout**. Nyní jste připraveni používat vlastní vyladěný zvuk ve svých aplikacích nebo produktech.

### <a name="supported-audio-formats"></a>Podporované zvukové formáty

| Formát | Vzorkovací frekvence 16 kHz | Vzorkovací frekvence 24 kHz |
|--------|--------------------|--------------------|
| Wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| Mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Viz také

* [Dlouhé zvukové rozhraní API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Řečové studio](https://speech.microsoft.com)
