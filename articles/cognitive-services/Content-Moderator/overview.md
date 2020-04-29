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
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: f28f2bcf5d04c9a6354b8135bd39546b9d8b9bf3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404301"
---
# <a name="what-is-azure-content-moderator"></a>Co je Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator je služba pro rozpoznávání, která kontroluje text, obrázky a obsah videa pro materiál, který je potenciálně urážlivý, rizikové nebo jinak nežádoucí. Když se tento materiál najde, služba použije příslušné popisky (příznaky) k obsahu. Vaše aplikace pak může obsah označený příznakem zpracovat a zajistit tak dodržování předpisů nebo zachování zamýšleného prostředí pro uživatele. Další informace o tom, co ukazují různé příznaky obsahu, najdete v části [rozhraní API pro moderování](#moderation-apis) .

## <a name="where-its-used"></a>Kde se používá

Následuje několik scénářů, ve kterých by vývojář nebo tým vývojářů softwaru mohli službu Content Moderator využít:

- Online tržiště, které jsou moderované katalogy produktů a další uživatelem generovaný obsah.
- Herní společnosti, které středně vytvářené herní artefakty a konverzační místnosti.
- Systémy zasílání zpráv pro sociální sítě, které přidávají jejich uživatelé jako střední obrázky, text a videa.
- Podnikové mediální společnosti, které implementují centralizované moderování pro svůj obsah.
- K-12 poskytovatelé řešení pro vzdělávání odfiltrují obsah, který je nevhodný pro studenty a pedagogy.

> [!NOTE]
> Content Moderator nelze použít k detekci neplatných imagí pro zneužití. Kvalifikovaní organizace ale můžou použít [cloudovou službu PhotoDNA](https://www.microsoft.com/photodna "Cloudová služba Microsoft PhotoDNA") na obrazovku pro tento typ obsahu.

## <a name="what-it-includes"></a>Co zahrnuje

Služba Content Moderator se skládá z několika rozhraní API webových služeb, která jsou k dispozici prostřednictvím volání REST i sady .NET SDK. Zahrnuje také nástroj pro revize, který umožňuje lidským kontrolorům pomoci služby a zdokonalit nebo vyladit funkci moderování.

## <a name="moderation-apis"></a>Rozhraní API pro moderování

Služba Content Moderator zahrnuje rozhraní API pro moderování, které kontroluje obsah pro materiál, který je potenciálně nevhodný nebo nevhodný.

![Blokový diagram pro rozhraní API pro moderování Content Moderator](images/content-moderator-mod-api.png)

Následující tabulka popisuje různé typy rozhraní API pro moderování.

| Skupina rozhraní API | Popis |
| ------ | ----------- |
|[**Moderování textu**](text-moderation-api.md)| Kontroluje text pro urážlivý obsah, sexuální explicitní nebo sugestivní obsah, vulgární výrazy a osobní údaje.|
|[**Seznamy vlastních výrazů**](try-terms-list-api.md)| Vyhledá text na základě vlastního seznamu podmínek spolu s vestavěnými podmínkami. Pomocí vlastních seznamů můžete blokovat nebo povolovat obsah podle vlastních zásad obsahu.|  
|[**Moderování obrázků**](image-moderation-api.md)| Vyhledává na obrázcích obsah pro dospělé nebo nevhodný obsah, rozpoznává text na obrázcích pomocí funkce optického rozpoznávání znaků (OCR) a rozpoznává tváře.|
|[**Seznamy vlastních obrázků**](try-image-list-api.md)| Vyhledává na obrázcích obrázky z vlastního seznamu. Pomocí seznamů vlastních obrázků můžete vyfiltrovat instance běžně se opakujícího obsahu, který nechcete znovu klasifikovat.|
|[**Moderování videa**](video-moderation-api.md)| Vyhledává ve videích nevhodný obsah nebo obsah pro dospělé a vrací pro uvedený obsah časové značky.|

## <a name="review-apis"></a>Recenze rozhraní API

Rozhraní API pro kontrolu vám umožňují integrovat kanál moderování s lidmi revidujícími. K vytváření a automatizaci pracovních postupů pomocí [Nástroje pro kontrolu](#review-tool) (níže) použijte [úlohy](review-api.md#jobs), [recenze](review-api.md#reviews)a operace [pracovního postupu](review-api.md#workflows) .

> [!NOTE]
> Rozhraní API pracovního postupu ještě není dostupné v sadě .NET SDK, ale dá se použít s koncovým bodem REST.

![Blokový diagram pro rozhraní API pro kontrolu Content Moderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Nástroj pro revize

Služba Content Moderator také zahrnuje webový [Nástroj pro revizi](Review-Tool-User-Guide/human-in-the-loop.md), který hostuje revize obsahu pro lidské moderování ke zpracování. Lidské vstupy tuto službu nevlakují, ale kombinovaná práce týmů služby a lidských revizí umožňuje vývojářům přeškrtnout správné rovnováhu mezi efektivitou a přesností. Nástroj pro kontrolu také poskytuje uživatelsky přívětivý front-end pro několik Content Moderatorch prostředků.

![Domovská stránka nástroje Content Moderator Revize](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Services by měli vývojáři, kteří používají Content Moderator službu, znát zásady Microsoftu pro zákaznická data. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Začněte používat službu Content Moderator podle pokynů v části [vyzkoušet Content moderator na webu](quick-start.md).
