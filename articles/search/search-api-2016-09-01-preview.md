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
ms.openlocfilehash: 8eae54c912711a11c015737903b6898b98fd5159
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Rozhraní API REST služby vyhledávání systému Azure: Verze 2016-09-01-Preview
Tento článek je referenční dokumentaci k nástroji pro `api-version=2016-09-01-Preview`. Tato verze preview rozšiřuje všeobecně dostupná verzí [rozhraní api-version = 2016-09-01](https://docs.microsoft.com/rest/api/searchservice), tím, že poskytuje následující povolenými experimentálními funkcemi:

* [`moreLikeThis` parametr dotazu](search-more-like-this.md) najít dokumenty, které se vztahují k určitému dokumentu.

Přesvědčte se, zda Pokud chcete cílit na verzi preview rozhraní API `api-version=2016-09-01-Preview` můžete vyzkoušet tyto povolenými experimentálními funkcemi. Následující příklad ukazuje, jak ve verzi preview verze rozhraní api je uveden v provedení dalších jako tento dotaz.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Funkce Preview jsou k dispozici pro testování a experimenty s cílem shromažďování zpětné vazby a mohou podléhat změnám. **Důrazně nedoporučujeme používání preview rozhraní API v produkční aplikace.**

Služba Azure Search je k dispozici v několika verzích. Naleznete [verze služby vyhledávání](https://docs.microsoft.com/azure/search/search-api-versions) podrobnosti.
