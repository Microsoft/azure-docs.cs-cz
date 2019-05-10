---
title: Konfigurace veřejného koncového bodu – Azure SQL Database managed instance | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat veřejný koncový bod pro spravovanou instanci
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465184"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurace veřejného koncového bodu ve spravované instanci Azure SQL Database

Veřejný koncový bod pro [spravovanou instanci](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) umožňuje přístup k datům pro spravovanou instanci z mimo [virtuální sítě](../virtual-network/virtual-networks-overview.md). Budete moct přistupovat k vaší spravované instance ze služeb Azure s více tenanty jako je Power BI, Azure App Service nebo k místní síti. S využitím veřejného koncového bodu na managed instance, není potřeba používat síť VPN, která se dá vyhnout problémům propustnosti sítě VPN.

V tomto článku se dozvíte jak:

> [!div class="checklist"]
> - Povolení veřejného koncového bodu pro vaši spravovanou instanci na webu Azure Portal
> - Povolení veřejného koncového bodu pro vaše spravované instance pomocí Powershellu
> - Nakonfigurujte spravované instance skupiny zabezpečení sítě umožňující provoz k veřejnému koncovému bodu spravované instance
> - Získání připojovacího řetězce bez veřejného koncového bodu spravované instance

## <a name="permissions"></a>Oprávnění

Z důvodu citlivosti dat, která jsou v managed instance konfigurace povolit spravované instance veřejný koncový bod vyžaduje dvoustupňový proces. Tato bezpečnostní opatření dodržuje oddělení povinností (Zatravnit):

- Povolení veřejného koncového bodu na managed instance je nutné provést správce spravované instance. Správce spravované instance můžete najít na **přehled** stránky SQL managed instance prostředku.
- Povoluje provoz pomocí skupiny zabezpečení sítě, kterou je potřeba provést pomocí správce sítě. Další informace najdete v tématu [oprávnění skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Povolení veřejného koncového bodu pro spravovanou instanci na webu Azure Portal

1. Spusťte na webu Azure portal <https://portal.azure.com/.>
1. Otevřete skupinu prostředků s spravovanou instanci a vyberte **spravovaná instance SQL** chcete nakonfigurovat na veřejný koncový bod.
1. Na **zabezpečení** nastavení, vyberte **virtuální síť** kartu.
1. Na stránce Konfigurace virtuální sítě vyberte **povolit** a pak **Uložit** ikona Aktualizovat konfiguraci.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Povolení veřejného koncového bodu pro managed instance pomocí Powershellu

### <a name="enable-public-endpoint"></a>Povolení veřejného koncového bodu

Spusťte následující příkazy Powershellu. Nahraďte **id předplatného** svým ID předplatného. Také nahraďte **rg-name** s vybranou skupinou prostředků pro spravovanou instanci a nahraďte **mi název** s názvem spravované instance.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Zakázat veřejný koncový bod

Zakázat veřejný koncový bod pomocí Powershellu, by spustíte následující příkaz (a také nezapomeňte zadat skupiny zabezpečení sítě pro příchozí port 3342 zavřete, pokud ho máte nakonfigurované):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Povolte provoz veřejný koncový bod na skupiny zabezpečení sítě

1. Pokud máte na stránce konfigurace spravovanou instanci stále otevřen, přejděte **přehled** kartu. V opačném případě přejděte zpět do vaší **spravovaná instance SQL** prostředků. Vyberte **virtuální síť/podsíť** odkaz, kterým se dostanete na stránku Konfigurace virtuální sítě.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Vyberte **podsítě** kartě v podokně vlevo konfigurace vaší virtuální síti a poznamenejte si **skupiny zabezpečení** pro spravovanou instanci.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Vraťte se do vaší skupiny prostředků, který obsahuje spravované instance. Měli byste vidět **skupinu zabezpečení sítě** název jak je uvedeno nahoře. Vyberte název přejděte na stránku konfigurace skupiny zabezpečení sítě.

1. Vyberte **příchozí pravidla zabezpečení** kartu, a **přidat** pravidlo, které má vyšší prioritu než **deny_all_inbound** pravidlo s následujícím nastavením: </br> </br>

    |Nastavení  |Navrhovaná hodnota  |Popis  |
    |---------|---------|---------|
    |**Zdroj**     |Všechny IP adresy nebo značka služby         |<ul><li>Pro služby Azure, jako je Power BI vyberte značku Azure Cloud Service</li> <li>Pro počítač nebo virtuální počítač Azure použijte adresu IP pro NAT</li></ul> |
    |**Rozsahy zdrojových portů**     |*         |Ponechte tuto hodnotu na * (jakýkoli) jsou obvykle dynamicky přidělené a jako takové, nepředvídatelné zdrojové porty |
    |**cíl**     |Vše         |Opuštění cíl jako některý umožňující provoz v podsíti spravované instance |
    |**Rozsahy cílových portů**     |3342         |Obor cílový port 3342, což je koncový bod veřejné TDS spravované instance |
    |**Protokol**     |TCP         |Spravovaná instance protokolu TCP používá pro TDS |
    |**Akce**     |Povolit         |Povolí příchozí provoz do spravované instance prostřednictvím veřejného koncového bodu |
    |**Priorita**     |1300         |Ujistěte se, že toto pravidlo je vyšší prioritu než **deny_all_inbound** pravidlo |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 se používá pro veřejný koncový bod připojení k managed instance a v tuto chvíli se nedá změnit.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Získání připojovacího řetězce pro veřejný koncový bod spravované instance

1. Přejděte na stránku konfigurace spravovanou instanci SQL, který je povolen pro veřejný koncový bod. Vyberte **připojovací řetězce** kartu **nastavení** konfigurace.
1. Všimněte si, že je název hostitele veřejného koncového bodu k dispozici ve formátu < mi_name >. **veřejné**. < dns_zone >. database.windows.net a že je port používaný pro připojení 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Další postup

- Další informace o [využívající Azure SQL Database managed instance bezpečně s veřejným koncovým bodem](sql-database-managed-instance-public-endpoint-securely.md).