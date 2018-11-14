---
title: Práce s účty služby Azure Cosmos DB
description: Tento článek popisuje, jak vytvořit a používat účty Azure Cosmos DB
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: dea343dee65ab66d52b431614fd334fd6e380f50
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628923"
---
# <a name="working-with-azure-cosmos-db-accounts"></a>Práce s účty služby Azure Cosmos DB

Azure Cosmos DB je plně spravovanou platformu as-a-service (PaaS). Pokud chcete začít používat Azure Cosmos DB, by měl nejdřív vytvořit účet služby Azure Cosmos DB ve vašem předplatném Azure. Váš účet Azure Cosmos obsahuje jedinečný název DNS a účet můžete spravovat pomocí webu Azure portal, rozhraní příkazového řádku Azure nebo pomocí jiné sady SDK pro konkrétní jazyk. Další informace najdete v tématu [Správa účtu Azure Cosmos](how-to-manage-database-account.md).

Účet služby Azure Cosmos DB je základní jednotkou globální distribuci a vysokou dostupnost. Globální distribuce dat a propustnost nad několika oblastmi Azure, můžete přidávat a odebírat oblastí Azure ke svému účtu Azure Cosmos v každém okamžiku. Můžete nakonfigurovat svůj účet Azure Cosmos a mít jeden nebo více oblastí zápisu. Další informace najdete v tématu [postup přidání nebo odebrání oblastí Azure na účtu Azure Cosmos](how-to-manage-database-account.md). Můžete nakonfigurovat [výchozí konzistence](consistency-levels.md) úrovně na účet služby Azure Cosmos. Azure Cosmos DB poskytuje komplexní smlouvy SLA, včetně propustnosti, latenci na úrovni 99. percentilu, konzistence a vysokou dostupnost. Další informace najdete v tématu [smlouvách SLA v Azure Cosmos DB](https://azure.microsoft.com/en-us/support/legal/sla/cosmos-db/v1_2/).

Bezpečně spravovat přístup ke všem datům v rámci svého účtu Azure Cosmos, můžete použít hlavní klíče spojené s vaším účtem. Na další zabezpečení přístupu ke svým datům můžete nakonfigurovat koncový bod služby virtuální sítě a brány firewall protokolu IP na vašem účtu Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Prvky v účtu služby Azure Cosmos

Kontejner Azure Cosmos DB je základní jednotkou škálovatelnosti. V kontejneru můžete mít prakticky neomezený počet zřízená propustnost (RU/s) a služby úložiště. Azure Cosmos DB transparentně oddíly kontejneru pomocí klíče logický oddíl, který zadáte, aby bylo možné Elasticky škálovat propustnost a úložiště. Další informace najdete v tématu [práci s kontejnery Azure Cosmos a položky](databases-containers-items.md).

V současné době můžete vytvořit maximálně 100 účty služby Azure Cosmos v rámci předplatného Azure. Jeden účet Azure Cosmos můžete spravovat prakticky neomezený objem dat a zřízené propustnosti. Ke správě dat a zřízené propustnosti, můžete vytvořit jednu nebo více databází Azure Cosmos v rámci vašeho účtu a v rámci této databáze, můžete vytvořit jeden nebo více kontejnerů. Následující obrázek ukazuje hierarchii elementů v rámci účtu Azure Cosmos:

![Hierarchie účtu Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Další postup

Teď můžete přejít k zjistěte, jak spravovat váš účet Azure Cosmos nebo jiných konceptům spojeným s Azure Cosmos DB najdete v článku:

* [Postupy spravovat váš účet Azure Cosmos](how-to-manage-database-account.md)
* [Globální distribuce](distribute-data-globally.md)
* [Úrovně konzistence](consistency-levels.md)
* [Práce s kontejnery Azure Cosmos a položek](databases-containers-items.md)
* [Koncový bod služby virtuální sítě pro váš účet Azure Cosmos](firewall-support.md)
* [Brány firewall protokolu IP pro váš účet Azure Cosmos](vnet-service-endpoint.md)
* [Postup přidání nebo odebrání oblastí Azure na účtu Azure Cosmos](how-to-manage-database-account.md)
* [Smlouvy o úrovni služeb Azure Cosmos DB](https://azure.microsoft.com/en-us/support/legal/sla/cosmos-db/v1_2/)
