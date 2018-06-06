---
title: Vytvářet a spravovat databáze Azure pro koncové body služby PostgreSQL virtuální sítě a pravidla pomocí portálu Azure | Microsoft Docs
description: Vytvářet a spravovat databáze Azure pro koncové body služby PostgreSQL virtuální sítě a pravidla pomocí portálu Azure
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 8199bd98b9ae091ff2b27efa8334e8e763879359
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757361"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Vytvářet a spravovat databáze Azure pro koncové body služby PostgreSQL virtuální sítě a pravidla virtuální sítě pomocí portálu Azure
Koncové body služby virtuální sítě (VNet) a pravidla rozšířit privátního adresního prostoru virtuální sítě k vaší databázi Azure pro PostgreSQL server. Přehled Azure databáze pro koncové body služby PostgreSQL virtuální sítě, včetně omezení, najdete v části [databáze Azure pro koncové body služby serveru VNet PostgreSQL](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve verzi public preview ve všech oblastech podporovaných pro databázi Azure pro PostgreSQL.

> [!NOTE]
> Podpora pro koncové body služby virtuální sítě je pouze pro obecné účely a paměťově optimalizované servery.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Vytvoření pravidla virtuální sítě a povolit koncové body služby na portálu Azure

1. Na stránce server PostgreSQL, v části nastavení klikněte na **zabezpečení připojení** otevřete podokno zabezpečení připojení pro databázi Azure pro PostgreSQL. Klikněte na tlačítko na **+ přidat existující virtuální síť**. Pokud nemáte existující virtuální síť můžete kliknout na **+ vytvořit novou virtuální síť** k jeho vytvoření. V tématu [rychlý start: vytvoření virtuální sítě pomocí portálu Azure](../virtual-network/quick-create-portal.md)

   ![Portál Azure – klikněte na možnost zabezpečení připojení](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Zadejte název pravidla virtuální sítě, vyberte předplatné, virtuální síť a název podsítě a pak klikněte na tlačítko **povolit**. To umožňuje automaticky koncové body služby virtuální sítě na podsíť pomocí **Microsoft.SQL** služby značky.

   ![Portál Azure – konfigurace virtuální sítě](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Důrazně doporučujeme před konfigurací koncové body služby přečtěte si tento článek o konfigurace koncového bodu služby a důležité informace. **Koncový bod služby virtuální sítě:** A [koncový bod služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejichž hodnoty vlastností obsahovat jeden nebo více názvy typů formální služby Azure. Koncové body služby virtuální síť použít název typu služby **Microsoft.Sql**, které odkazuje na službu Azure s názvem databáze SQL. Tato značka služby platí také pro Azure SQL Database, databáze Azure pro služby PostgreSQL a MySQL. Je důležité si uvědomit, při použití **Microsoft.Sql** služby značky pro koncový bod služby virtuální sítě konfiguruje přenosů koncový bod služby pro všechny služby Azure Database, včetně Azure SQL Database, databáze Azure pro PostgreSQL a Azure databáze MySQL servery na podsíti. 
   > 

3. Jakmile bude povoleno, klikněte na tlačítko **OK** a zobrazí se, že jsou povolené koncové body služby virtuální sítě spolu s pravidlem, virtuální sítě.

   ![Koncové body služby virtuální síť, která je povolena a vytvořit pravidlo virtuální sítě](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Další postup
- Podobně můžete skript pro [povolit virtuální síť koncové body služby a vytvořit pravidlo, virtuální sítě pro databázi Azure pro použití Azure CLI PostgreSQL](howto-manage-vnet-using-cli.md).
- Pomoc při připojování k databázi Azure pro PostgreSQL server najdete v tématu [knihovny připojení pro databázi Azure pro PostgreSQL](./concepts-connection-libraries.md)
