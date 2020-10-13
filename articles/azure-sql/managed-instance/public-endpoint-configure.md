---
title: Konfigurace veřejného koncového bodu – spravovaná instance Azure SQL
description: Zjistěte, jak nakonfigurovat veřejný koncový bod pro spravovanou instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/07/2019
ms.openlocfilehash: f3708885759a6a353742fe89b4454b39496aeeab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619980"
---
# <a name="configure-public-endpoint-in-azure-sql-managed-instance"></a>Konfigurace veřejného koncového bodu ve spravované instanci Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Veřejný koncový bod pro [spravovanou instanci](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) umožňuje přístup k datům ze spravované instance mimo [virtuální síť](../../virtual-network/virtual-networks-overview.md). K spravované instanci máte přístup z více tenantů služeb Azure, jako je Power BI, Azure App Service nebo místní síť. Pomocí veřejného koncového bodu na spravované instanci nemusíte používat síť VPN, která může pomoci vyhnout se problémům s propustností sítě VPN.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> - Povolit veřejný koncový bod pro spravovanou instanci v Azure Portal
> - Povolení veřejného koncového bodu pro spravovanou instanci pomocí prostředí PowerShell
> - Nakonfigurujte skupinu zabezpečení sítě spravované instance tak, aby povolovala provoz do veřejného koncového bodu spravované instance.
> - Získání připojovacího řetězce veřejného koncového bodu spravované instance

## <a name="permissions"></a>Oprávnění

Vzhledem k citlivosti dat, která jsou ve spravované instanci, je konfigurace pro povolení veřejného koncového bodu spravované instance vyžaduje proces se dvěma kroky. Toto opatření zabezpečení dodržuje oddělení povinností (SoD):

- Povolení veřejného koncového bodu na spravované instanci musí být provedeno správcem spravované instance. Správce spravované instance najdete na stránce s **přehledem** prostředku spravované instance.
- Povolení provozu pomocí skupiny zabezpečení sítě, kterou je nutné provést správcem sítě. Další informace najdete v tématu [oprávnění skupiny zabezpečení sítě](../../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Povolení veřejného koncového bodu pro spravovanou instanci v Azure Portal

1. Spustit Azure Portal v <https://portal.azure.com/.>
1. Otevřete skupinu prostředků se spravovanou instancí a vyberte **spravovanou instanci SQL** , na které chcete nakonfigurovat veřejný koncový bod.
1. V nastavení **zabezpečení** vyberte kartu **virtuální síť** .
1. Na stránce konfigurace virtuální sítě vyberte **Povolit** a potom kliknutím na ikonu **Uložit** aktualizujte konfiguraci.

![Snímek obrazovky ukazuje stránku virtuální sítě spravované instance SQL s povoleným veřejným koncovým bodem.](./media/public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Povolení veřejného koncového bodu pro spravovanou instanci pomocí prostředí PowerShell

### <a name="enable-public-endpoint"></a>Povolit veřejný koncový bod

Spusťte následující příkazy PowerShellu. Nahraďte ID **předplatného** ID vašeho předplatného. Také nahraďte **RG-Name** skupinou prostředků vaší spravované instance a nahraďte **mi** název názvem vaší spravované instance.

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

Pokud chcete veřejný koncový bod zakázat pomocí prostředí PowerShell, spusťte následující příkaz (a také nezapomeňte zavřít NSG pro příchozí port 3342, pokud jste je nakonfigurovali):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Povolení provozu veřejného koncového bodu ve skupině zabezpečení sítě

1. Pokud je stránka konfigurace spravované instance stále otevřená, přejděte na kartu **Přehled** . V opačném případě se vraťte k prostředku **spravované instance SQL** . Vyberte odkaz **virtuální síť/podsíť** , který vás přesměruje na stránku konfigurace virtuální sítě.

    ![Snímek obrazovky se zobrazí stránka konfigurace virtuální sítě, kde můžete najít hodnotu virtuální síť nebo podsíť.](./media/public-endpoint-configure/mi-overview.png)

1. Vyberte kartu **podsítě** v levém podokně Konfigurace vaší virtuální sítě a poznamenejte si **skupinu zabezpečení** pro spravovanou instanci.

    ![Snímek obrazovky zobrazující kartu podsíť, kde můžete získat skupinu zabezpečení pro spravovanou instanci.](./media/public-endpoint-configure/mi-vnet-subnet.png)

1. Vraťte se do skupiny prostředků, která obsahuje vaši spravovanou instanci. Měl by se zobrazit název **skupiny zabezpečení sítě** , který jste si poznamenali výše. Vyberte název, který chcete přejít na stránku konfigurace skupiny zabezpečení sítě.

1. Vyberte kartu **příchozí pravidla zabezpečení** a **přidejte** pravidlo, které má vyšší prioritu než pravidlo **deny_all_inbound** s následujícím nastavením: </br> </br>

    |Nastavení  |Navrhovaná hodnota  |Popis  |
    |---------|---------|---------|
    |**Zdroj**     |Jakákoli IP adresa nebo značka služby         |<ul><li>U služeb Azure, jako je Power BI, vyberte značku cloudová služba Azure.</li> <li>Pro váš počítač nebo virtuální počítač Azure použijte IP adresu překladu adres (NAT).</li></ul> |
    |**Rozsahy zdrojových portů**     |* |Nechte to * (any), protože zdrojové porty se obvykle dynamicky přiřazují a jako nepředvídatelné. |
    |**Cíl**     |Všechny         |Ponechání cíle jako pro povolení provozu do podsítě spravované instance |
    |**Rozsahy cílových portů**     |3342         |Rozsah cílového portu na 3342, který je veřejným koncovým bodem spravované instance TDS |
    |**Protokol**     |TCP         |Spravovaná instance SQL používá protokol TCP pro TDS. |
    |**Akce**     |Povolit         |Povolit příchozí provoz do spravované instance prostřednictvím veřejného koncového bodu |
    |**Priorita**     |1300         |Ujistěte se, že toto pravidlo má vyšší prioritu než pravidlo **deny_all_inbound** . |

    ![Snímek obrazovky zobrazuje pravidla zabezpečení příchozí s novým pravidlem public_endpoint_inbound nad pravidlo deny_all_inbound.](./media/public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 se používá pro připojení veřejného koncového bodu ke spravované instanci a v tomto okamžiku jej nelze změnit.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Získání připojovacího řetězce veřejného koncového bodu spravované instance

1. Přejděte na stránku konfigurace spravované instance, která je povolená pro veřejný koncový bod. V části konfigurace **Nastavení** vyberte kartu **připojovací řetězce** .
1. Všimněte si, že název hostitele veřejného koncového bodu se nachází ve formátu <mi_name>. **Public**. <dns_zone>. Database.Windows.NET a port používaný pro připojení je 3342.

    ![Snímek obrazovky zobrazuje připojovací řetězce pro veřejné a privátní koncové body.](./media/public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [bezpečně používat spravovanou instanci Azure SQL s veřejným koncovým bodem](public-endpoint-overview.md).
