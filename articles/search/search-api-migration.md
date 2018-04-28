---
title: Upgrade na rozhraní API služby Azure Search služby REST verze 2016-09-01 | Microsoft Docs
description: Upgrade na rozhraní API služby Azure Search služby REST verze 2016-09-01
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: ea901462677d42d90007a2130825bd3b382407f2
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Upgrade na rozhraní API služby Azure Search služby REST verze 2016-09-01
Pokud používáte verze 2015-02-28 nebo 2015-02-28 – Náhled [rozhraní REST API služby Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx), tento článek vám pomůže při upgradu aplikace k používání všeobecně dostupná verze rozhraní API 2016-09-01.

Verze 2016-09-01 rozhraní REST API obsahuje některé změny z předchozích verzí. Toto jsou většinou zpětně kompatibilní, takže měnili kód měli vyžadovat jen minimální úsilí, v závislosti na verzi, které jste používali před. V tématu [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak upravit svůj kód k použití nové verze rozhraní API.

> [!NOTE]
> Instanci služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete použít verzi, když je už nejnovějšího, ale doporučujeme migraci kód a použít nejnovější verzi.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>Co je nového ve verzi 2016-09-01
Verze 2016-09-01 je druhý všeobecně dostupná verze rozhraní API REST služby vyhledávání Azure. Nové funkce v této verzi rozhraní API:

* [Vlastní analyzátorů](https://aka.ms/customanalyzers), které umožňují převzít kontrolu nad tímto procesem převodu textu indexovanou a vyhledávat tokenů.
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) a [Azure Table Storage](search-howto-indexing-azure-tables.md) indexery, které vám umožní snadno importovat data ze služby Azure storage do Azure Search v plánu nebo na vyžádání.
* [Pole mapování](search-indexer-field-mappings.md), který slouží k úpravě indexery jak importovat data do Azure Search.
* Značky etag binárním rozsáhlým, která umožňuje aktualizovat definice indexy, indexery a zdroje dat způsobem bezpečných souběžnosti. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Pokud provádíte upgrade z verze 2015-02-28, pravděpodobně nebudete mít proveďte změny v kódu, jiné než, chcete-li změnit číslo verze. Pouze situace, ve kterých budete muset změnit kód jsou při:

* Váš kód selže, když nerozpoznané vlastnosti jsou vráceny v odpovědi rozhraní API. Ve výchozím nastavení je třeba vlastnosti, které nerozumí ignorovat vaší aplikace.
* Váš kód potrvají žádostí o rozhraní API a pokusí se přeposlat na novou verzi rozhraní API. Například tomu může dojít, pokud vaše aplikace bude pokračování tokeny vrácená z rozhraní API služby Search (Další informace vyhledejte `@search.nextPageParameters` v [referenční dokumentace rozhraní API pro vyhledávání](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)).

Pokud některý z těchto situacích se na vás vztahovat, budete muset odpovídajícím způsobem upravit svůj kód. V opačném žádné změny by měl být nutné, pokud chcete začít používat [nové funkce](#WhatsNew) verze 2016-09-01.

Pokud provádíte upgrade z verze 2015-02-28-Preview, výše platí také, ale také musí být vědět, že některé funkce preview nejsou dostupné ve verzi 2016-09-01:

* Podpora indexeru Azure Blob Storage pro soubory CSV a objekty BLOB obsahující pole JSON.
* Synonyma.
* "Informace jako to" dotazy

Pokud váš kód používá tyto funkce, nebudete moci upgradovat na 2016-09-01 bez odebrání vašeho využití z nich.

> [!IMPORTANT]
> Prosím mějte na paměti, verzi preview rozhraní API jsou určené pro testování a vyhodnocení a neměl by se používat v produkčním prostředí.
> 
> 

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o použití rozhraní API REST služby vyhledávání Azure, přečtěte si nedávno aktualizovaného [referenční dokumentace rozhraní API](https://msdn.microsoft.com/library/azure/dn798935.aspx) na webu MSDN.

Uvítáme vaše názory na Azure Search. Pokud narazíte na potíže, neváhejte nám požádat o pomoc na [fórum Azure Search MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) nebo [StackOverflow](http://stackoverflow.com/). Pokud dotaz se žádostí o službě Azure Search na StackOverflow, je třeba označit její `azure-search`.

Děkujeme za používání služby Azure Search.

