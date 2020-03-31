---
title: Použití pravidel virtuální sítě – portál Azure – Databáze Azure pro PostgreSQL – jeden server
description: Vytvoření a správa koncových bodů a pravidel služby Virtuální sítě Azure Database for PostgreSQL – jeden server pomocí portálu Azure
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 413c3a7b6fdcda996d3db548fb53f358eb8c71e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978280"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Vytváření a správa koncových bodů služby virtuální sítě a pravidel virtuální sítě v databázi Azure pro PostgreSQL – jeden server pomocí portálu Azure
Koncové body a pravidla služeb virtuální sítě (Virtuální síť) rozšiřují privátní adresní prostor virtuální sítě na váš server Azure Database for PostgreSQL. Přehled koncových bodů služby Azure Database for PostgreSQL VNet, včetně omezení, najdete v [tématu Azure Database for PostgreSQL Server Virtuální síť koncových bodů](concepts-data-access-and-security-vnet.md). Koncové body služby Virtuální sítě jsou dostupné ve všech podporovaných oblastech pro Azure Database for PostgreSQL.

> [!NOTE]
> Podpora koncových bodů služby virtuální sítě je jenom pro servery optimalizované pro obecné účely a paměť.
> V případě partnerského vztahu virtuální sítě, pokud provoz protéká společnou bránou virtuální sítě s koncovými body služby a má tok do druhé strany, vytvořte pravidlo ACL/Virtuální sítě, které umožní virtuálním počítačům Azure ve virtuální síti brány přístup k databázi Azure pro postgreSQL server.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Vytvoření pravidla virtuální sítě a povolení koncových bodů služby na webu Azure Portal

1. Na stránce PostgreSQL server v záhlaví Nastavení klikněte na **Zabezpečení připojení** a otevřete podokno Zabezpečení připojení pro Azure Database for PostgreSQL. 

2. Ujistěte se, že povolit přístup ke službám Azure ovládací prvek je nastavena na **VYPNUTO**.

> [!Important]
> Pokud ponecháte ovládací prvek nastavený na ZAPNUTO, váš server Azure PostgreSQL Database server přijímá komunikaci z libovolné podsítě. Ponechání ovládacího prvku nastaveného na zapnuto může být nadměrný přístup z hlediska zabezpečení. Funkce koncového bodu služby Virtuální síť Microsoft Azure v koordinaci s funkcí pravidla virtuální sítě v Azure Database for PostgreSQL může společně snížit plochu zabezpečení.

3. Dále klikněte na **+ Přidání existující virtuální sítě**. Pokud nemáte existující virtuální síť, můžete kliknout na **+ Vytvořit novou virtuální síť** a vytvořit ji. Viz [Úvodní příručka: Vytvoření virtuální sítě pomocí portálu Azure](../virtual-network/quick-create-portal.md)

   ![Portál Azure – klikněte na zabezpečení připojení.](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Zadejte název pravidla virtuální sítě, vyberte předplatné, virtuální síť a název podsítě a klikněte na **Povolit**. To automaticky povolí koncové body služby virtuální sítě v podsíti pomocí značky služby **Microsoft.SQL.**

   ![Portál Azure – konfigurace virtuální sítě](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

    Koncové body služby lze konfigurovat ve virtuálních sítích nezávisle uživatelem s přístupem pro zápis do virtuální sítě.
    
    Chcete-li zabezpečit prostředky služby Azure do virtuální sítě, musí mít uživatel oprávnění k "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" pro přidávané podsítě. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.
    
    Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
    Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou prostředky virtuální sítě a služby Azure v různých předplatných, prostředky by měly být pod stejným klientem služby Active Directory (AD). Ujistěte se, že obě předplatná mají registrovaného zprostředkovatele prostředků **Microsoft.Sql.** Další informace naleznete v odkazech [na registraci správce prostředků][resource-manager-portal]

   > [!IMPORTANT]
   > Důrazně doporučujeme přečíst si tento článek o konfiguraci koncového bodu služby a důležité informace před konfigurací koncových bodů služby. **Koncový bod služby Virtuální síť:** [Koncový bod služby Virtuální síť](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služby Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft.Sql**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby platí také pro Azure SQL Database, Azure Database for PostgreSQL a MySQL služby. Je důležité si uvědomit, že při použití značky služby **Microsoft.Sql** na koncový bod služby virtuální sítě konfiguruje provoz koncových bodů služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Azure Database for MySQL servery v podsíti. 
   > 

5. Po povolení klikněte na **OK** a uvidíte, že koncové body služby virtuální sítě jsou povolené spolu s pravidlem virtuální sítě.

   ![Koncové body služby Virtuální sítě jsou povoleny a pravidlo virtuální sítě je vytvořeno.](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Další kroky
- Podobně můžete skriptovat [povolit koncové body služby virtuální sítě a vytvořit pravidlo virtuální sítě pro Azure Database for PostgreSQL pomocí Azure CLI](howto-manage-vnet-using-cli.md).
- Nápovědu k připojení k azure databázi pro postgreSQL server najdete v [tématu Knihovny připojení pro Azure Database for PostgreSQL.](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md