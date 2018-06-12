---
title: Vytvářet a spravovat databáze Azure pro koncové body služby MySQL virtuální sítě a pravidla pomocí portálu Azure | Microsoft Docs
description: Vytvářet a spravovat databáze Azure pro koncové body služby MySQL virtuální sítě a pravidla pomocí portálu Azure
services: mysql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7520868fd6bd349043ad2c53e62de5db978db8b1
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267216"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Vytvářet a spravovat databáze Azure pro koncové body služby MySQL virtuální sítě a pravidla virtuální sítě pomocí portálu Azure
Koncové body služby virtuální sítě (VNet) a pravidla rozšířit privátního adresního prostoru virtuální sítě k vaší databázi Azure pro server databáze MySQL. Přehled Azure databáze pro koncové body služby MySQL virtuální sítě, včetně omezení, najdete v části [databáze Azure pro koncové body služby serveru VNet MySQL](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve verzi public preview ve všech oblastech podporovaných pro databázi Azure pro databázi MySQL.

> [!NOTE]
> Podpora pro koncové body služby virtuální sítě je pouze pro obecné účely a paměťově optimalizované servery.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Vytvoření pravidla virtuální sítě a povolit koncové body služby na portálu Azure

1. Na stránce server MySQL, v části nastavení klikněte na **zabezpečení připojení** otevřete podokno zabezpečení připojení pro databázi Azure pro databázi MySQL. Klikněte na tlačítko na **+ přidat existující virtuální síť**. Pokud nemáte existující virtuální síť můžete kliknout na **+ vytvořit novou virtuální síť** k jeho vytvoření. V tématu [rychlý start: vytvoření virtuální sítě pomocí portálu Azure](../virtual-network/quick-create-portal.md)

   ![Portál Azure – klikněte na možnost zabezpečení připojení](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Zadejte název pravidla virtuální sítě, vyberte předplatné, virtuální síť a název podsítě a pak klikněte na tlačítko **povolit**. To umožňuje automaticky koncové body služby virtuální sítě na podsíť pomocí **Microsoft.SQL** služby značky.

   ![Portál Azure – konfigurace virtuální sítě](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Důrazně doporučujeme před konfigurací koncové body služby přečtěte si tento článek o konfigurace koncového bodu služby a důležité informace. **Koncový bod služby virtuální sítě:** A [koncový bod služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejichž hodnoty vlastností obsahovat jeden nebo více názvy typů formální služby Azure. Koncové body služby virtuální síť použít název typu služby **Microsoft.Sql**, které odkazuje na službu Azure s názvem databáze SQL. Tato značka služby platí také pro Azure SQL Database, databáze Azure pro služby PostgreSQL a MySQL. Je důležité si uvědomit, při použití **Microsoft.Sql** služby značky pro koncový bod služby virtuální sítě konfiguruje přenosů koncový bod služby pro všechny služby Azure Database, včetně Azure SQL Database, databáze Azure pro PostgreSQL a Azure databáze MySQL servery na podsíti. 
   > 

3. Jakmile bude povoleno, klikněte na tlačítko **OK** a zobrazí se, že jsou povolené koncové body služby virtuální sítě spolu s pravidlem, virtuální sítě.

   ![Koncové body služby virtuální síť, která je povolena a vytvořit pravidlo virtuální sítě](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Další postup
- Podobně můžete skript pro [povolit virtuální síť koncové body služby a vytvořit pravidlo, virtuální sítě pro databázi Azure pro databázi MySQL pomocí rozhraní příkazového řádku Azure](howto-manage-vnet-using-cli.md).
- Pomoc při připojování k databázi Azure pro server databáze MySQL, najdete v tématu [knihovny připojení pro databázi Azure pro databázi MySQL](./concepts-connection-libraries.md)
