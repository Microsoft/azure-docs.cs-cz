---
title: Konfigurace veřejného koncového bodu – spravovaná instance
description: Zjistěte, jak nakonfigurovat veřejný koncový bod pro spravovanou instanci.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256156"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurace veřejného koncového bodu ve spravované instanci Azure SQL Database

Veřejný koncový bod pro [spravovanou instanci](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) umožňuje přístup k datům spravované instance mimo [virtuální síť](../virtual-network/virtual-networks-overview.md). Spravovaný instancí máte přístup z víceklientských služeb Azure, jako je Power BI, Azure App Service nebo místní síť. Pomocí veřejného koncového bodu ve spravované instanci není nutné používat síť VPN, což může pomoci vyhnout se problémům s propustností sítě VPN.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> - Povolení veřejného koncového bodu pro spravovanou instanci na webu Azure Portal
> - Povolení veřejného koncového bodu pro spravovanou instanci pomocí PowerShellu
> - Konfigurace skupiny zabezpečení sítě spravované instance tak, aby umožňovala přenosy do veřejného koncového bodu spravované instance
> - Získání připojovacího řetězce veřejného koncového bodu spravované instance

## <a name="permissions"></a>Oprávnění

Vzhledem k citlivosti dat, která je ve spravované instanci, konfigurace povolit veřejný koncový bod spravované instance vyžaduje dvoustupňový proces. Toto bezpečnostní opatření je v souladu s oddělením cel (SoD):

- Povolení veřejného koncového bodu na spravované instanci musí provést správce spravované instance. Správce spravované instance najdete na stránce **Přehled** prostředku spravované instance SQL.
- Povolení provozu pomocí skupiny zabezpečení sítě, kterou musí provést správce sítě. Další informace naleznete v tématu [oprávnění skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Povolení veřejného koncového bodu pro spravovanou instanci na webu Azure Portal

1. Spusťte portál Azure na<https://portal.azure.com/.>
1. Otevřete skupinu prostředků se spravovanou instancí a vyberte **instanci spravovanou SQL,** na které chcete nakonfigurovat veřejný koncový bod.
1. V nastavení **zabezpečení** vyberte kartu **Virtuální síť.**
1. Na stránce Konfigurace virtuální sítě vyberte **Povolit** a pak ikonu **Uložit** aktualizujte konfiguraci.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Povolení veřejného koncového bodu pro spravovanou instanci pomocí PowerShellu

### <a name="enable-public-endpoint"></a>Povolení veřejného koncového bodu

Spusťte následující příkazy prostředí PowerShell. Nahraďte **Id předplatného** id s ID předplatného. Také nahraďte **rg-name** skupinou prostředků pro spravovanou instanci a nahraďte **mi-name** názvem spravované instance.

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

### <a name="disable-public-endpoint"></a>Zakázání veřejného koncového bodu

Chcete-li zakázat veřejný koncový bod pomocí prostředí PowerShell, provedete následující příkaz (a také nezapomeňte zavřít nsg pro příchozí port 3342, pokud jej nakonfigurovali):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Povolit veřejný provoz koncových bodů ve skupině zabezpečení sítě

1. Pokud máte konfigurační stránku spravované instance stále otevřenou, přejděte **SQL managed instance** na kartu **Přehled.** Vyberte odkaz **Virtuální síť nebo podsíť,** který vás přenese na konfigurační stránku Virtuální síť.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. V levém konfiguračním podokně virtuální sítě vyberte kartu **Podsítě** a poznamenejte si **skupinu ZABEZPEČENÍ** pro spravovanou instanci.

    ![mi-vnet-podnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Vraťte se do skupiny prostředků, která obsahuje spravovanou instanci. Měl by se výše uvedený název **skupiny zabezpečení sítě** zobrazit. Vyberte název, který chcete přejít na konfigurační stránku skupiny zabezpečení sítě.

1. Vyberte kartu **Příchozí pravidla zabezpečení** a **Přidejte** pravidlo s vyšší prioritou než pravidlo **deny_all_inbound** s následujícím nastavením: </br> </br>

    |Nastavení  |Navrhovaná hodnota  |Popis  |
    |---------|---------|---------|
    |**Zdroj**     |Libovolná IP adresa nebo výrobní značka         |<ul><li>Pro služby Azure, jako je Power BI, vyberte Azure Cloud Service Tag</li> <li>Pro váš počítač nebo virtuální počítač Azure použijte IP adresu NAT</li></ul> |
    |**Rozsahy zdrojových portů**     |*         |Ponechte to na * (libovolný) jako zdrojové porty jsou obvykle dynamicky přiděleny a jako takové, nepředvídatelné |
    |**Cíl**     |Všechny         |Ponechání cíle jako libovolného, aby byl povolen provoz do podsítě spravované instance |
    |**Rozsahy cílových portů**     |3342         |Cílový port oboru na 3342, což je veřejný koncový bod spravované instance TDS |
    |**Protokol**     |TCP         |Spravovaná instance používá protokol TCP pro TDS. |
    |**Akce**     |Povolit         |Povolit spravovanou instanci příchozího provozu prostřednictvím veřejného koncového bodu |
    |**Priorita**     |1300         |Ujistěte se, že toto pravidlo má vyšší prioritu než pravidlo **deny_all_inbound** |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 se používá pro připojení veřejného koncového bodu ke spravované instanci a v tomto okamžiku jej nelze změnit.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Získání připojovacího řetězce veřejného koncového bodu spravované instance

1. Přejděte na stránku konfigurace spravované instance SQL, která byla povolena pro veřejný koncový bod. V konfiguraci **Nastavení** vyberte kartu **Připojovací řetězce.**
1. Všimněte si, že název hostitele veřejného koncového bodu je dodáván ve formátu <mi_name>. **veřejné**.<dns_zone>.database.windows.net a port používaný pro připojení je 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [bezpečném používání spravované instance Azure SQL Database s veřejným koncovým bodem](sql-database-managed-instance-public-endpoint-securely.md).