---
title: Použít pravidla virtuální sítě-Azure Portal-Azure Database for PostgreSQL-Single server
description: Vytvoření a správa koncových bodů a pravidel služby virtuální sítě Azure Database for PostgreSQL – jeden server pomocí Azure Portal
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 377f8eba179253ca6187b10a22970d0eadcda2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489825"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Vytváření a správa koncových bodů služby virtuální sítě a pravidel virtuální sítě na serveru Azure Database for PostgreSQL – s využitím Azure Portal
Koncovými body a pravidly služby Virtual Network (VNet) přesahují privátní adresní prostor Virtual Network na server Azure Database for PostgreSQL. Přehled koncových bodů služby virtuální sítě Azure Database for PostgreSQL, včetně omezení, najdete v tématu [koncové body služby virtuální sítě v Azure Database for PostgreSQL serveru](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastech pro Azure Database for PostgreSQL.

> [!NOTE]
> Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.
> V případě partnerského vztahu virtuálních sítí platí, že pokud přenos prochází přes společnou bránu virtuální sítě s koncovými body služby a měl by se přesměrovat na partnera, vytvořte prosím pravidlo seznamu ACL/virtuální sítě, které povolí službě Azure Virtual Machines ve virtuální síti brány pro přístup k serveru Azure Database for PostgreSQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Vytvoření pravidla virtuální sítě a povolení koncových bodů služby v Azure Portal

1. Na stránce serveru PostgreSQL v části Nastavení klikněte na **zabezpečení připojení** . tím otevřete podokno zabezpečení připojení pro Azure Database for PostgreSQL. 

2. Zajistěte, aby byl ovládací prvek povolení přístupu ke službám Azure nastavený na **vypnuto**.

> [!Important]
> Pokud ovládací prvek necháte nastavený na ZAPNUTo, Server databáze Azure PostgreSQL přijme komunikaci ze všech podsítí. Ponechání ovládacího prvku na ZAPNUTo může být nadměrný přístup z hlediska zabezpečení. Funkce koncového bodu služby Microsoft Azure Virtual Network, v koordinaci s funkcí pravidla virtuální sítě Azure Database for PostgreSQL, společně může snížit oblast zabezpečení.

3. Potom klikněte na **+ Přidat existující virtuální síť**. Pokud nemáte existující virtuální síť, můžete ji vytvořit kliknutím na **+ vytvořit novou virtuální síť** . Další informace najdete v tématu [rychlý Start: vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md)

   :::image type="content" source="./media/howto-manage-vnet-using-portal/1-connection-security.png" alt-text="Azure Portal – kliknutí na zabezpečení připojení":::

4. Zadejte název pravidla virtuální sítě, vyberte předplatné, virtuální síť a název podsítě a pak klikněte na **Povolit**. Tím se automaticky povolí koncové body služby virtuální sítě v podsíti s použitím značky služby **Microsoft. SQL** .

   :::image type="content" source="./media/howto-manage-vnet-using-portal/2-configure-vnet.png" alt-text="Azure Portal – konfigurace virtuální sítě":::

    Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

    Koncové body služby je možné konfigurovat na virtuálních sítích nezávisle na uživateli s oprávněním k zápisu do virtuální sítě.
    
    Aby bylo možné zabezpečit prostředky služeb Azure pro virtuální síť, musí mít uživatel pro přidávané podsítě oprávnění k Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.
    
    Další informace o [předdefinovaných rolích](../role-based-access-control/built-in-roles.md) a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md).
    
    Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). Zajistěte, aby oba odběry měly zaregistrovaný poskytovatel prostředků **Microsoft. SQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

   > [!IMPORTANT]
   > Před konfigurací koncových bodů služby se důrazně doporučuje přečíst si tento článek o konfiguracích koncových bodů a požadavcích služby. **Koncový bod služby Virtual Network:** [Koncový bod služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft. SQL**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby platí také pro Azure SQL Database služby Azure Database for PostgreSQL a MySQL. Je důležité si uvědomit, že pokud použijete značku služby **Microsoft. SQL** pro koncový bod služby virtuální sítě, nakonfiguruje se provoz koncového bodu služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Azure Database for MySQL serverů v podsíti. 
   > 

5. Po povolení klikněte na **OK** a uvidíte, že koncové body služby virtuální sítě jsou povolené spolu s pravidlem virtuální sítě.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png" alt-text="Povolené koncové body služby virtuální sítě a vytvořené pravidlo virtuální sítě":::

## <a name="next-steps"></a>Další kroky
- Podobně můžete skript pro [Povolení koncových bodů služby virtuální sítě a vytvoření pravidla virtuální sítě pro Azure Database for PostgreSQL pomocí Azure CLI](howto-manage-vnet-using-cli.md).
- Nápovědu k připojení k serveru Azure Database for PostgreSQL najdete v tématu [knihovny připojení pro Azure Database for PostgreSQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md