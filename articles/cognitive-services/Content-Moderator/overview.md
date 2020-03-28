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
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 570d55c8523e1c1deca3242a8841b0cc34322786
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053876"
---
# <a name="what-is-azure-content-moderator"></a>Co je Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator je kognitivní služba, která kontroluje text, obrázek a video obsah pro materiál, který je potenciálně urážlivý, riskantní nebo jinak nežádoucí. Když je tento materiál nalezen, služba použije příslušné popisky (příznaky) na obsah. Vaše aplikace pak může obsah označený příznakem zpracovat a zajistit tak dodržování předpisů nebo zachování zamýšleného prostředí pro uživatele. Další informace o tom, co označují různé příznaky obsahu, najdete v části [Moderování api.](#moderation-apis)

## <a name="where-its-used"></a>Kde se používá

Následuje několik scénářů, ve kterých by vývojář nebo tým vývojářů softwaru mohli službu Content Moderator využít:

- Online tržiště, která moderují katalogy produktů a další obsah vytvářený uživateli.
- Herní společnosti, které moderují uživatelské herní artefakty a chatovací místnosti.
- Platformy pro zasílání sociálních zpráv, které moderují obrázky, text a videa přidané jejich uživateli.
- Podnikové mediální společnosti, které implementují centralizovanou moderování svého obsahu.
- Poskytovatelé vzdělávacích řešení K-12 odfiltrovali obsah, který je pro studenty a pedagogy nevhodný.

> [!NOTE]
> Moderátor obsahu nelze použít k detekci nelegálních obrázků zneužívání dětí. Kvalifikované organizace však mohou používat [službu PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Cloudová služba Microsoft PhotoDNA") k obrazovce pro tento typ obsahu.

## <a name="what-it-includes"></a>Co zahrnuje

Služba Content Moderator se skládá z několika rozhraní API webových služeb, která jsou k dispozici prostřednictvím volání REST i sady .NET SDK. Zahrnuje také nástroj pro kontrolu prováděnou lidmi, který lidským kontrolorům umožňuje ve službě vylepšovat nebo ladit funkci moderování.

## <a name="moderation-apis"></a>Rozhraní API pro moderování

Služba Content Moderator obsahuje nastavení API pro moderování, která kontrolují obsah materiálu, který je potenciálně nevhodný nebo nežádoucí.

![blokový diagram pro nastavení API moderování moderátora obsahu](images/content-moderator-mod-api.png)

Následující tabulka popisuje různé typy podmínek pro podporu moderování.

| Skupina ROZHRANÍ API | Popis |
| ------ | ----------- |
|[**Moderování textu**](text-moderation-api.md)| Skenuje text pro urážlivý obsah, sexuálně explicitní nebo sugestivní obsah, vulgární výrazy a osobní údaje.|
|[**Seznamy vlastních výrazů**](try-terms-list-api.md)| Skenuje text proti vlastnímu seznamu termínů spolu s předdefinovanými termíny. Pomocí vlastních seznamů můžete blokovat nebo povolovat obsah podle vlastních zásad obsahu.|  
|[**Moderování obrazu**](image-moderation-api.md)| Vyhledává na obrázcích obsah pro dospělé nebo nevhodný obsah, rozpoznává text na obrázcích pomocí funkce optického rozpoznávání znaků (OCR) a rozpoznává tváře.|
|[**Seznamy vlastních obrázků**](try-image-list-api.md)| Vyhledává na obrázcích obrázky z vlastního seznamu. Pomocí seznamů vlastních obrázků můžete vyfiltrovat instance běžně se opakujícího obsahu, který nechcete znovu klasifikovat.|
|[**Moderování videa**](video-moderation-api.md)| Vyhledává ve videích nevhodný obsah nebo obsah pro dospělé a vrací pro uvedený obsah časové značky.|

## <a name="review-apis"></a>Recenze rozhraní API

Rozhraní API pro kontrolu umožňují integrovat kanál moderování s lidskými recenzenty. Pomocí operací [Úlohy](review-api.md#jobs), [Recenze](review-api.md#reviews)a [Pracovní postup](review-api.md#workflows) můžete vytvářet a automatizovat pracovní postupy člověk ve smyčce pomocí [nástroje Revize](#the-review-tool) (níže).

> [!NOTE]
> Rozhraní API pracovního postupu ještě není k dispozici v sdk .NET, ale lze použít s koncovým bodem REST.

![blokový diagram pro nastavení API pro kontrolu obsahu moderátora](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Nástroj revize

Služba Content Moderator také obsahuje webový [nástroj pro kontrolu](Review-Tool-User-Guide/human-in-the-loop.md), který je hostitelem recenzí obsahu, který mají moderátoři zpracovat. Lidský vstup netrénuje službu, ale kombinovaná práce servisních a lidských revizních týmů umožňuje vývojářům nalézt správnou rovnováhu mezi efektivitou a přesností. Nástroj Revize také poskytuje uživatelsky přívětivý front-end pro několik zdrojů Content Moderator.

![Úvodní stránka nástroje pro kontrolu prováděnou lidmi v Content Moderatoru](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení

Stejně jako u všech služeb Cognitive Services by vývojáři používající službu Content Moderator měli znát zásady společnosti Microsoft týkající se zákaznických dat. Další informace najdete na [stránce Služby Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v Centru zabezpečení společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

Můžete začít používat službu Content Moderator podle pokynů v části [Vyzkoušet moderátor obsahu na webu](quick-start.md).
