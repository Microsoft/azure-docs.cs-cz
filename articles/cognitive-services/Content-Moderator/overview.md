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
ms.date: 10/05/2018
ms.author: sajagtap
ms.openlocfilehash: 5756e8fb451b073c68271359848ab27373ad85ed
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309548"
---
# <a name="what-is-content-moderator"></a>Co je Content Moderator?

Moderování obsahu je proces monitorování potenciálně urážlivého, nežádoucího nebo rizikového materiálu v textu, na obrázcích a ve videu. Obsah označený příznakem je pak možné skrýt nebo jinak zpracovat a zajistit tak dodržování předpisů nebo zachování zamýšleného prostředí pro uživatele.

## <a name="where-it-is-used"></a>Kde se používá

Následující seznam obsahuje několik ukázkových scénářů, kde je možné Content Moderator použít:

- Online tržiště moderující katalogy produktů a uživatelem generovaný obsah
- Herní společnosti moderující uživatelem generované herní artefakty a chatovací místnosti
- Sociální platforma zasílání zpráv moderující obrázky, texty a videa přidaná uživateli
- Podnikové mediální společnosti implementující centralizované moderování jejich obsahu
- Poskytovatelé řešení vzdělávání K-12 filtrující nevhodný a urážlivý obsah pro studenty a vyučující

## <a name="what-it-includes"></a>Co zahrnuje

Content Moderator se skládá z několika rozhraní API webových služeb a integrovaného nástroje pro kontrolu HITL (human-in-the-loop), které pomáhají moderovat obrázky, text a videa.

![Blokový diagram Content Moderatoru](images/content-moderator-block-diagram.png)

### <a name="apis"></a>Rozhraní API

Služba Content Moderator zahrnuje následující rozhraní API:
  - [**Rozhraní API pro moderování textu**](text-moderation-api.md): pomocí tohoto rozhraní API můžete zkontrolovat, zda text obsahuje potenciálně vulgární, explicitní, sugestivní, urážlivé výrazy nebo identifikovatelné osobní údaje (PII).
  - [**Rozhraní API pro správu vlastního seznamu termínů**](try-terms-list-api.md): pomocí tohoto rozhraní API můžete navíc k integrovaným termínům hledat shody s vlastními seznamy termínů. Pomocí těchto seznamů můžete zablokovat nebo povolit obsah podle vašich zásad obsahu.  
  - [**Rozhraní API pro moderování obrázků**](image-moderation-api.md): pomocí tohoto rozhraní API můžete kontrolovat, zda se v obrázcích vyskytuje obsah pro dospělé nebo nevhodný obsah, rozpoznávat text v obrázku pomocí optického rozpoznávání znaků (OCR) a rozpoznávat tváře.
  - [**Rozhraní API pro správu vlastního seznamu obrázků**](try-image-list-api.md): pomocí tohoto rozhraní API můžete hledat shody s vlastním seznamem obrázků, předem identifikovaným obsahem, který není nutné znovu klasifikovat.
  - [**Rozhraní API moderování videa**](video-moderation-api.md): pomocí tohoto rozhraní API můžete kontrolovat videa z hlediska obsahu pro dospělé nebo nevhodného obsahu.
  - [**Rozhraní API pro kontrolu**](try-review-api-job.md): použijte operace [úlohy](try-review-api-job.md), [kontroly](try-review-api-review.md), a [pracovní postupy](try-review-api-workflow.md), abyste v nástroji pro kontrolu vytvořili a automatizovali pracovní postupy prováděné lidmi.

### <a name="human-review-tool"></a>Nástroj pro kontrolu prováděnou lidmi

Vaše předplatné Content Moderatoru zahrnuje integrovaný [nástroj pro kontrolu prováděnou lidmi](Review-Tool-User-Guide/human-in-the-loop.md). Pomocí výše uvedených rozhraní API pro kontrolu můžete vytvořit kontroly textu, obrázků a videí pro lidské moderátory a jejich konečná rozhodnutí.

![Nástroj pro kontrolu videa Content Moderatoru](images/video-review-default-view.png)

## <a name="next-steps"></a>Další kroky

Na stránce [rychlého startu](quick-start.md) získáte přehled, jak začít s Content Moderatorem.
