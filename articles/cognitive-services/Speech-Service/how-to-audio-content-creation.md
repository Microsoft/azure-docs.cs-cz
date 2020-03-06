---
title: Vytvoření zvukového obsahu – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Vytváření zvukového obsahu je online nástroj, který umožňuje přizpůsobit a vyladit výstup převodu textu na řeč od Microsoftu pro vaše aplikace a produkty.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: ab0d2b8d95b4cb5996dd93fa0bb24085c9de26d5
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331532"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Vylepšení syntézy pomocí vytváření zvukového obsahu

[Vytváření zvukového obsahu](https://aka.ms/audiocontentcreation) je online nástroj, který umožňuje přizpůsobit a vyladit výstup převodu textu na řeč od Microsoftu pro vaše aplikace a produkty. Pomocí tohoto nástroje můžete vyladit veřejné a vlastní hlasy pro přesnější přirozené výrazy a spravovat výstup v cloudu.

Nástroj pro vytváření zvukového obsahu je založený na [jazyce SSML (Speech syntézy)](speech-synthesis-markup.md). Pro zjednodušení přizpůsobení a optimalizace vám vytvoření zvukového obsahu umožňuje vizuálně kontrolovat výstupy textu na řeč v reálném čase.

## <a name="how-does-it-work"></a>Jak to funguje?

Tento diagram znázorňuje kroky potřebné k vyladění a exportu přizpůsobených výstupů řeči na text. Pomocí odkazů níže můžete získat další informace o jednotlivých krocích.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Prvním krokem je [Vytvoření účtu Azure, registrace prostředku řeči a získání klíče předplatného](#create-a-speech-resource). Jakmile budete mít klíč předplatného, můžete ho použít k volání služby pro rozpoznávání řeči a k [Vytvoření zvukového obsahu](https://aka.ms/audiocontentcreation).
2. [Vytvořte soubor pro optimalizaci zvuku](#create-an-audio-tuning-file) pomocí prostého textu nebo SSML.
3. Vyberte hlas a jazyk, který chcete ladit. Vytváření zvukového obsahu zahrnuje všechny [hlasy od společnosti Microsoft pro převod textu na řeč](language-support.md#text-to-speech). Můžete použít standardní, neuronové nebo vlastní hlas.
   >[!NOTE]
   > Gated Access je k dispozici pro vlastní hlasy neuronové, díky kterým můžete vytvářet hlasy s vysokou definicí podobnou přirozenému zvuku řeči. Další podrobnosti najdete v tématu [proces uzavírání](https://aka.ms/ignite2019/speech/ethics).

4. Zkontrolujte výchozí výsledek. Pak pomocí nástroje pro vyladění upravte výslovnost, rozteč, rychlost, výbuchy, styl hlasu a další. Úplný seznam možností najdete v tématu [jazyk značek pro rozpoznávání řeči](speech-synthesis-markup.md).
5. Uložte a [exportujte vyladěné zvuky](#export-tuned-audio). Když v systému uložíte stopu ladění, můžete pokračovat v práci a iterovat na výstupu. Až budete s výstupem spokojeni, můžete vytvořit úlohu vytvoření zvuku pomocí funkce exportu. Můžete sledovat stav úlohy exportu a stáhnout výstup pro použití s aplikacemi a produkty.
6. Posledním krokem je použití vlastního laděného hlasu v aplikacích a produktech.

## <a name="create-a-speech-resource"></a>Vytvoření prostředku pro rozpoznávání řeči

Pomocí těchto kroků vytvořte prostředek pro rozpoznávání řeči a připojte ho ke službě Speech Studio.

1. Podle těchto pokynů si můžete [zaregistrovat účet Azure](get-started.md#new-resource) a [vytvořit prostředek pro rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource). Ujistěte se, že vaše cenová úroveň je nastavená na **S0**. Pokud používáte některý z hlasů neuronové, ujistěte se, že jste vytvořili prostředek v [podporované oblasti](regions.md#standard-and-neural-voices).
2. Přihlaste se k [vytváření zvukového obsahu](https://aka.ms/audiocontentcreation).
3. Vyberte existující projekt nebo klikněte na **vytvořit nový**.
4. Předplatné můžete kdykoli upravit pomocí možnosti **Nastavení** , která se nachází v horním navigačním panelu.

## <a name="create-an-audio-tuning-file"></a>Vytvořit soubor pro optimalizaci zvukového souboru

Existují dva způsoby, jak získat obsah do nástroje pro tvorbu zvukového obsahu.

**Možnost 1:**

1. Až se přihlásíte k [Vytvoření zvukového obsahu](https://aka.ms/audiocontentcreation), klikněte na **ladění zvuku** a vytvořte nový soubor pro optimalizaci zvuku.
2. Až se zobrazí okno pro úpravy, můžete zadat až 10 000 znaků.
3. Nezapomeňte uložit.

**Možnost 2:**

1. Až se přihlásíte k [Vytvoření zvukového obsahu](https://aka.ms/audiocontentcreation), klikněte na **nahrát** a naimportujte jeden nebo víc textových souborů. Podporuje se prostý text i SSML.
2. Když nahráváte textové soubory, ujistěte se, že obsah splňuje tyto požadavky.

   | Vlastnost | Hodnota/poznámky |
   |----------|---------------|
   | Formát souboru | Prostý text (. txt)<br/> Text SSML (. txt)<br/> Soubory zip nejsou podporovány |
   | Formát kódování | UTF-8 |
   | Název souboru | Každý soubor musí mít jedinečný název. Duplicity nejsou podporovány. |
   | Délka textu | Textové soubory nesmí přesáhnout 10 000 znaků. |
   | Omezení SSML | Každý soubor SSML může obsahovat pouze jednu část SSML. |

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

## <a name="export-tuned-audio"></a>Exportovat laděný zvuk

Po kontrole zvukového výstupu a splnění jeho optimalizace a úprav můžete zvuk exportovat.

1. V nástroji pro [tvorbu zvukového obsahu](https://aka.ms/audiocontentcreation) klikněte na **exportovat** a vytvořte úlohu vytváření zvuku.
2. Vyberte formát výstupu pro vyladěný zvuk. Seznam podporovaných formátů a vzorkovacích sazeb je k dispozici níže.
3. Stav úlohy můžete zobrazit na kartě **úloha exportu** . Pokud se úloha nezdařila, přejděte na stránku podrobné informace pro celou sestavu.
4. Po dokončení úlohy je váš zvuk k dispozici ke stažení na kartě **Knihovna zvukového** prostředí.
5. Klikněte na **Stáhnout**. Teď jste připraveni používat vlastní laděný zvuk ve vašich aplikacích nebo produktech.

### <a name="supported-audio-formats"></a>Podporované formáty zvuku

| Formát | vzorkovací frekvence 16 kHz | frekvence vzorků 24 kHz |
|--------|--------------------|--------------------|
| buď | RIFF-16khz-16bitový-mono-PCM | RIFF-24khz-16bitový-mono-PCM |
| MP3 | Audio-16khz-128kbitrate-mono-MP3 | Audio-24khz-160kbitrate-mono-MP3 |

## <a name="see-also"></a>Viz také

* [Rozhraní API pro dlouhé zvukové zařízení](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Studio Speech](https://speech.microsoft.com)
