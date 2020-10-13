---
title: Práce s účty Azure Cosmos DB
description: Tento článek popisuje, jak vytvářet a používat účty Azure Cosmos. Zobrazuje také hierarchii prvků v účtu Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/09/2020
ms.reviewer: sngun
ms.openlocfilehash: d01164552b1f3f10f592cedf80eafe57566bf09c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91661070"
---
# <a name="work-with-azure-cosmos-account"></a>Práce s účtem Azure Cosmos

Azure Cosmos DB je plně spravovaná platforma jako služba (PaaS). Pokud chcete začít používat Azure Cosmos DB, měli byste nejdřív ve svém předplatném Azure vytvořit účet Azure Cosmos. Váš účet Azure Cosmos obsahuje jedinečný název DNS a účet můžete spravovat pomocí Azure Portal, Azure CLI nebo pomocí různých sad SDK pro konkrétní jazyk. Další informace najdete v tématu [Správa účtu Azure Cosmos](how-to-manage-database-account.md).

Účet Azure Cosmos je základní jednotkou globální distribuce a vysokou dostupnost. Pro globální distribuci vašich dat a propustnosti napříč několika oblastmi Azure můžete kdykoli přidat a odebrat oblasti Azure na účet Azure Cosmos. Účet Azure Cosmos můžete nakonfigurovat tak, aby měl buď jednu, nebo více oblastí zápisu. Další informace najdete v tématu [jak přidat a odebrat oblasti Azure na účet Azure Cosmos](how-to-manage-database-account.md). Můžete nakonfigurovat výchozí úroveň [konzistence](consistency-levels.md) v účtu Azure Cosmos. Azure Cosmos DB poskytuje komplexní SLA včetně propustnosti, latence na 99 percentilu, konzistence a vysoké dostupnosti. Další informace najdete v tématu [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Pokud chcete bezpečně spravovat přístup ke všem datům v rámci vašeho účtu Azure Cosmos, můžete použít [primární klíče](secure-access-to-data.md) přidružené k vašemu účtu. Abyste mohli lépe zabezpečit přístup k vašim datům, můžete na svém účtu Azure Cosmos nakonfigurovat [koncový bod služby virtuální](vnet-service-endpoint.md) [sítě a IP adresu brány firewall](firewall-support.md) . 

## <a name="elements-in-an-azure-cosmos-account"></a>Prvky v účtu Azure Cosmos

Azure Cosmos Container je základní jednotkou škálovatelnosti. V kontejneru můžete mít prakticky neomezenou zřízenou propustnost (RU/s) a úložiště. Azure Cosmos DB transparentně rozdělí váš kontejner pomocí klíče logického oddílu, který zadáte, aby bylo možné elasticky škálovat zřízenou propustnost a úložiště. Další informace najdete v tématu [práce s kontejnery a položkami Azure Cosmos](databases-containers-items.md).

V současné době můžete v rámci předplatného Azure vytvořit maximálně 50 účtů Azure Cosmos (Jedná se o částečný limit, který se dá zvýšit prostřednictvím žádosti o podporu). Jeden účet Azure Cosmos může prakticky spravovat neomezený objem dat a zřízenou propustnost. Pokud chcete spravovat vaše data a zřízenou propustnost, můžete vytvořit jednu nebo více databází Azure Cosmos pod vaším účtem a v rámci této databáze. můžete vytvořit jeden nebo více kontejnerů. Následující obrázek znázorňuje hierarchii prvků v účtu Azure Cosmos:

:::image type="content" source="./media/account-overview/hierarchy.png" alt-text="Hierarchie účtu Azure Cosmos" border="false":::

## <a name="next-steps"></a>Další kroky

Naučte se spravovat účet Azure Cosmos a další koncepty:

* [Jak spravovat účet Azure Cosmos](how-to-manage-database-account.md)
* [Globální distribuce](distribute-data-globally.md)
* [Úrovně konzistence](consistency-levels.md)
* [Práce s kontejnery a položkami Azure Cosmos](databases-containers-items.md)
* [Koncový bod služby virtuální sítě pro účet Azure Cosmos](vnet-service-endpoint.md)
* [IP-brána firewall pro účet Azure Cosmos](firewall-support.md)
* [Postup přidání a odebrání oblastí Azure do účtu Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
