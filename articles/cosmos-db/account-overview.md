---
title: Práce s účty služby Azure Cosmos DB
description: Tento článek popisuje, jak vytvořit a používat účty Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: c0e97eed0a4028fdd9d2254fd9472dc3141d607a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239834"
---
# <a name="work-with-azure-cosmos-account"></a>Práce s účtem Azure Cosmos

Azure Cosmos DB je plně spravovanou platformu as-a-service (PaaS). Pokud chcete začít používat Azure Cosmos DB, by měl nejdřív vytvořit účet Azure Cosmos ve vašem předplatném Azure. Váš účet Azure Cosmos obsahuje jedinečný název DNS a účet můžete spravovat pomocí webu Azure portal, rozhraní příkazového řádku Azure nebo pomocí jiné sady SDK pro konkrétní jazyk. Další informace najdete v tématu [Správa účtu Azure Cosmos](how-to-manage-database-account.md).

Účet Azure Cosmos je základní jednotkou globální distribuci a vysokou dostupnost. Globální distribuce dat a propustnost nad několika oblastmi Azure, můžete přidávat a odebírat oblastí Azure ke svému účtu Azure Cosmos v každém okamžiku. Můžete nakonfigurovat svůj účet Azure Cosmos a mít jeden nebo více oblastí zápisu. Další informace najdete v tématu [postup přidání nebo odebrání oblastí Azure na účtu Azure Cosmos](how-to-manage-database-account.md). Můžete nakonfigurovat [výchozí konzistence](consistency-levels.md) úrovně na účet služby Azure Cosmos. Azure Cosmos DB poskytuje komplexní smlouvy SLA, včetně propustnosti, latenci na úrovni 99. percentilu, konzistence a vysokou dostupnost. Další informace najdete v tématu [smlouvách SLA v Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Pokud chcete zabezpečeně spravovat přístup ke všem datům v rámci svého účtu Azure Cosmos, můžete použít [hlavní klíče](secure-access-to-data.md) spojené s vaším účtem. Další zabezpečit přístup k vašim datům, můžete nakonfigurovat [koncový bod služby virtuální sítě](vnet-service-endpoint.md) a [brány firewall protokolu IP](firewall-support.md) na vašem účtu Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Prvky v účtu služby Azure Cosmos

Kontejner Azure Cosmos DB je základní jednotkou škálovatelnosti. V kontejneru můžete mít prakticky neomezený počet zřízená propustnost (RU/s) a služby úložiště. Azure Cosmos DB transparentně oddíly kontejneru pomocí klíče logický oddíl, který zadáte, aby bylo možné Elasticky škálovat propustnost a úložiště. Další informace najdete v tématu [práci s kontejnery Azure Cosmos a položky](databases-containers-items.md).

V současné době můžete vytvořit maximálně 100 účty služby Azure Cosmos v rámci předplatného Azure. Jeden účet Azure Cosmos můžete spravovat prakticky neomezený objem dat a zřízené propustnosti. Ke správě dat a zřízené propustnosti, můžete vytvořit jednu nebo více databází Azure Cosmos v rámci vašeho účtu a v rámci této databáze, můžete vytvořit jeden nebo více kontejnerů. Následující obrázek ukazuje hierarchii elementů v rámci účtu Azure Cosmos:

![Hierarchie účtu Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Další postup

Další informace o správě vašeho účtu Azure Cosmos a dalších konceptech:

* [Postupy spravovat váš účet Azure Cosmos](how-to-manage-database-account.md)
* [Globální distribuce](distribute-data-globally.md)
* [Úrovně konzistence](consistency-levels.md)
* [Práce s kontejnery Azure Cosmos a položek](databases-containers-items.md)
* [Koncový bod služby virtuální sítě pro váš účet Azure Cosmos](vnet-service-endpoint.md)
* [Brány firewall protokolu IP pro váš účet Azure Cosmos](firewall-support.md)
* [Postup přidání nebo odebrání oblastí Azure na účtu Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
