---
title: Co je Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Dozvíte se, jak s použitím Content Moderatoru sledovat, označovat příznaky, vyhodnocovat a filtrovat nevhodný materiál v uživatelem generovaném obsahu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 440471acb6e122bf25ba21b0ab3b5a2f7d9b021d
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758131"
---
# <a name="what-is-azure-content-moderator"></a>Co je Azure Content Moderator?

Rozhraní API Azure Content Moderatoru je služba Cognitive Services, která kontroluje potenciálně urážlivý, rizikový nebo jinak nežádoucí materiál v textu, obrázcích a videích. Pokud se takový materiál najde, použije služba pro obsah odpovídající popisky (příznaky). Vaše aplikace pak může obsah označený příznakem zpracovat a zajistit tak dodržování předpisů nebo zachování zamýšleného prostředí pro uživatele. Další informace o tom, co značí různé příznaky obsahu, najdete v části [Rozhraní API Content Moderatoru](#content-moderator-apis).

## <a name="where-it-is-used"></a>Kde se používá

Následuje několik scénářů, ve kterých by vývojář nebo tým vývojářů softwaru mohli službu Content Moderator využít:

- Online tržiště moderující katalogy produktů a další uživatelem generovaný obsah
- Herní společnosti moderující uživatelem generované herní artefakty a chatovací místnosti
- Sociální platforma zasílání zpráv moderující obrázky, texty a videa přidaná uživateli
- Podnikové mediální společnosti implementující centralizované moderování obsahu
- Poskytovatelé řešení vzdělávání K-12 filtrující nevhodný obsah pro studenty a vyučující

## <a name="what-it-includes"></a>Co zahrnuje

Služba Content Moderator se skládá z několika rozhraní API webových služeb, která jsou k dispozici prostřednictvím volání REST i sady .NET SDK. Zahrnuje také nástroj pro kontrolu prováděnou lidmi, který lidským kontrolorům umožňuje ve službě vylepšovat nebo ladit funkci moderování.

![Blokový diagram pro Content Moderator znázorňující rozhraní API pro moderování, rozhraní API pro kontrolu a nástroj pro kontrolu prováděnou lidmi](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>Rozhraní API Content Moderatoru

Služba Content Moderator zahrnuje rozhraní API pro následující scénáře.

| Skupina rozhraní API | Popis |
| ------ | ----------- |
|[**Moderování textu**](text-moderation-api.md)| Vyhledá text pro neslušný obsah, sexuálně explicitní nebo sugestivní obsah, vulgárních výrazů a osobních údajů.|
|[**Seznamy vlastních výrazů**](try-terms-list-api.md)| Vyhledává v textu kromě předdefinovaných výrazů i výrazy z vlastního seznamu. Pomocí vlastních seznamů můžete blokovat nebo povolovat obsah podle vlastních zásad obsahu.|  
|[**Moderování obrázků**](image-moderation-api.md)| Vyhledává na obrázcích obsah pro dospělé nebo nevhodný obsah, rozpoznává text na obrázcích pomocí funkce optického rozpoznávání znaků (OCR) a rozpoznává tváře.|
|[**Seznamy vlastních obrázků**](try-image-list-api.md)| Vyhledává na obrázcích obrázky z vlastního seznamu. Pomocí seznamů vlastních obrázků můžete vyfiltrovat instance běžně se opakujícího obsahu, který nechcete znovu klasifikovat.|
|[**Moderování videa**](video-moderation-api.md)| Vyhledává ve videích nevhodný obsah nebo obsah pro dospělé a vrací pro uvedený obsah časové značky.|
|[**Projděte si rozhraní API**](try-review-api-job.md)| Pomocí operací [úloh](try-review-api-job.md), [kontrol](try-review-api-review.md) a [pracovních postupů](try-review-api-workflow.md) můžete v nástroji pro kontrolu prováděnou lidmi vytvořit a automatizovat pracovní postupy prováděné lidmi. Rozhraní API pro pracovní postup ještě není k dispozici v sadě .NET SDK.|

### <a name="review-tool"></a>Nástroj pro revize

Služba Content Moderator zahrnuje i webová [nástroj pro recenze](Review-Tool-User-Guide/human-in-the-loop.md), který je hostitelem obsahu kontroly pro lidské moderátory ke zpracování. Lidský přínos neslouží k trénování služby, ale kombinace práce služby a lidského posuzovacího týmu umožní vývojářům dosáhnout správné rovnováhy mezi efektivitou a přesností. Nástroj pro recenze také poskytuje uživatelsky přívětivé front-endu pro širokou škálu prostředků Content Moderatoru.

![Úvodní stránka nástroje pro kontrolu prováděnou lidmi v Content Moderatoru](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Ochrany osobních údajů a zabezpečení

Jako se všemi služeb Cognitive Services, vývojáře, kteří používají službu Content Moderator měli vědět zásady společnosti Microsoft na zákaznická data. Zobrazit [stránku služeb Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na webu Microsoft Trust Center další informace.

## <a name="next-steps"></a>Další postup

Začínáme používat službu Content Moderator podle pokynů v [zkuste Content Moderator na webu](quick-start.md).