---
title: Upgrade na nejnovější verzi rozhraní API REST služby Azure Search | Microsoft Docs
description: Upgrade na nejnovější verzi rozhraní API REST služby Azure Search
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 3848f317fd6d760961756f132edf9cbcb5f431ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181966"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>Upgrade na nejnovější verzi rozhraní API REST služby Azure Search
Pokud používáte předchozí verzi [rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/), tento článek vám pomůže při upgradu vaší aplikace, aby používal nejnovější všeobecně dostupná verze rozhraní API, 2017. 11 11.

2017 verze 11 11 rozhraní REST API obsahuje některé změny z předchozích verzí. Toto jsou většinou zpětně kompatibilní, takže měnili kód měli vyžadovat jen minimální úsilí, v závislosti na verzi, které jste používali před. V tématu [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak upravit svůj kód k použití nové verze rozhraní API.

> [!NOTE]
> Instanci služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete použít verzi, když je už nejnovějšího, ale doporučujeme migraci kód a použít nejnovější verzi.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Co je nového ve verzi 2017. 11 11
Verze 2017. 11 11 je nejnovější všeobecně dostupná verze rozhraní API REST služby vyhledávání Azure. Nové funkce v této verzi rozhraní API:

* [Synonyma](search-synonyms.md). Nové funkce synonyma slouží k definování ekvivalenty a rozbalte oboru dotazu.
* [Podpora pro efektivní indexování objekty BLOB text](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). Nové `text` analýza režim pro Azure Blob indexery výrazně zvyšuje výkon indexování.
* [Služba Statistika rozhraní API](https://aka.ms/azure-search-stats). Zobrazte aktuální využití a omezení prostředků ve službě Azure Search s tímto novým rozhraním API.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Pokud provádíte upgrade z verze GA, 2015-02-28 nebo 2016-09-01, pravděpodobně nebudete mít proveďte změny v kódu, jiné než, chcete-li změnit číslo verze. Pouze situace, ve kterých budete muset změnit kód jsou při:

* Váš kód selže, když nerozpoznané vlastnosti jsou vráceny v odpovědi rozhraní API. Ve výchozím nastavení je třeba vlastnosti, které nerozumí ignorovat vaší aplikace.
* Váš kód potrvají žádostí o rozhraní API a pokusí se přeposlat na novou verzi rozhraní API. Například tomu může dojít, pokud vaše aplikace bude pokračování tokeny vrácená z rozhraní API služby Search (Další informace vyhledejte `@search.nextPageParameters` v [referenční dokumentace rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Pokud některý z těchto situacích se na vás vztahovat, budete muset odpovídajícím způsobem upravit svůj kód. V opačném žádné změny by měl být nutné, pokud chcete začít používat [nové funkce](#WhatsNew) verze 2017. 11 11.

Pokud provádíte upgrade z verze preview rozhraní api-version, výše platí také, ale také musí být vědět, že některé funkce preview nejsou dostupné ve verzi 2017. 11 11:

* Podpora indexeru Azure Blob Storage pro soubory CSV a objekty BLOB obsahující pole JSON.
* "Informace jako to" dotazy

Pokud váš kód používá tyto funkce, nebudete moci upgradovat 2017. 11 11 bez odebrání vašeho využití z nich.

> [!IMPORTANT]
> Prosím mějte na paměti, verzi preview rozhraní API jsou určené pro testování a vyhodnocení a neměl by se používat v produkčním prostředí.
> 
> 

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o použití rozhraní API REST služby vyhledávání Azure, přečtěte si nedávno aktualizovaného [referenční dokumentace rozhraní API](https://docs.microsoft.com/rest/api/searchservice/) na webu MSDN.

Uvítáme vaše názory na Azure Search. Pokud narazíte na potíže, neváhejte nám požádat o pomoc na [fórum Azure Search MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) nebo [StackOverflow](http://stackoverflow.com/). Pokud dotaz se žádostí o službě Azure Search na StackOverflow, je třeba označit její `azure-search`.

Děkujeme za používání služby Azure Search.

