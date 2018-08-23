---
title: Vytvoření a správě Azure Database for PostgreSQL VNet koncové body služby a pravidla pomocí webu Azure portal | Dokumentace Microsoftu
description: Vytvoření a správě Azure Database for PostgreSQL VNet koncové body služby a pravidla pomocí webu Azure portal
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 08/15/2018
ms.openlocfilehash: af5df507bd7a825ddfaaa7518691cb5fbec34d47
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42057178"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Vytvoření a správě Azure Database for PostgreSQL VNet koncové body služby a pravidla virtuální sítě pomocí webu Azure portal
Koncové body služeb virtuální sítě (VNet) a pravidla rozšiřují privátní adresní prostor virtuální sítě pro váš server Azure Database for PostgreSQL. Přehled služby Azure Database pro koncové body služby virtuální sítě PostgreSQL, včetně omezení, naleznete v tématu [– Azure Database for koncové body služby virtuální sítě serveru PostgreSQL](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastí pro službu Azure Database for PostgreSQL.

> [!NOTE]
> Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Vytvořit pravidlo virtuální sítě a povolit koncové body služby na webu Azure Portal

1. Na stránce v části Nastavení serveru PostgreSQL záhlaví, klikněte na tlačítko **zabezpečení připojení** a otevřete tak podokno zabezpečení připojení pro službu Azure Database for PostgreSQL. Pak klikněte na **+ přidání existující virtuální sítě**. Pokud nemáte existující virtuální síť můžete kliknout na **+ vytvořit novou virtuální síť** k jejímu vytvoření. Zobrazit [rychlý start: vytvoření virtuální sítě pomocí webu Azure portal](../virtual-network/quick-create-portal.md)

   ![Portál Azure – zabezpečení připojení klikněte na](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Zadejte název pravidla virtuální sítě, vyberte předplatné, virtuální síť a název podsítě a pak klikněte na tlačítko **povolit**. To umožňuje automaticky koncové body služby virtuální sítě na podsíť pomocí **Microsoft.SQL** značka služby.

   ![Azure portal – konfigurace virtuální sítě](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Důrazně doporučujeme v tomto článku o konfigurace koncového bodu služby a důležité informace před konfigurací koncových bodů služby. **Koncový bod služby virtuální sítě:** A [koncový bod služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejichž hodnoty vlastností zahrnují jeden nebo víc názvů typu formální služby Azure. Koncové body služeb virtuální sítě použít název typu služby **Microsoft.Sql**, která odkazuje na službu Azure SQL Database s názvem. Tuto značku služby platí také pro Azure SQL Database, Azure Database for PostgreSQL a MySQL. Je důležité při použití zásad skupiny pamatujte **Microsoft.Sql** značka služby do koncového bodu služby virtuální sítě konfiguruje provoz koncový bod služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Servery Azure Database for MySQL v podsíti. 
   > 

3. Jakmile povolíte službu, klikněte na tlačítko **OK** a uvidíte, že jsou povolené koncové body služby virtuální sítě spolu s pravidlo virtuální sítě.

   ![Povolené koncové body služeb virtuální sítě a vytvoří pravidlo virtuální sítě](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Další postup
- Podobně můžete používat skripty pro [koncové body služby povolit virtuální sítě a vytvořit pravidlo virtuální sítě pro službu Azure Database for PostgreSQL pomocí Azure CLI](howto-manage-vnet-using-cli.md).
- Pomoc při připojování k serveru Azure Database for PostgreSQL, naleznete v tématu [připojení knihoven pro službu Azure Database for PostgreSQL](./concepts-connection-libraries.md)
