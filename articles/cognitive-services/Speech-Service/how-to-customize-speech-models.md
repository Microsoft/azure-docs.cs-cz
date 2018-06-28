---
title: Přizpůsobení rozpoznávání řeči na Text modely | Microsoft Docs
description: Vylepšit rozpoznávání řeči přizpůsobení rozpoznávání řeči na Text modely.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 4d6c819f03034703314593f3e19392a580036039
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029141"
---
# <a name="customize-speech-to-text-models-using-speech-service"></a>Přizpůsobit pomocí služby řeči modely "Řeči na Text"

Služba rozpoznávání řeči můžete dosáhnout lepší výsledky rozpoznávání řeči, umožňuje přizpůsobit tři modely používané **řeči na Text** rozhraní API. Modely jsou automaticky Trénink probíhal vzorová data, které zadáte.

| Model | Ukázková data | Účel |
|-------|---------------|---------|
| Akustickými modelu      | Rozpoznávání řeči, text | Přizpůsobte zvuků (fonémy) spojené s konkrétní slova. Cvičení nové zvýraznění nebo dialektu, hovořícího prostředí atd. |
| Jazyk modelu      | Text | Přizpůsobte slova, která se ví, služby a jak se používají v utterances. Přidejte technické podmínky, místní místo názvy atd. |
| Výslovnosti | Text | Zlepšení rozpoznávání problémových slova, sloučeniny a zkratky. Například rozpoznat jako "C3PO" mapování "viz threepio" |

Po vytvoření nové modely, vytvořte vlastní koncový bod, který používá model pro jeden nebo více z výše uvedených účelů. Můžete také zvolit, že základní model poskytovaný službou řeči Pokud chcete použít, například vlastní akustickými model a model standardní jazyk. Pak použijete vlastní koncový bod místo standardní koncový bod pro požadavky na REST. Každý koncový bod má přidruženou *ID nasazení* tak, aby se dá použít s řeči SDK.

Přizpůsobení všechny modely, které se provádí prostřednictvím [vlastní řeči portál](https://www.cris.ai/).

## <a name="language-support"></a>Podpora jazyků

Jsou podporovány následující jazyky pro vlastní **řeči na Text** modely jazyk.

| Kód | Jazyk |
|-|-|
| cs-CZ | Angličtina (Spojené státy) 
| zh-CN | Čínština 
| SP SP | Španělština (Španělsko) 
| fr-FR | Francouzština (Francie) 
| IT-IT | italština 
| de-DE | Němčina
| pt-BR | Portugalština (Brazílie)
| ru-RU | ruština
| JP-JP | Japonština
| ar např | arabština (Egypt)

Vlastní akustickými modely podporují pouze Angličtina (USA) (en US). Čínština akustickými sady dat však lze importovat pro testování modely čínštinu.

Vlastní výslovnosti podporuje pouze Angličtina (USA) (en US) a němčina (de-DE) v tuto chvíli.

## <a name="prepare-data-sets"></a>Příprava sady dat

Každý typ modelu vyžaduje mírně odlišné datové a formátování, jak je popsáno v tomto poli.

| Model | Co poskytujete      |
|-------|-----------------------|
| Akustickými | Soubor ZIP obsahující zvukové soubory dokončení utterances a textového souboru obsahujícího transcriptions těchto souborů. Každý řádek souboru musí obsahovat název souboru, na kartě (ASCII 9) a text.|
| Jazyk | Textový soubor obsahující jeden utterance na každém řádku. |
| Výslovnosti | Textový soubor obsahující nápovědu výslovnosti na každém řádku. Každý pomocný parametr je zobrazení formuláře (slovo nebo zkratka), za nímž následuje karta (ASCII 9) a oznamována formuláře (požadované výslovnosti).  |

Postupujte podle textových souborů [text přepis pokyny](prepare-transcription.md) pro jazyk modelu.

## <a name="prepare-audio-files"></a>Příprava zvukových souborů

Zvukové soubory pro akustickými modely se zaznamená v typickém umístění, řadu zástupci uživatelů (Pokud je vaším cílem je za účelem optimalizace rozpoznávání pro jeden mluvčího), pomocí podobná mají vaši uživatelé mikrofonu. Požadovaný formát všechny zvuk vzorků, které je popsané v této tabulce.

| Vlastnost | Požadovaná hodnota |
|----------|------|
Formát souboru | RIFF (WAV)
Vzorkovací frekvence | 8000 Hz nebo 16 000 Hz
Kanály | 1 (mono)
Ukázka formátu | PCM 16bitové celé číslo
Doba trvání souboru | Od 0,1 do 60 sekund
Ticho kroužku | 0,1 sekundy
Formát archivu | ZIP
Archiv maximální velikost | 2 GB

Pokud jste školení model pro práci v pozadí aktivní, například objekt pro vytváření nebo automobilu, zahrnují několik sekund typické pozadí hluku na začátku nebo konci některé ukázky. Nezahrnujte jen šumu ukázky.

## <a name="upload-data-sets"></a>Nahrání datové sady

Chcete-li vytvořit vlastní model, nejprve nahrát data a pak zahájit proces školení.

1.  Přihlaste se k [vlastní řeči portál](https://www.cris.ai/).

1.  Vyberte typ datové sady, kterou chcete vytvořit z **vlastní řeči** nabídky - přizpůsobení dat pro akustickými modely, jazyk Data pro modely jazyk nebo výslovnosti. Zobrazí se seznam stávající sady dat daného typu (pokud existuje).

1. Zvolte jazyk, kliknutím na **změnu národního prostředí**.

1.  Klikněte na tlačítko **importovat nové** a zadejte název a popis nové datové sady.

1. Vyberte datové soubory, které jste připravili.

1. Klikněte na tlačítko **Import** nahrát data a spustíte ověření. Ověření zajišťuje, aby všechny soubory ve správném formátu. Ověření operace může chvíli trvat.

## <a name="create-a-model"></a>Vytvoření modelu

 Po ověření datové sady můžete takto trénování modelu.

> [!NOTE]
> Není nutné data výslovnosti na školení.

1. Zvolte typ modelu, kterou vytváříte z **vlastní řeči** nabídky, pak klikněte na tlačítko **vytvořit nový.**

1. Vyberte národní prostředí pro model.

1. Vyberte základní model pro nový model. Výběr základní model určí režimů rozpoznávání, ke kterým může být použit modelu, a také slouží jako záložní pro všechna data, není v datové sadě.

1.  Vyberte sadu dat, ze kterého má být vytvořen modelu. Datové sady, může být používán libovolný počet modelů.

1. Klikněte na tlačítko **vytvořit** zahájíte cvičení nový model.

## <a name="test-a-model"></a>Testování modelu

Jako součást procesu vytváření modelu můžete otestovat váš model proti akustickými datové sady. Nový model se používá k rozpoznávat řeč v datové sadě zvukových souborů a výsledky porovnávaný odpovídajícího textu. Nejlepších výsledků dosáhnete použijte jinou sadu dat akustickými než ten, který jste použili k vytvoření modelu.

## <a name="custom-endpoint"></a>Vlastní koncový bod

Jakmile vytvoříte vlastní akustickými modely nebo modely jazyk, je můžete nasadit do vlastní **řeči na Text** koncový bod. Pouze účet, který je vytvořen koncový bod je povoleno provádět volání do ní.

Chcete-li vytvořit koncový bod, zvolte **koncové body** z **vlastní řeči** nabídce v horní části stránky. **Koncové body** stránka obsahuje tabulku aktuální vlastní koncové body, pokud jste vytvořili žádné. Klikněte na tlačítko **vytvořit nový** k vytvoření nového koncového bodu.

Vyberte modely, které chcete použít v **akustickými modelu** a **jazykový Model** uvádí. Dostupné možnosti, vždy zahrňte základní modely společnosti Microsoft. Nesmí kombinovat konverzačního modely s vyhledávání a modely, určují, takže volba akustickými modelu omezuje modely k dispozici jazyk a naopak. Můžete použít stejné modely v libovolný počet koncových bodů.

Klikněte na tlačítko **vytvořit** po výběru modely. Zřídit nový koncový bod může trvat až 30 minut.

Pokud váš koncový bod je připraven, vyberte ho v **koncové body** tabulce najdete ID URI a nasazení Můžete použít vlastní koncové body pomocí [Rest API](rest-apis.md#speech-to-text) a [řeči SDK](speech-sdk.md). [Ukázky kódu jsou](samples.md) zahrnují příklad použití vlastní převod řeči na Text koncový bod.

## <a name="next-steps"></a>Další postup

- [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
- [Jak rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
