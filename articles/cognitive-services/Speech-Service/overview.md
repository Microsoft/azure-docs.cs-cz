---
title: Co je Speech service (preview)? | Dokumenty Microsoft
description: 'Speech service součástí společnosti Microsoft Cognitive Services, sjednotí několik služeb Azure řeči, které byly dříve k dispozici samostatně: (zahrnující rozpoznávání řeči a převod textu na řeč) pro zpracování řeči Bingu, vlastní řeč a překladu řeči.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: ccdcdeeaf4ac8730be4f9e3ee648dc41c2a02641
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345154"
---
# <a name="what-is-the-speech-service-preview"></a>Co je Speech service (preview)?

Speech service součástí společnosti Microsoft Cognitive Services, sjednotí několik služeb Azure řeči, které byly dříve k dispozici samostatně: (zahrnující rozpoznávání řeči a převod textu na řeč) pro zpracování řeči Bingu, vlastní řeč a překladu řeči. Stejně jako jeho prekurzorů Speech service využívá k tomu technologií používaných pro ostatní produkty Microsoftu, včetně Cortana a Microsoft Office.

> [!NOTE]
> Speech service je aktuálně ve verzi public preview. Vraťte se sem pravidelně aktualizací do dokumentace, ukázky kódu Další a další.

S jedním oděrem jednotné Speech service poskytuje vývojářům snadný způsob, jak poskytnout funkce výkonnou podporou hlasových jejich aplikací. Své aplikace můžete nyní funkcí hlasových příkazů, přepis, diktování, syntézu řeči a překladu.

|Funkce|Popis|
|-|-|
|Převod řeči na text|Převede průběžné lidské řeči na text, který může sloužit jako vstup do vaší aplikace. Můžete integrovat [služby Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) k odvození z projevy záměru uživatele.|
|Převod textu na řeč|Převede text na zvukové soubory přirozeného to nezní řečového.|
|Rozpoznávání řeči&nbsp;překladu|Překlady řeči do jiných jazyků, poskytněte text nebo řeči výstup.|

## <a name="using-the-speech-service"></a>Pomocí služby řeči

Speech service je k dispozici dvě možnosti. [Sada SDK](speech-sdk.md) vyčleňuje podrobnostmi síťových protokolů. [Rozhraní REST API](rest-apis.md) funguje s libovolným programovacím jazykem, ale neposkytuje všechny funkce nabízené prostřednictvím sady SDK.

|<br>Metoda|Řeč<br>na Text|Text, který se<br>Řeč|Řeč<br>Překlad|<br>Popis|
|-|-|-|-|-|
|[Sady SDK](speech-sdk.md)|Ano|Ne|Ano|Knihovny pro konkrétní programovací jazyky, které zjednodušují vývoj.|
|[REST](rest-apis.md)|Ano|Ano|Ne|Jednoduché založené na protokolu HTTP rozhraní API, která umožňuje snadno přidat rozpoznávání řeči do vaší aplikace.|

## <a name="speech-to-text"></a>Převod řeči na text

[Převod řeči na Text](speech-to-text.md) (STT), nebo rozpoznávání řeči, rozhraní API transcribes audiostreamy do textu, který vaše aplikace může přijmout jako vstup. Vaše aplikace potom může například zadat tento text do dokumentu nebo ho zpracovat jako příkaz.

Převod řeči na Text samostatně optimalizované pro interaktivní, konverzace a scénáře diktování. Tady jsou běžné případy použití pro Speech to Text API. 

* Rozpoznat stručný utterance, například příkaz bez prozatímní výsledky
* Přepisy dlouhý, dříve nahrané utterance, jako je například hlasové pošty zpráva
* Přepisy streamování řeči v reálném čase pomocí částečných výsledků pro diktování
* Zjistit, co uživatelé chtějí provedete podle mluvené slovo požadavek v přirozeném jazyce

Služba Speech to Text API podporuje přepis interaktivní řeči v reálném čase průběžné rozpoznávání a dočasné výsledky. Podporuje také detekci konce řeči, volitelné automatické použití velkých a malých písmen a interpunkce, maskování vulgarismů a normalizaci textu.

Můžete přizpůsobit převod řeči na Text akustických a jazykových modelů, aby pokryla specializovaného slovníku, hlučného prostředí a různé způsoby mluvený.

## <a name="text-to-speech"></a>Převod textu na řeč

[Převod textu na řeč](text-to-speech.md) (převod textu na ŘEČ), nebo syntézu řeči, rozhraní API převede prostého textu na řeč přirozeného to nezní, doručí do vaší aplikace v zvukový soubor. Více hlasů ve pohlaví nebo zvýraznění, jsou k dispozici pro mnoho podporované jazyky.

Rozhraní API podporuje značky řeči syntézu Markup Language (SSML) umožňující vám zadat přesné fonetická výslovnost problémových slov. SSML může také přímo v textu indikovat řečové charakteristiky (včetně důrazu, rychlosti, hlasitosti, pohlaví a výšky).

Tady jsou běžné případy použití pro převod textu na řeč rozhraní API.

* Řečovém výstupu jako alternativní obrazovky výstupu pro zrakově postižené uživatele
* Hlasové vás vyzve k zadání automobilu aplikací, jako je navigace
* Konverzační uživatelská rozhraní společně s rozhraním Speech to Text API

Pokud potřebujete nepodporované dialekt nebo chcete jenom jedinečný hlas pro vaši aplikaci, převod textu na řeč rozhraní API podporuje vlastní hlasové modely.

## <a name="speech-translation"></a>Překlad řeči

[Překlad řeči](speech-translation.md) rozhraní API je možné přeložit zvukové streamy v téměř reálném čase nebo zpracovat zaznamenané řeči. Ve streamování překladu, vrátí služba prozatímní výsledky, které se dají zobrazit uživateli, aby se indikoval průběh překladu. Výsledky se dají vrátit v textové nebo hlasové podobě.

Případy použití pro překlad řeči, patří.

* Implementace "konverzační" překladu mobilní aplikace nebo zařízení pro cestovatelům 
* Zadejte automatické překlady titulkování z nahrávky zvuku a videa

## <a name="speech-devices-sdk"></a>Speech Devices SDK

Se zavedením jednotné Speech service, Microsoft a příslušní partneři nabízí platformu integrované hardware a software pro optimalizovaný pro vývoj zařízení řeči: [sadou SDK pro řeč zařízení](speech-devices-sdk.md). Tato sada SDK je vhodná pro vývoj inteligentních hlasových zařízení pro všechny typy použití.

Zařízení sadou SDK pro řeč umožňuje vytvářet okolí zařízeních pomocí přizpůsobených probuzení slovo, tak, aby upozornění, která aktivuje zvuku jedinečné pro vlastní značky. Poskytuje také špičkové zpracování zvuku z vícekanálových zdrojů pro přesnější rozpoznávání řeči, včetně potlačení šumu, a technologií Far-Field Voice a Beamforming.

Sada SDK je založená na webové sokety používá port 443.

## <a name="next-steps"></a>Další postup

Získejte bezplatné předplatné zkušební verze klíče pro službu rozpoznávání řeči.

> [!div class="nextstepaction"]
> [Bezplatné vyzkoušení Speech service](get-started.md)
