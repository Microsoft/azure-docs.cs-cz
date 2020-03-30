---
title: Správa koncových bodů virtuální sítě – portál Azure – databáze Azure pro MariaDB
description: Vytváření a správa azure databáze pro koncové body a pravidla virtuální sítě MariaDB pomocí portálu Azure
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 61a8337536f55ceda9bef5b7eaa67a37644d2aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530593"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Vytvoření a správa databáze Azure pro koncové body virtuální sítě MariaDB a pravidla virtuální sítě pomocí portálu Azure

Pravidla a koncové body služeb virtuální sítě rozšiřují privátní adresní prostor virtuální sítě na server Azure Database for MariaDB. Přehled koncových bodů služby Azure Database for MariaDB VNet, včetně omezení, najdete v článku [Databáze Azure pro koncové body služby Virtuální sítě serveru MariaDB](concepts-data-access-security-vnet.md). Koncové body služby Virtuální sítě jsou dostupné ve všech podporovaných oblastech pro Azure Database for MariaDB.

> [!NOTE]
> Podpora koncových bodů služby virtuální sítě je jenom pro servery optimalizované pro obecné účely a paměť.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Vytvoření pravidla virtuální sítě a povolení koncových bodů služby

1. Na stránce serveru MariaDB klikněte v záhlaví Nastavení na **zabezpečení připojení** a otevřete podokno Zabezpečení připojení pro Azure Database for MariaDB.

2. Ujistěte se, že povolit přístup ke službám Azure ovládací prvek je nastavena na **VYPNUTO**.

> [!Important]
> Pokud ji nastavíte na ZAPNUTO, váš databázový server Azure MariaDB přijímá komunikaci z libovolné podsítě. Ponechání ovládacího prvku nastaveného na zapnuto může být nadměrný přístup z hlediska zabezpečení. Funkce koncového bodu služby Virtuální síť Microsoft Azure v koordinaci s funkcí pravidla virtuální sítě v Azure Database for MariaDB společně můžou snížit plochu zabezpečení.

3. Dále klikněte na **+ Přidání existující virtuální sítě**. Pokud nemáte existující virtuální síť, můžete kliknout na **+ Vytvořit novou virtuální síť** a vytvořit ji. Viz [Úvodní příručka: Vytvoření virtuální sítě pomocí portálu Azure](../virtual-network/quick-create-portal.md)

   ![Portál Azure – klikněte na zabezpečení připojení.](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Zadejte název pravidla virtuální sítě, vyberte předplatné, virtuální síť a název podsítě a klikněte na **Povolit**. To automaticky povolí koncové body služby virtuální sítě v podsíti pomocí značky služby **Microsoft.SQL.**

   ![Portál Azure – konfigurace virtuální sítě](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

   Koncové body služby lze konfigurovat ve virtuálních sítích nezávisle uživatelem s přístupem pro zápis do virtuální sítě.
    
   Chcete-li zabezpečit prostředky služby Azure do virtuální sítě, musí mít uživatel oprávnění k "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" pro přidávané podsítě. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.
    
   Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou prostředky virtuální sítě a služby Azure v různých předplatných, prostředky by měly být pod stejným klientem služby Active Directory (AD). Ujistěte se, že obě předplatná mají registrovaného zprostředkovatele prostředků **Microsoft.Sql.** Další informace naleznete v odkazech [na registraci správce prostředků][resource-manager-portal]

   > [!IMPORTANT]
   > Důrazně doporučujeme přečíst si tento článek o konfiguraci koncového bodu služby a důležité informace před konfigurací koncových bodů služby. **Koncový bod služby Virtuální síť:** [Koncový bod služby Virtuální síť](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služby Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft.Sql**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby se vztahuje také na Azure SQL Database, Azure Database pro MariaDB, PostgreSQL a MySQL služby. Je důležité si uvědomit, že při použití značky služby **Microsoft.Sql** na koncový bod služby virtuální sítě konfiguruje provoz koncových bodů služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB a Azure Database for MySQL servery v podsíti.
   > 

5. Po povolení klikněte na **OK** a uvidíte, že koncové body služby virtuální sítě jsou povolené spolu s pravidlem virtuální sítě.

   ![Koncové body služby Virtuální sítě jsou povoleny a pravidlo virtuální sítě je vytvořeno.](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Další kroky
- Další informace o [konfiguraci ssl v azure databázi pro MariaDB](howto-configure-ssl.md)
- Podobně můžete skriptovat [povolit koncové body služby virtuální sítě a vytvořit pravidlo virtuální sítě pro Azure Database for MariaDB pomocí rozhraní příkazového příkazu k síti Azure](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md