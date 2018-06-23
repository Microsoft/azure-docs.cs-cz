---
title: Přehled služby Azure obsahu moderátora | Microsoft Docs
description: Naučte se používat obsah moderátora ke sledování, příznak, hodnocení a filtrování nevhodných obsah na uživatelem generovaný obsah.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: cd9e2e9c10e9dc5ba118c8319f76174bf6f0da9f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343618"
---
# <a name="what-is-content-moderator"></a>Co je obsahu moderátora?

Přerušování obsahu je proces monitorování možné urážlivé, nežádoucím a rizikové obsahu. Moderované obsah může být bitové kopie, text a videa.

## <a name="where-it-is-used"></a>Kde se používá

Následující seznam obsahuje několik ukázkových scénářů, kde se používá moderátora obsahu:

- Online tržiště moderování katalogy produktů a uživatelem generovaný obsah
- Herní společností moderování uživatele vytvořila herní artefaktů a chatovací místnosti
- Sociální platformy zasílání zpráv moderování bitové kopie, text a videa přidal uživatelů
- Enterprise média společností implementace centralizované přerušování obsahu pro obsah
- Poskytovatelé řešení K-12 education filtrování chybný a urážlivé obsahu pro studenty a lektorům

## <a name="what-it-includes"></a>Její součástí

Obsahu moderátora se skládá z několika webové rozhraní API a integrované lidské v the smyčku zkontrolujte nástroje, který pomůže střední obrázky, text a videa.

![Obsahu moderátora Blokový diagram](images/content-moderator-block-diagram.png)

## <a name="apis"></a>Rozhraní API

Obsahu moderátora zahrnuje následující rozhraní API:
  - [**Text přerušování rozhraní API**](text-moderation-api.md): používat toto rozhraní API ke kontrole text možné vulgárnost explicitní, sugestivní, urážlivé a identifikovatelné osobní informace (PII).
  - [**Vlastní termín seznamu rozhraní API**](try-terms-list-api.md): Toto rozhraní API použijte k porovnání vlastní seznamy podmínky kromě předdefinovaných podmínky. Využijte tyto seznamy blokovat nebo povolit obsah podle obsahu zásad.  
  - [**Obrázek přerušování rozhraní API**](image-moderation-api.md): Toto rozhraní API použijte ke kontrole bitové kopie na obsah pro dospělé a zájem, detekovat text v bitové kopie pomocí funkce rozpoznávání optické rozpoznávání znaků (znaků) a zjistit řezy.
  - [**Vlastní image seznamu rozhraní API**](try-image-list-api.md): Toto rozhraní API použijte k porovnání vlastních seznamů obrázků, předem identifikovaných obsah, který je nebudete muset znovu klasifikovat.
  - [**Video přerušování rozhraní API**](video-moderation-api.md): používat toto rozhraní API ke kontrole videa pro potenciální obsah pro dospělé a zájem.
  - [**Zkontrolujte rozhraní API**](try-review-api-job.md): použití [úlohy](try-review-api-job.md), [recenze](try-review-api-review.md), a [pracovního postupu](try-review-api-workflow.md) na vytvoření a automatizovat pracovní postupy lidské v the smyčky v rámci operací Zkontrolujte nástroj.

## <a name="human-review-tool"></a>Nástroj pro recenze prováděné lidmi

Vaše předplatné obsahu moderátora zahrnuje předdefinovaná [lidské Zkontrolujte nástroj](Review-Tool-User-Guide/human-in-the-loop.md). Pomocí výše uvedených zkontrolujte API můžete vytvořit recenze text, obrázky a videa pro vaše lidského moderátorů přijímat konečné rozhodnutí.

![Zkontrolujte nástroj obsahu moderátora video](images/video-review-default-view.png)

## <a name="next-steps"></a>Další postup

Použití [rychlý Start](quick-start.md) začít pracovat s moderátora obsahu.
