---
title: Správa koncových bodů virtuální sítě – Azure Portal-Azure Database for MariaDB
description: Vytváření a správa koncových bodů a pravidel služby VNet Azure Database for MariaDB pomocí Azure Portal
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5eaa7821c61010b322d8f9032c439df28c297f3d
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540871"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Vytváření a správa koncových bodů služby VNet Azure Database for MariaDB a pravidel virtuální sítě pomocí Azure Portal

Pravidla a koncové body služeb virtuální sítě rozšiřují privátní adresní prostor virtuální sítě na server Azure Database for MariaDB. Přehled koncových bodů služby virtuální sítě Azure Database for MariaDB, včetně omezení, najdete v tématu [koncové body služby virtuální sítě v Azure Database for MariaDB serveru](concepts-data-access-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastech pro Azure Database for MariaDB.

> [!NOTE]
> Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Vytvoření pravidla virtuální sítě a povolení koncových bodů služby

1. Na stránce serveru MariaDB v části Nastavení klikněte na **zabezpečení připojení** . tím otevřete podokno zabezpečení připojení pro Azure Database for MariaDB.

2. Zajistěte, aby byl ovládací prvek povolení přístupu ke službám Azure nastavený na **vypnuto**.

> [!Important]
> Pokud ho nastavíte na ZAPNUTo, Server databáze Azure MariaDB přijme komunikaci ze všech podsítí. Ponechání ovládacího prvku na ZAPNUTo může být nadměrný přístup z hlediska zabezpečení. Funkce koncového bodu služby Microsoft Azure Virtual Network, v koordinaci s funkcí pravidla virtuální sítě Azure Database for MariaDB, společně může snížit oblast zabezpečení.

3. Potom klikněte na **+ Přidat existující virtuální síť**. Pokud nemáte existující virtuální síť, můžete ji vytvořit kliknutím na **+ vytvořit novou virtuální síť** . Další informace najdete v tématu [rychlý Start: vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md)

   ![Azure Portal – kliknutí na zabezpečení připojení](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Zadejte název pravidla virtuální sítě, vyberte předplatné, virtuální síť a název podsítě a pak klikněte na **Povolit**. Tím se automaticky povolí koncové body služby virtuální sítě v podsíti s použitím značky služby **Microsoft. SQL** .

   ![Azure Portal – konfigurace virtuální sítě](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

   Koncové body služby je možné konfigurovat na virtuálních sítích nezávisle na uživateli s oprávněním k zápisu do virtuální sítě.
    
   Aby bylo možné zabezpečit prostředky služeb Azure pro virtuální síť, musí mít uživatel pro přidávané podsítě oprávnění k Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.
    
   Další informace o [předdefinovaných rolích](../role-based-access-control/built-in-roles.md) a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md).
    
   Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). Zajistěte, aby oba odběry měly zaregistrovaný poskytovatel prostředků **Microsoft. SQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

   > [!IMPORTANT]
   > Před konfigurací koncových bodů služby se důrazně doporučuje přečíst si tento článek o konfiguracích koncových bodů a požadavcích služby. **Koncový bod služby Virtual Network:** [Koncový bod služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft. SQL** , který odkazuje na službu Azure s názvem SQL Database. Tato značka služby se vztahuje také na služby Azure SQL Database, Azure Database for MariaDB, PostgreSQL a MySQL. Je důležité si uvědomit, že pokud použijete značku služby **Microsoft. SQL** pro koncový bod služby virtuální sítě, nakonfiguruje se provoz koncového bodu služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB a Azure Database for MySQL serverů v podsíti.
   > 

5. Po povolení klikněte na **OK** a uvidíte, že koncové body služby virtuální sítě jsou povolené spolu s pravidlem virtuální sítě.

   ![Povolené koncové body služby virtuální sítě a vytvořené pravidlo virtuální sítě](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Další kroky
- Další informace o [konfiguraci SSL na Azure Database for MariaDB](howto-configure-ssl.md)
- Podobně můžete skript pro [Povolení koncových bodů služby virtuální sítě a vytvoření pravidla virtuální sítě pro Azure Database for MariaDB pomocí Azure CLI](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md