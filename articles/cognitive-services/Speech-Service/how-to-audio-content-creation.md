---
title: Vytvoření zvukového obsahu – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Vytváření zvukového obsahu je online nástroj, který umožňuje přizpůsobit a vyladit výstup převodu textu na řeč od Microsoftu pro vaše aplikace a produkty.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 72fecbdc173a6174e54a28e48f983965f397ba6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86224583"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Vylepšení syntézy pomocí nástroje pro tvorbu zvukového obsahu

[Vytváření zvukového obsahu](https://aka.ms/audiocontentcreation) je online nástroj, který umožňuje přizpůsobit a vyladit výstup převodu textu na řeč od Microsoftu pro vaše aplikace a produkty. Pomocí tohoto nástroje můžete vyladit veřejné a vlastní hlasy pro přesnější přirozené výrazy a spravovat výstup v cloudu.

Nástroj pro vytváření zvukového obsahu je založený na [jazyce SSML (Speech syntézy)](speech-synthesis-markup.md). Pro zjednodušení přizpůsobení a optimalizace vám vytvoření zvukového obsahu umožňuje vizuálně kontrolovat výstupy textu na řeč v reálném čase.

Podívejte se na [video s kurzem](https://www.youtube.com/watch?v=O1wIJ7mts_w) vytvoření zvukového obsahu.

## <a name="how-does-it-work"></a>Jak to funguje?

Tento diagram znázorňuje kroky potřebné k vyladění výstupů převodu textu na řeč. Pomocí odkazů níže můžete získat další informace o jednotlivých krocích.

![Diagram kroků, které jsou potřebné k doladění výstupů převodu textu na řeč.](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [Nastavte si účet Azure a prostředek řeči](#set-up-your-azure-account-and-speech-resource) , abyste mohli začít.
2. Pomocí prostého textu nebo skriptů SSML [vytvořte soubor pro optimalizaci zvuku](#create-an-audio-tuning-file) .
3. Vyberte hlas a jazyk pro obsah vašeho skriptu. Vytváření zvukového obsahu zahrnuje všechny [hlasy od společnosti Microsoft pro převod textu na řeč](language-support.md#text-to-speech). Můžete použít standardní, neuronové nebo vlastní hlas.
   >[!NOTE]
   > Gated Access je k dispozici pro vlastní hlasy neuronové, díky kterým můžete vytvářet hlasy s vysokou definicí podobnou přirozenému zvuku řeči. Další podrobnosti najdete v tématu [proces uzavírání](https://aka.ms/ignite2019/speech/ethics).

4. Zkontrolujte výchozí výstup syntézy. Pak Vylepšete výstup úpravou výslovnosti, přerušení, sklonu, poměru, nevýbušnosti, stylu hlasu a dalších možností. Úplný seznam možností najdete v tématu [jazyk značek pro rozpoznávání řeči](speech-synthesis-markup.md). Tady je [video](https://youtu.be/mUvf2NbfuYU) , které ukazuje, jak vyladit výstup řeči pomocí vytváření zvukového obsahu. 
5. Uložte a [exportujte vyladěné zvuky](#export-tuned-audio). Když v systému uložíte stopu ladění, můžete pokračovat v práci a iterovat na výstupu. Až budete s výstupem spokojeni, můžete vytvořit úlohu vytvoření zvuku pomocí funkce exportu. Můžete sledovat stav úlohy exportu a stáhnout výstup pro použití s aplikacemi a produkty.

## <a name="set-up-your-azure-account-and-speech-resource"></a>Nastavení účtu Azure a prostředku pro rozpoznávání řeči

1. Pokud chcete pracovat se vytvářením zvukového obsahu, musíte mít účet Azure. Účet Azure můžete vytvořit pomocí svého účtu Microsoft. Při [nastavování účtu Azure](get-started.md#new-resource)postupujte podle těchto pokynů. 
2. [Vytvořte prostředek řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) k účtu Azure. Ujistěte se, že vaše cenová úroveň je nastavená na **S0**. Pokud používáte některý z hlasů neuronové, ujistěte se, že jste vytvořili prostředek v [podporované oblasti](regions.md#standard-and-neural-voices).
2. Po získání účtu Azure a prostředku pro rozpoznávání řeči můžete používat služby řeči a přistupovat k [vytváření zvukového obsahu](https://aka.ms/audiocontentcreation).
3. Vyberte prostředek pro rozpoznávání řeči, na kterém potřebujete pracovat. Tady můžete také vytvořit nový prostředek pro rozpoznávání řeči. 
4. Prostředek řeči můžete kdykoli upravit pomocí možnosti **Nastavení** , která se nachází v horním navigačním panelu.

## <a name="create-an-audio-tuning-file"></a>Vytvořit soubor pro optimalizaci zvukového souboru

Existují dva způsoby, jak získat obsah do nástroje pro tvorbu zvukového obsahu.

**Možnost 1:**

1. Kliknutím na **nový soubor** vytvořte nový soubor pro optimalizaci zvuku.
2. Zadejte nebo vložte obsah do okna pro úpravy. Znaky pro každý soubor jsou až 20 000. Pokud je váš skript delší než 20 000 znaků, můžete použít možnost 2 pro automatické rozdělení obsahu do více souborů. 
3. Nezapomeňte uložit.

**Možnost 2:**

1. Kliknutím na **Odeslat** naimportujete jeden nebo více textových souborů. Podporuje se prostý text i SSML.
2. Pokud je soubor skriptu delší než 20 000 znaků, rozdělte ho prosím podle odstavců, podle znaku nebo podle regulárních výrazů. 
3. Když nahráváte textové soubory, ujistěte se, že tento soubor splňuje tyto požadavky.

   | Vlastnost | Hodnota/poznámky |
   |----------|---------------|
   | Formát souboru | Prostý text (. txt)<br/> Text SSML (. txt)<br/> Soubory zip nejsou podporovány |
   | Formát kódování | UTF-8 |
   | Název souboru | Každý soubor musí mít jedinečný název. Duplicity nejsou podporovány. |
   | Délka textu | Textové soubory nesmí přesáhnout 20 000 znaků. |
   | Omezení SSML | Každý soubor SSML může obsahovat pouze jednu část SSML. |

### <a name="plain-text-example"></a>Příklad prostého textu

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Příklad textu SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportovat laděný zvuk

Po kontrole zvukového výstupu a splnění jeho optimalizace a úprav můžete zvuk exportovat.

1. Kliknutím na **exportovat** vytvořte úlohu vytváření zvuku. **Export do knihovny zvuku** se doporučuje, protože podporuje dlouhý zvukový výstup a plné prostředí pro výstup zvuku. Zvuk můžete také stáhnout přímo na místní disk, ale k dispozici je pouze prvních 10 minut. 
2. Vyberte formát výstupu pro vyladěný zvuk. Seznam podporovaných formátů a vzorkovacích sazeb je k dispozici níže.
3. Stav úlohy můžete zobrazit na kartě **úloha exportu** . Pokud se úloha nezdařila, přejděte na stránku podrobné informace pro celou sestavu.
4. Po dokončení úlohy je váš zvuk k dispozici ke stažení na kartě **Knihovna zvukového** prostředí.
5. Klikněte na tlačítko **Stáhnout**. Teď jste připraveni používat vlastní laděný zvuk ve vašich aplikacích nebo produktech.

### <a name="supported-audio-formats"></a>Podporované formáty zvuku

| Formát | vzorkovací frekvence 16 kHz | frekvence vzorků 24 kHz |
|--------|--------------------|--------------------|
| buď | RIFF-16khz-16bitový-mono-PCM | RIFF-24khz-16bitový-mono-PCM |
| MP3 | Audio-16khz-128kbitrate-mono-MP3 | Audio-24khz-160kbitrate-mono-MP3 |

## <a name="see-also"></a>Viz také

* [Rozhraní API pro dlouhé zvukové zařízení](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
