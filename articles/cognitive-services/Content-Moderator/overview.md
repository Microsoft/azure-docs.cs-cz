---
title: Co je Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Dozvíte se, jak s použitím Content Moderatoru sledovat, označovat příznaky, vyhodnocovat a filtrovat nevhodný materiál v uživatelem generovaném obsahu.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 10/22/2018
ms.author: sajagtap
ms.openlocfilehash: 076948e7434802af7f0ad47f279335009817d40e
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209580"
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

| Akce | Popis |
| ------ | ----------- |
|[**Moderování textu**](text-moderation-api.md)| Vyhledává v textu urážlivý obsah, sexuálně explicitní nebo sugestivní obsah, vulgární výrazy a identifikovatelné osobní údaje (PII).|
|[**Seznamy vlastních výrazů**](try-terms-list-api.md)| Vyhledává v textu kromě předdefinovaných výrazů i výrazy z vlastního seznamu. Pomocí vlastních seznamů můžete blokovat nebo povolovat obsah podle vlastních zásad obsahu.|  
|[**Moderování obrázků**](image-moderation-api.md)| Vyhledává na obrázcích obsah pro dospělé nebo nevhodný obsah, rozpoznává text na obrázcích pomocí funkce optického rozpoznávání znaků (OCR) a rozpoznává tváře.|
|[**Seznamy vlastních obrázků**](try-image-list-api.md)| Vyhledává na obrázcích obrázky z vlastního seznamu. Pomocí seznamů vlastních obrázků můžete vyfiltrovat instance běžně se opakujícího obsahu, který nechcete znovu klasifikovat.|
|[**Moderování videa**](video-moderation-api.md)| Vyhledává ve videích nevhodný obsah nebo obsah pro dospělé a vrací pro uvedený obsah časové značky.|
|[**Kontrola**](try-review-api-job.md)| Pomocí operací [úloh](try-review-api-job.md), [kontrol](try-review-api-review.md) a [pracovních postupů](try-review-api-workflow.md) můžete v nástroji pro kontrolu prováděnou lidmi vytvořit a automatizovat pracovní postupy prováděné lidmi. Rozhraní API pro pracovní postupy ještě není v sadě .NET SDK dostupné.|

### <a name="human-review-tool"></a>Nástroj pro kontrolu prováděnou lidmi

Služba Content Moderator zahrnuje také webový [nástroj pro kontrolu prováděnou lidmi](Review-Tool-User-Guide/human-in-the-loop.md). 

![Úvodní stránka nástroje pro kontrolu prováděnou lidmi v Content Moderatoru](images/homepage.PNG)

Pomocí rozhraní API pro kontrolu můžete nastavit týmové kontroly textu, obrázků a videí v závislosti na zadaných filtrech. Konečné rozhodnutí o moderování pak můžou provést lidští moderátoři. Lidský přínos neslouží k trénování služby, ale kombinace práce služby a lidského posuzovacího týmu umožní vývojářům dosáhnout správné rovnováhy mezi efektivitou a přesností.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat Content Moderator, postupujte podle tohoto [rychlého startu](quick-start.md).
