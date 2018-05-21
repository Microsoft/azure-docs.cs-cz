---
title: Verze rozhraní API 2016 modulu Azure Search služby REST-09-01-Preview | Microsoft Docs
description: Azure Search služby REST verze rozhraní API 2016-09-01-Preview zahrnuje povolenými experimentálními funkcemi, jako je například moreLikeThis hledání.
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 04/18/2018
ms.author: nateko
ms.openlocfilehash: e5cd7fcd0c853f03dbafb4d95b8459dcc83aecdf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Rozhraní API REST služby vyhledávání systému Azure: Verze 2016-09-01-Preview
V tomto článku jsou uvedené funkce verze preview `api-version=2016-09-01-Preview`. Tato verze preview rozšiřuje předchozí verze všeobecně dostupná, [rozhraní api-version = 2016-09-01](https://docs.microsoft.com/rest/api/searchservice), tím, že poskytuje následující povolenými experimentálními funkcemi:

* [`moreLikeThis` parametr dotazu](search-more-like-this.md) najít dokumenty, které se vztahují k určitému dokumentu. Tato funkce byla v dřívějších verzích Preview. Pokud jste volající toto rozhraní API se starší verzí rozhraní api, můžete nadále používat tuto verzi.


## <a name="how-to-call-a-preview-api"></a>Jak volat preview rozhraní API

Následující příklad ukazuje, jak ve verzi preview verze rozhraní api je uveden v provedení dalších jako tento dotaz.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Funkce Preview jsou k dispozici pro testování a experimenty s cílem shromažďování zpětné vazby a mohou podléhat změnám. **Důrazně nedoporučujeme používání preview rozhraní API v produkční aplikace.**

Služba Azure Search je k dispozici v několika verzích. Naleznete [verze služby vyhledávání](https://docs.microsoft.com/azure/search/search-api-versions) podrobnosti.
