---
title: Co je Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Dozvíte se, jak s použitím Content Moderatoru sledovat, označovat příznaky, vyhodnocovat a filtrovat nevhodný materiál v uživatelem generovaném obsahu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: a78a92a33075a97ddadb2e1fe677b7ded541d12c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565580"
---
# <a name="what-is-azure-content-moderator"></a>Co je Azure Content Moderator?

Rozhraní API Azure Content Moderatoru je služba Cognitive Services, která kontroluje potenciálně urážlivý, rizikový nebo jinak nežádoucí materiál v textu, obrázcích a videích. Pokud se takový materiál najde, použije služba pro obsah odpovídající popisky (příznaky). Vaše aplikace pak může obsah označený příznakem zpracovat a zajistit tak dodržování předpisů nebo zachování zamýšleného prostředí pro uživatele. Další informace o tom, co ukazují různé příznaky obsahu, najdete v části [rozhraní API](#moderation-apis) pro moderování.

## <a name="where-it-is-used"></a>Kde se používá

Následuje několik scénářů, ve kterých by vývojář nebo tým vývojářů softwaru mohli službu Content Moderator využít:

- Online tržiště, které jsou moderované katalogy produktů a další uživatelem generovaný obsah.
- Herní společnosti, které středně vytvářené herní artefakty a konverzační místnosti.
- Systémy zasílání zpráv pro sociální sítě, které přidávají jejich uživatelé jako střední obrázky, text a videa.
- Podnikové mediální společnosti, které implementují centralizované moderování pro svůj obsah.
- K-12 poskytovatelé řešení pro vzdělávání odfiltrují obsah, který je nevhodný pro studenty a pedagogy.

> [!NOTE]
> Content Moderator nelze použít k detekci neplatných imagí pro zneužití. Kvalifikovaní organizace ale můžou použít(https://www.microsoft.com/photodna "cloudovou službu Microsoft PhotoDNA") [PhotoDNA Cloud]Service k zobrazení tohoto typu obsahu.

## <a name="what-it-includes"></a>Co zahrnuje

Služba Content Moderator se skládá z několika rozhraní API webových služeb, která jsou k dispozici prostřednictvím volání REST i sady .NET SDK. Zahrnuje také nástroj pro kontrolu prováděnou lidmi, který lidským kontrolorům umožňuje ve službě vylepšovat nebo ladit funkci moderování.

## <a name="moderation-apis"></a>Rozhraní API pro moderování

Služba Content Moderator zahrnuje rozhraní API pro moderování, které kontroluje obsah pro materiál, který je potenciálně nevhodný nebo nevhodný.

![Blokový diagram pro rozhraní API pro moderování Content Moderator](images/content-moderator-mod-api.png)

Následující tabulka popisuje různé typy rozhraní API pro moderování.

| Skupina rozhraní API | Popis |
| ------ | ----------- |
|[**Moderování textu**](text-moderation-api.md)| Kontroluje text pro urážlivý obsah, sexuální explicitní nebo sugestivní obsah, vulgární výrazy a osobní údaje.|
|[**Seznamy vlastních výrazů**](try-terms-list-api.md)| Vyhledává v textu kromě předdefinovaných výrazů i výrazy z vlastního seznamu. Pomocí vlastních seznamů můžete blokovat nebo povolovat obsah podle vlastních zásad obsahu.|  
|[**Moderování obrázků**](image-moderation-api.md)| Vyhledává na obrázcích obsah pro dospělé nebo nevhodný obsah, rozpoznává text na obrázcích pomocí funkce optického rozpoznávání znaků (OCR) a rozpoznává tváře.|
|[**Seznamy vlastních obrázků**](try-image-list-api.md)| Vyhledává na obrázcích obrázky z vlastního seznamu. Pomocí seznamů vlastních obrázků můžete vyfiltrovat instance běžně se opakujícího obsahu, který nechcete znovu klasifikovat.|
|[**Moderování videa**](video-moderation-api.md)| Vyhledává ve videích nevhodný obsah nebo obsah pro dospělé a vrací pro uvedený obsah časové značky.|

## <a name="review-apis"></a>Recenze rozhraní API

Rozhraní API pro kontrolu vám umožňují integrovat kanál moderování s lidmi revidujícími. K vytváření a automatizaci pracovních postupů pomocí [Nástroje pro kontrolu](#the-review-tool) (níže) použijte [úlohy](review-api.md#jobs), [recenze](review-api.md#reviews)a operace [pracovního postupu](review-api.md#workflows) .

> [!NOTE]
> Rozhraní API pracovního postupu ještě není dostupné v sadě .NET SDK, ale dá se použít s koncovým bodem REST.

![Blokový diagram pro rozhraní API pro kontrolu Content Moderator](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Nástroj pro kontrolu

Služba Content Moderator také zahrnuje webový [Nástroj](Review-Tool-User-Guide/human-in-the-loop.md)pro revizi, který hostuje revize obsahu pro lidské moderování ke zpracování. Lidský přínos neslouží k trénování služby, ale kombinace práce služby a lidského posuzovacího týmu umožní vývojářům dosáhnout správné rovnováhy mezi efektivitou a přesností. Nástroj pro kontrolu poskytuje také uživatelsky přívětivý front-end pro celou řadu Content Moderatorch prostředků.

![Úvodní stránka nástroje pro kontrolu prováděnou lidmi v Content Moderatoru](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Services by měli vývojáři, kteří používají Content Moderator službu, znát zásady Microsoftu pro zákaznická data. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další postup

Začněte používat službu Content Moderator podle pokynů v části [vyzkoušet Content moderator na webu](quick-start.md).