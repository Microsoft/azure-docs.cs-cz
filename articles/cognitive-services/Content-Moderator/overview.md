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
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: d814a943bc8dc789abe84b33583714beb998c0ef
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607017"
---
# <a name="what-is-azure-content-moderator"></a>Co je Azure Content Moderator?

Rozhraní API Azure Content Moderatoru je služba Cognitive Services, která kontroluje potenciálně urážlivý, rizikový nebo jinak nežádoucí materiál v textu, obrázcích a videích. Pokud se takový materiál najde, použije služba pro obsah odpovídající popisky (příznaky). Vaše aplikace pak může obsah označený příznakem zpracovat a zajistit tak dodržování předpisů nebo zachování zamýšleného prostředí pro uživatele. Zobrazit [rozhraní API pro moderování](#moderation-apis) označení naleznete další informace o co příznaky jiný obsah.

## <a name="where-it-is-used"></a>Kde se používá

Následuje několik scénářů, ve kterých by vývojář nebo tým vývojářů softwaru mohli službu Content Moderator využít:

- Online tržišť s řešeními, které moderování katalogů produktů a dalších na uživatelem generovaný obsah.
- Hraní her, které střední uživatelem generovaný her artefakty a chatovací místnosti.
- Sociální platforem zasílání zpráv, které moderování obrázků, textu a videa přidaná uživatelé přistupují.
- Enterprise mediálním společnostem, které implementují centralizované moderování pro svůj obsah.
- Filtrování obsahu, která je nevhodná pro studenty a vyučující poskytovatelů řešení K-12 vzdělávání.

> [!NOTE]
> Content Moderator nelze použít k detekci zneužití imagí neplatný podřízený. Však můžete použít kvalifikovaný organizace [PhotoDNA Cloudovou službu](https://www.microsoft.com/photodna "cloudové služby Microsoftu PhotoDNA") na obrazovku pro tento typ obsahu.

## <a name="what-it-includes"></a>Co zahrnuje

Služba Content Moderator se skládá z několika rozhraní API webových služeb, která jsou k dispozici prostřednictvím volání REST i sady .NET SDK. Zahrnuje také nástroj pro kontrolu prováděnou lidmi, který lidským kontrolorům umožňuje ve službě vylepšovat nebo ladit funkci moderování.

## <a name="moderation-apis"></a>Rozhraní API pro moderování

Služba Content Moderator zahrnuje rozhraní API pro moderování, který zkontrolujte obsah pro materiál, který je potenciálně nevhodný nebo nežádoucí.

![Blokový diagram pro rozhraní API pro moderování Content Moderatoru](images/content-moderator-mod-api.png)

Následující tabulka popisuje různé typy rozhraní API pro moderování.

| Skupina rozhraní API | Popis |
| ------ | ----------- |
|[**Moderování textu**](text-moderation-api.md)| Vyhledá text pro neslušný obsah, sexuálně explicitní nebo sugestivní obsah, vulgárních výrazů a osobních údajů.|
|[**Seznamy vlastních výrazů**](try-terms-list-api.md)| Vyhledává v textu kromě předdefinovaných výrazů i výrazy z vlastního seznamu. Pomocí vlastních seznamů můžete blokovat nebo povolovat obsah podle vlastních zásad obsahu.|  
|[**Moderování obrázků**](image-moderation-api.md)| Vyhledává na obrázcích obsah pro dospělé nebo nevhodný obsah, rozpoznává text na obrázcích pomocí funkce optického rozpoznávání znaků (OCR) a rozpoznává tváře.|
|[**Seznamy vlastních obrázků**](try-image-list-api.md)| Vyhledává na obrázcích obrázky z vlastního seznamu. Pomocí seznamů vlastních obrázků můžete vyfiltrovat instance běžně se opakujícího obsahu, který nechcete znovu klasifikovat.|
|[**Moderování videa**](video-moderation-api.md)| Vyhledává ve videích nevhodný obsah nebo obsah pro dospělé a vrací pro uvedený obsah časové značky.|

## <a name="review-apis"></a>Recenze rozhraní API

Revize rozhraní API umožňují integrovat svůj kanál moderování lidské revidující. Použití [úlohy](review-api.md#jobs), [revize](review-api.md#reviews), a [pracovního postupu](review-api.md#workflows) operace vytvoření a automatizovat pracovní postupy lidských v the smyčky [nástroj pro recenze](#the-review-tool) () níže).

> [!NOTE]
> Rozhraní API pro pracovní postup ještě není k dispozici v sadě .NET SDK, ale je možné ke koncovému bodu REST.

![Blokový diagram pro Content Moderator, projděte si rozhraní API](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Nástroj pro recenze

Služba Content Moderator zahrnuje i webová [nástroj pro recenze](Review-Tool-User-Guide/human-in-the-loop.md), který je hostitelem obsahu kontroly pro lidské moderátory ke zpracování. Lidský přínos neslouží k trénování služby, ale kombinace práce služby a lidského posuzovacího týmu umožní vývojářům dosáhnout správné rovnováhy mezi efektivitou a přesností. Nástroj pro recenze také poskytuje uživatelsky přívětivé front-endu pro různé prostředky Content Moderatoru.

![Úvodní stránka nástroje pro kontrolu prováděnou lidmi v Content Moderatoru](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Ochrany osobních údajů a zabezpečení

Jako se všemi služeb Cognitive Services, vývojáře, kteří používají službu Content Moderator měli vědět zásady společnosti Microsoft na zákaznická data. Zobrazit [stránku služeb Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na webu Microsoft Trust Center další informace.

## <a name="next-steps"></a>Další postup

Začínáme používat službu Content Moderator podle pokynů v [zkuste Content Moderator na webu](quick-start.md).