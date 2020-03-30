---
title: Práce s účty Azure Cosmos DB
description: Tento článek popisuje, jak vytvářet a používat účty Azure Cosmos. Zobrazuje také hierarchii prvků v účtu Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: d29ed68b2945b2473b33aa88176e6f5d832a0fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246900"
---
# <a name="work-with-azure-cosmos-account"></a>Práce s účtem Azure Cosmos

Azure Cosmos DB je plně spravovaná platforma jako služba (PaaS). Pokud chcete začít používat Azure Cosmos DB, měli byste nejprve vytvořit účet Azure Cosmos ve vašem předplatném Azure. Váš účet Azure Cosmos obsahuje jedinečný název DNS a můžete spravovat účet pomocí portálu Azure, Azure CLI nebo pomocí různých sad SDK specifických pro jazyk. Další informace najdete v tématu [jak spravovat svůj účet Azure Cosmos](how-to-manage-database-account.md).

Účet Azure Cosmos je základní jednotkou globální distribuce a vysoké dostupnosti. Pro globální distribuci dat a propustnost ve více oblastech Azure, můžete přidat a odebrat oblasti Azure do svého účtu Azure Cosmos kdykoliv. Účet Azure Cosmos můžete nakonfigurovat tak, aby měl jednu nebo více oblastí zápisu. Další informace najdete v tématu [jak přidat a odebrat oblasti Azure do účtu Azure Cosmos](how-to-manage-database-account.md). Výchozí úroveň [konzistence](consistency-levels.md) můžete nakonfigurovat na účtu Azure Cosmos. Azure Cosmos DB poskytuje komplexní smlouvy SLA zahrnující propustnost, latenci na 99 percentilu, konzistenci a vysokou dostupnost. Další informace naleznete [v tématu Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Chcete-li bezpečně spravovat přístup ke všem datům v rámci vašeho účtu Azure Cosmos, můžete použít [hlavní klíče](secure-access-to-data.md) přidružené k vašemu účtu. Chcete-li dále zabezpečit přístup k vašim datům, můžete nakonfigurovat [koncový bod služby Virtuální sítě](vnet-service-endpoint.md) a ip [bránu firewall](firewall-support.md) na vašem účtu Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Prvky v účtu Azure Cosmos

Kontejner Azure Cosmos je základní jednotkou škálovatelnosti. Můžete mít prakticky neomezenou zřízenou propustnost (RU/s) a úložiště na kontejneru. Azure Cosmos DB transparentně rozdělí váš kontejner pomocí klíče logického oddílu, který zadáte, aby bylo možné elasticky škálovat zřízenou propustnost a úložiště. Další informace najdete v [tématu práce s kontejnery a položky Azure Cosmos](databases-containers-items.md).

V současné době můžete vytvořit maximálně 100 účtů Azure Cosmos v rámci předplatného Azure. Jeden účet Azure Cosmos může prakticky spravovat neomezené množství dat a zřízenou propustnost. Chcete-li spravovat data a zřízenou propustnost, můžete vytvořit jednu nebo více databází Azure Cosmos pod svým účtem a v rámci této databáze můžete vytvořit jeden nebo více kontejnerů. Následující obrázek znázorňuje hierarchii prvků v účtu Azure Cosmos:

![Hierarchie účtu Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak spravovat svůj účet Azure Cosmos a další koncepty:

* [Jak spravovat svůj účet Azure Cosmos](how-to-manage-database-account.md)
* [Globální distribuce](distribute-data-globally.md)
* [Úrovně konzistence](consistency-levels.md)
* [Práce s kontejnery a položkami Azure Cosmos](databases-containers-items.md)
* [Koncový bod služby Virtuální sítě pro váš účet Azure Cosmos](vnet-service-endpoint.md)
* [IP brána firewall pro váš účet Azure Cosmos](firewall-support.md)
* [Jak přidat a odebrat oblasti Azure do svého účtu Azure Cosmos](how-to-manage-database-account.md)
* [SLA DB Služby Azure Cosmos](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
