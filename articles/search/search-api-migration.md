---
title: Upgradujte na nejnovější verzi rozhraní REST API služby Azure Search – Azure Search
description: Zkontrolovat rozdíly ve verzích rozhraní API a další akce, které jsou potřeba k migraci existujícího kódu na nejnovější verzi rozhraní REST API služby Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 23003859b9a75fb986fe65f5528004f3dd150f9b
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633040"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Upgradujte na nejnovější verzi rozhraní REST API služby Azure Search
Pokud používáte předchozí verzi [rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/), tento článek vám pomůže při upgradu aplikace použít nejnovější dostupná verze rozhraní API 2017-11-11.

Verze 2017-11-11 rozhraní REST API obsahuje některé změny z předchozích verzí. Toto jsou většinou zpětně kompatibilní, takže změna kódu by měla trvat pouze minimálním úsilím, v závislosti na verzi, kterou jste používali před. Zobrazit [kroky pro upgrade](#UpgradeSteps) pokyny o tom, jak změnit váš kód k použití nové verze rozhraní API.

> [!NOTE]
> Instance služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete nadále používat verzi, pokud již není nejnovější, ale doporučujeme migrovat kód Refaktorovat pro použití na nejnovější verzi.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Co je nového ve verzi 2017-11-11
Verze 2017-11-11 je nejnovější dostupná verze rozhraní REST API Azure Search Service. Mezi nové funkce v této verzi rozhraní API patří:

* [Synonyma](search-synonyms.md). Nové funkce synonym umožňuje definovat ekvivalentní termíny a rozbalte obor dotazu.
* [Podpora pro efektivní indexování objektů BLOB text](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). Nové `text` analýze režimu pro indexování objektů Blob v Azure výrazně zlepšuje výkon indexování.
* [Statistika API služby](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Zobrazte aktuální využití a omezení prostředků ve službě Azure Search pomocí tohoto nového rozhraní API.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroky pro upgrade
Pokud provádíte upgrade z verze GA, 2015-02-28 nebo 2016-09-01, pravděpodobně není nutné provádět žádné změny kódu, jiné než Chcete-li změnit číslo verze. Jsou pouze situací, ve kterých může být nutné změnit kód, pokud:

* Vašemu kódu nepodaří při nerozpoznaný vlastnosti jsou vráceny v odpovědi rozhraní API. Ve výchozím nastavení vaše aplikace by měl Ignorovat vlastnosti, které není srozumitelný.
* Váš kód nevyřeší požadavků rozhraní API a pokusí se přeposlat na novou verzi rozhraní API. Například k tomu může dojít, pokud vaše aplikace bude pokračování tokeny vrácená z rozhraní API pro vyhledávání (Další informace vyhledejte `@search.nextPageParameters` v [Reference k rozhraní API hledání](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Pokud se na vás vztahovat jeden z těchto situací, budete muset změnit váš kód odpovídajícím způsobem. V opačném případě žádné změny by měl být nezbytné, pokud chcete začít používat [nové funkce](#WhatsNew) verze 2017-11-11.

Pokud provádíte upgrade z verze preview rozhraní api-version, výše uvedené platí také, ale také musí být vědomi, že některé funkce ve verzi preview nejsou k dispozici ve verzi 2017-11-11:

* Podpora indexeru Azure Blob Storage pro soubory CSV a objekty BLOB, který obsahuje pole JSON.
* "Informace následujícím způsobem" dotazy

Pokud váš kód používá tyto funkce, nebude možné upgradovat na 2017-11-11 bez odebrání využití z nich.

> [!IMPORTANT]
> Prosím mějte na paměti, ve verzi preview rozhraní API jsou určené pro testování a hodnocení a neměli byste používat v produkčním prostředí.
> 
> 

## <a name="conclusion"></a>Závěr
Pokud potřebujete další podrobnosti o použití rozhraní REST API Azure Search Service, najdete v článku nedávno aktualizovanou [Reference k rozhraní API](https://docs.microsoft.com/rest/api/searchservice/) na webové stránce MSDN.

Vítáme váš názor na Azure Search. Pokud narazíte na problémy, neváhejte nás požádat o pomoc na [fórum Azure Search na webu MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) nebo [StackOverflow](https://stackoverflow.com/). Pokud jste položení otázky o Azure Search na StackOverflow, ujistěte se, že ji označit `azure-search`.

Děkujeme vám za použití Azure Search.

