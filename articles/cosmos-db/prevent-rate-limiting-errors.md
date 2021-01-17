---
title: Zabraňte chybám omezujícím rychlost Azure Cosmos DB rozhraní API pro operace MongoDB.
description: Naučte se, jak zabránit tomu, aby vaše rozhraní Azure Cosmos DB API pro operace MongoDBy nedocházelo k chybám při použití funkce SSR (opakování na straně serveru).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540542"
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


## <a name="next-steps"></a>Další kroky

Další informace o řešení běžných chyb najdete v tomto článku:

* [Řešení běžných problémů v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-troubleshoot.md)
