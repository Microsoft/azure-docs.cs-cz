---
title: Zabraňte chybám omezujícím rychlost Azure Cosmos DB rozhraní API pro operace MongoDB.
description: Naučte se, jak zabránit tomu, aby vaše rozhraní Azure Cosmos DB API pro operace MongoDBy nedocházelo k chybám při použití funkce SSR (opakování na straně serveru).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: e1ccf55d38a9a3a5a1d0a3622c90dd7b51e5e477
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258486"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Zabránění chybám v omezení rychlosti pro Azure Cosmos DB rozhraní API pro operace MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Rozhraní Azure Cosmos DB API pro operace MongoDB může selhat s chybami při omezení četnosti (16500/429), pokud překročí limit propustnosti kolekce (ru). 

Můžete povolit funkci pro opakování na straně serveru (SSR) a nechat server opakovat tyto operace automaticky. Žádosti se budou opakovat po krátké prodlevě pro všechny kolekce ve vašem účtu. Tato funkce je vhodnou alternativou pro zpracování chyb v klientské aplikaci, které omezují rychlost.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Přejděte na rozhraní Azure Cosmos DB API pro účet MongoDB.

1. V části **Nastavení** otevřete podokno **funkce** .

1. Vyberte možnost **Opakovat na straně serveru**.

1. Kliknutím na **Povolit** Povolte tuto funkci pro všechny kolekce ve vašem účtu.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Snímek obrazovky funkce opakování na straně serveru pro rozhraní Azure Cosmos DB API pro MongoDB":::

## <a name="use-the-azure-cli"></a>Použití Azure CLI

1. Ověřte, jestli je pro váš účet už povolený SSR:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Povolit** SSR pro všechny kolekce v databázovém účtu. Aby se tato změna projevila, může trvat až 15 minut.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
Následující příkaz **zakáže** SSR pro všechny kolekce v databázovém účtu. Aby se tato změna projevila, může trvat až 15 minut.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
* Jak se budou žádosti opakovat?
    * Žádosti se opakují nepřetržitě (přes a znovu) až do dosažení časového limitu 60 sekund. Pokud je dosažen časový limit, klient obdrží [výjimku ExceededTimeLimit (50)](mongodb-troubleshoot.md).
*  Jak můžu monitorovat účinky SSR?
    *  V podokně Cosmos DB metriky můžete zobrazit chyby s omezením četnosti (429s), které se znovu vyzkoušely na straně serveru. Mějte na paměti, že tyto chyby nejdou klientovi, když je povolený SSR, protože se zpracovávají a znovu vyzkoušela strana serveru. 
    *  V protokolech [prostředků Cosmos DB](cosmosdb-monitor-resource-logs.md)můžete vyhledat položky protokolu obsahující "estimatedDelayFromRateLimitingInMilliseconds".
*  Bude SSR mít vliv na úroveň konzistence?
    *  SSR nemá vliv na konzistenci žádosti. Požadavky se znovu vyzkoušely na straně serveru, pokud se jedná o omezený počet (s chybou 429). 
*  Má SSR vliv na jakýkoli typ chyby, kterou může můj klient obdržet?
    *  No, SSR má vliv jenom na chyby omezení četnosti (429s), a to tak, že se na straně serveru opakuje. Tato funkce zabrání v nutnosti zpracovávat v klientské aplikaci chyby s omezením rychlosti. Všechny [ostatní chyby](mongodb-troubleshoot.md) budou přecházet do klienta. 

## <a name="next-steps"></a>Další kroky

Další informace o řešení běžných chyb najdete v tomto článku:

* [Řešení běžných problémů v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-troubleshoot.md)
