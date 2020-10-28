---
title: Konfigurace skupiny převzetí služeb při selhání
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Naučte se konfigurovat skupinu automatického převzetí služeb při selhání pro Azure SQL Database (jednu i sdruženou) i spravovanou instanci SQL, a to pomocí Azure Portal, rozhraní příkazového řádku Azure a PowerShellu.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 08/14/2019
ms.openlocfilehash: 98f2a8fb0ce48dab0e1e5f7610d73d55526969eb
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675106"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Konfigurace skupiny převzetí služeb při selhání pro Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

V tomto tématu se naučíte, jak nakonfigurovat [skupinu automatického převzetí služeb při selhání](auto-failover-group-overview.md) pro Azure SQL Database a Azure SQL Managed instance.

## <a name="single-database"></a>Izolovaná databáze

Vytvořte skupinu převzetí služeb při selhání a přidejte do ní jednu databázi pomocí Azure Portal nebo PowerShellu.

### <a name="prerequisites"></a>Předpoklady

Vezměte v úvahu následující požadavky:

- Přihlašovací údaje serveru a nastavení brány firewall pro sekundární server se musí shodovat s primárním serverem.

### <a name="create-failover-group"></a>Vytvořit skupinu převzetí služeb při selhání

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte skupinu převzetí služeb při selhání a přidejte do ní jednu databázi pomocí Azure Portal.

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby** a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu.
1. Vyberte databázi, kterou chcete přidat do skupiny převzetí služeb při selhání.
1. Kliknutím na název serveru v části **název serveru** otevřete nastavení serveru.

   ![Otevřít server pro jednu databázi](./media/auto-failover-group-configure/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu** a vytvořte novou skupinu převzetí služeb při selhání.

   ![Přidat novou skupinu převzetí služeb při selhání](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte nebo vyberte požadované hodnoty a pak vyberte **vytvořit** .

   - **Databáze v rámci této skupiny** : Vyberte databázi, kterou chcete přidat do skupiny převzetí služeb při selhání. Když přidáte databázi do skupiny převzetí služeb při selhání, automaticky se spustí proces geografické replikace.

   ![Přidat SQL Database do skupiny převzetí služeb při selhání](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu převzetí služeb při selhání a přidejte do ní svou databázi pomocí PowerShellu.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

---

### <a name="test-failover"></a>Testovací převzetí služeb při selhání

Otestujte převzetí služeb při selhání ve skupině převzetí služeb při selhání pomocí Azure Portal nebo PowerShellu.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Otestujte převzetí služeb při selhání ve skupině převzetí služeb při selhání pomocí Azure Portal.

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby** a do vyhledávacího pole zadejte "Azure SQL". Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu.
1. Vyberte databázi, kterou chcete přidat do skupiny převzetí služeb při selhání.

   ![Otevřít server pro jednu databázi](./media/auto-failover-group-configure/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte skupinu převzetí služeb při selhání, kterou jste právě vytvořili.
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](./media/auto-failover-group-configure/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární.
1. V podokně úloh vyberte **převzetí služeb při** selhání pro skupinu převzetí služeb při selhání obsahující vaši databázi.
1. U upozornění, které vás upozorní na to, že relace TDS budou odpojeny, vyberte **Ano** .

   ![Převzetí služeb při selhání ve skupině, která obsahuje vaši databázi](./media/auto-failover-group-configure/failover-sql-db.png)

1. Zkontrolujte, který server je teď primární a který server je sekundární. Pokud se převzetí služeb při selhání úspěšně převedlo, musí mít dva servery zaměnitelné role.
1. Znovu vyberte **převzetí služeb při selhání** , aby se servery znovu nezměnily na původní role.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testovací převzetí služeb při selhání ve skupině převzetí služeb při selhání pomocí PowerShellu  

Ověřte roli sekundární repliky:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Převzetí služeb při selhání sekundárním serverem:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

Vraťte skupinu převzetí služeb při selhání zpátky na primární server:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Pokud potřebujete sekundární databázi odstranit, odeberte ji ze skupiny převzetí služeb při selhání před jejím odstraněním. Odstranění sekundární databáze před jejím odebráním ze skupiny převzetí služeb při selhání může způsobit nepředvídatelné chování.

## <a name="elastic-pool"></a>Elastický fond

Vytvořte skupinu převzetí služeb při selhání a přidejte do ní elastický fond pomocí Azure Portal nebo PowerShellu.  

### <a name="prerequisites"></a>Předpoklady

Vezměte v úvahu následující požadavky:

- Přihlašovací údaje serveru a nastavení brány firewall pro sekundární server se musí shodovat s primárním serverem.

### <a name="create-the-failover-group"></a>Vytvoření skupiny převzetí služeb při selhání

Vytvořte skupinu převzetí služeb při selhání pro elastický fond pomocí Azure Portal nebo PowerShellu.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte skupinu převzetí služeb při selhání a přidejte do ní elastický fond pomocí Azure Portal.

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby** a do vyhledávacího pole zadejte "Azure SQL". Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu.
1. Vyberte elastický fond, který chcete přidat do skupiny převzetí služeb při selhání.
1. V podokně **Přehled** vyberte název serveru v části **název serveru** a otevřete tak nastavení serveru.
  
   ![Otevřít server pro elastický fond](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu** a vytvořte novou skupinu převzetí služeb při selhání.

   ![Přidat novou skupinu převzetí služeb při selhání](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte nebo vyberte požadované hodnoty a pak vyberte **vytvořit** . Buď vytvořte nový sekundární server, nebo vyberte existující sekundární server.

1. **V rámci skupiny vyberte databáze** a pak zvolte elastický fond, který chcete přidat do skupiny převzetí služeb při selhání. Pokud elastický fond na sekundárním serveru ještě neexistuje, zobrazí se upozornění s výzvou k vytvoření elastického fondu na sekundárním serveru. Vyberte upozornění a pak vyberte **OK** a vytvořte elastický fond na sekundárním serveru.

   ![Přidat elastický fond do skupiny převzetí služeb při selhání](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Vyberte **možnost vyberte** , pokud chcete nastavení elastického fondu použít pro skupinu převzetí služeb při selhání, a pak vyberte **vytvořit** a vytvořte skupinu převzetí služeb při selhání. Přidáním elastického fondu do skupiny převzetí služeb při selhání se automaticky spustí proces geografické replikace.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu převzetí služeb při selhání a přidejte do ní elastický fond pomocí PowerShellu.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>Testovací převzetí služeb při selhání

Testovací převzetí služeb při selhání vašeho elastického fondu pomocí Azure Portal nebo PowerShellu.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Převzetí služeb při selhání pro skupinu převezmete do sekundárního serveru a pak navrácení služeb po obnovení pomocí Azure Portal.

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby** a do vyhledávacího pole zadejte "Azure SQL". Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu.
1. Vyberte elastický fond, který chcete přidat do skupiny převzetí služeb při selhání.
1. V podokně **Přehled** vyberte název serveru v části **název serveru** a otevřete tak nastavení serveru.

   ![Otevřít server pro elastický fond](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte skupinu převzetí služeb při selhání, kterou jste vytvořili v části 2.
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](./media/auto-failover-group-configure/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární.
1. V podokně úloh vyberte **převzetí služeb při** selhání pro skupinu převzetí služeb při selhání, která obsahuje váš elastický fond.
1. U upozornění, které vás upozorní na to, že relace TDS budou odpojeny, vyberte **Ano** .

   ![Převzetí služeb při selhání ve skupině, která obsahuje vaši databázi](./media/auto-failover-group-configure/failover-sql-db.png)

1. Zkontrolujte, který server je primární a který server je sekundární. Pokud se převzetí služeb při selhání úspěšně převedlo, musí mít dva servery zaměnitelné role.
1. Znovu vyberte **převzetí služeb při** selhání, abyste skupinu převzetí služeb při selhání mohli obnovit původní nastavení.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testovací převzetí služeb při selhání ve skupině převzetí služeb při selhání pomocí PowerShellu

Ověřte roli sekundární repliky:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Převzetí služeb při selhání sekundárním serverem:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

---

> [!IMPORTANT]
> Pokud potřebujete sekundární databázi odstranit, odeberte ji ze skupiny převzetí služeb při selhání před jejím odstraněním. Odstranění sekundární databáze před jejím odebráním ze skupiny převzetí služeb při selhání může způsobit nepředvídatelné chování.

## <a name="sql-managed-instance"></a>Spravovaná instance SQL

Vytvořte skupinu převzetí služeb při selhání mezi dvěma spravovanými instancemi ve spravované instanci SQL Azure pomocí Azure Portal nebo PowerShellu.

Budete muset buď nakonfigurovat [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) , nebo vytvořit bránu pro virtuální síť každé spravované instance SQL, připojit tyto dvě brány a pak vytvořit skupinu převzetí služeb při selhání. 

Nasaďte spravované instance do [spárovaných oblastí](../../best-practices-availability-paired-regions.md) z důvodů výkonu. Spravované instance nacházející se v geograficky spárované oblasti mají mnohem lepší výkon v porovnání s nespárovanými oblastmi. 

### <a name="prerequisites"></a>Předpoklady

Vezměte v úvahu následující požadavky:

- Sekundární spravovaná instance musí být prázdná.
- Rozsah podsítě pro sekundární virtuální síť se nesmí překrývat s rozsahem podsítě primární virtuální sítě.
- Kolace a časové pásmo sekundární spravované instance se musí shodovat s primární spravovanou instancí.
- Při připojování dvou bran by měl být **sdílený klíč** stejný pro obě připojení.

### <a name="create-primary-virtual-network-gateway"></a>Vytvořit primární bránu virtuální sítě

Pokud jste nenakonfigurovali [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md), můžete vytvořit primární bránu virtuální sítě pomocí Azure Portal nebo PowerShellu.

> [!NOTE]
> SKU brány má vliv na výkon propustnosti. Tento článek nasadí bránu se základní JEDNOTKou ( `HwGw1` ). Pokud `VpnGw3` chcete dosáhnout vyšší propustnosti, nasaďte vyšší hodnotu SKU (příklad:). Všechny dostupné možnosti najdete v tématu [SKU brány](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) . 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte primární bránu virtuální sítě pomocí Azure Portal.

1. V [Azure Portal](https://portal.azure.com)přejdete do skupiny prostředků a vyberete prostředek **virtuální sítě** pro vaši primární spravovanou instanci.
1. V části **Nastavení** vyberte **podsítě** a pak vyberte, pokud chcete přidat novou **podsíť brány** . Ponechte výchozí hodnoty.

   ![Přidat bránu pro primární spravovanou instanci](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. Po vytvoření brány podsítě v levém navigačním podokně vyberte **vytvořit prostředek** a potom `Virtual network gateway` do vyhledávacího pole zadejte. Vyberte prostředek **brány virtuální sítě** publikovaný **Microsoftem** .

   ![Vytvořit novou bránu virtuální sítě](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Vyplňte požadovaná pole a nakonfigurujte bránu vaší primární spravované instance.

   Následující tabulka uvádí hodnoty nutné pro bránu pro primární spravovanou instanci:

    | **Pole** | Hodnota |
    | --- | --- |
    | **Předplatné** |  Předplatné, ve kterém je vaše primární spravovaná instance. |
    | **Název** | Název brány virtuální sítě. |
    | **Oblast** | Oblast, ve které je vaše primární spravovaná instance. |
    | **Typ brány** | Vyberte **VPN** . |
    | **Typ sítě VPN** | Vybrat **na základě trasy** |
    | **Skladová jednotka (SKU)**| Ponechte výchozí hodnotu `VpnGw1` . |
    | **Umístění**| Umístění, kde je sekundární spravovaná instance a sekundární virtuální síť.   |
    | **Virtuální síť**| Vyberte virtuální síť pro sekundární spravovanou instanci. |
    | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
    | **Název veřejné IP adresy**| Zadejte název vaší IP adresy. |
    | &nbsp; | &nbsp; |

1. Ostatní hodnoty ponechte jako výchozí a potom vyberte **zkontrolovat + vytvořit** a zkontrolujte nastavení brány virtuální sítě.

   ![Nastavení primární brány](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Vyberte **vytvořit** a vytvořte novou bránu virtuální sítě.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte primární bránu virtuální sítě pomocí prostředí PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000

   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location

   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Vytvořit sekundární bránu virtuální sítě

Vytvořte sekundární bránu virtuální sítě pomocí Azure Portal nebo PowerShellu.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Zopakováním kroků v předchozí části vytvořte podsíť virtuální sítě a bránu pro sekundární spravovanou instanci. Vyplňte požadovaná pole a nakonfigurujte bránu pro sekundární spravovanou instanci.

V následující tabulce jsou uvedeny hodnoty nutné pro bránu sekundární spravované instance:

   | **Pole** | Hodnota |
   | --- | --- |
   | **Předplatné** |  Předplatné, ve kterém je vaše sekundární spravovaná instance. |
   | **Název** | Název brány virtuální sítě, například `secondary-mi-gateway` . |
   | **Oblast** | Oblast, ve které je vaše sekundární spravovaná instance. |
   | **Typ brány** | Vyberte **VPN** . |
   | **Typ sítě VPN** | Vybrat **na základě trasy** |
   | **Skladová jednotka (SKU)**| Ponechte výchozí hodnotu `VpnGw1` . |
   | **Umístění**| Umístění, kde je sekundární spravovaná instance a sekundární virtuální síť.   |
   | **Virtuální síť**| Vyberte virtuální síť, která byla vytvořena v části 2, například `vnet-sql-mi-secondary` . |
   | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
   | **Název veřejné IP adresy**| Zadejte název vaší IP adresy, například `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Nastavení sekundární brány](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte sekundární bránu virtuální sítě pomocí prostředí PowerShell.

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000

   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location

   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn

   $gw2
   ```

---

### <a name="connect-the-gateways"></a>Připojení bran

Vytvořte připojení mezi dvěma branami pomocí Azure Portal nebo PowerShellu.

Je potřeba vytvořit dvě připojení – připojení z primární brány k sekundární bráně a pak připojení ze sekundární brány k primární bráně.

Sdílený klíč použitý pro obě připojení by měl být pro každé připojení stejný.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte připojení mezi dvěma branami pomocí Azure Portal.

1. Vyberte **vytvořit prostředek** z [Azure Portal](https://portal.azure.com).
1. Do `connection` vyhledávacího pole zadejte a stiskněte klávesu ENTER pro hledání, které vás přesměruje do prostředku **připojení** publikovaného společností Microsoft.
1. Vyberte **vytvořit** a vytvořte připojení.
1. Na kartě **základy** vyberte následující hodnoty a pak vyberte **OK** .
    1. Jako `VNet-to-VNet` **Typ připojení** vyberte.
    1. Vyberte vaše předplatné z rozevíracího seznamu.
    1. V rozevíracím seznamu vyberte skupinu prostředků pro spravovanou instanci.
    1. Z rozevíracího seznamu vyberte umístění vaší primární spravované instance.
1. Na kartě **Nastavení** vyberte nebo zadejte následující hodnoty a pak vyberte **OK** :
    1. Vyberte bránu primárního síťového rozhraní pro **první bránu virtuální sítě** , například `Primary-Gateway` .  
    1. Vyberte bránu sekundární sítě pro **druhou bránu virtuální sítě** , například `Secondary-Gateway` .
    1. Zaškrtněte políčko vedle možnosti **navázat obousměrné připojení** .
    1. Buď ponechte výchozí název primárního připojení, nebo ho přejmenujte na hodnotu podle vašeho výběru.
    1. Zadejte **sdílený klíč (PSK)** pro připojení, například `mi1m2psk` .

   ![Vytvořit připojení brány](./media/auto-failover-group-configure/create-gateway-connection.png)

1. Na kartě **Souhrn** zkontrolujte nastavení obousměrného připojení a pak vyberte **OK** . tím vytvoříte připojení.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte připojení mezi dvěma branami pomocí PowerShellu.

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection

   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"

   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Vytvoření skupiny převzetí služeb při selhání

Vytvořte skupinu převzetí služeb při selhání pro spravované instance pomocí Azure Portal nebo PowerShellu.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte skupinu převzetí služeb při selhání pro vaše spravované instance SQL pomocí Azure Portal.

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby** a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu.
1. Vyberte primární spravovanou instanci, kterou chcete přidat do skupiny převzetí služeb při selhání.  
1. V části **Nastavení** přejděte na **instance skupiny převzetí služeb při selhání** a pak zvolte **Přidat skupinu** a otevřete stránku **skupiny převzetí služeb při selhání instance** .

   ![Přidat skupinu převzetí služeb při selhání](./media/auto-failover-group-configure/add-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte název skupiny převzetí služeb při selhání a potom z rozevíracího seznamu zvolte sekundární spravovanou instanci. Vyberte **vytvořit** a vytvořte skupinu převzetí služeb při selhání.

   ![Vytvořit skupinu převzetí služeb při selhání](./media/auto-failover-group-configure/create-failover-group.png)

1. Po dokončení nasazení skupiny převzetí služeb při selhání se vrátíte zpátky na stránku **skupiny převzetí služeb při selhání** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu převzetí služeb při selhání pro vaše spravované instance pomocí prostředí PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

---

### <a name="test-failover"></a>Testovací převzetí služeb při selhání

Otestujte převzetí služeb při selhání ve skupině převzetí služeb při selhání pomocí Azure Portal nebo PowerShellu.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Otestujte převzetí služeb při selhání ve skupině převzetí služeb při selhání pomocí Azure Portal.

1. V [Azure Portal](https://portal.azure.com) přejděte do _sekundární_ spravované instance a v části nastavení vyberte **instance skupiny převzetí služeb při selhání** .
1. Zkontrolujte, která spravovaná instance je primární a která spravovaná instance je sekundární.
1. Vyberte **převzetí služeb při selhání** a pak pro upozornění na odpojené relace TDS vyberte **Ano** .

   ![Převzít služby při selhání skupiny převzetí služeb při selhání](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Zkontrolujte, která instance spravovaných je primární a která instance je sekundární. Pokud převzetí služeb při selhání proběhlo úspěšně, měly by tyto dvě instance mít přepnuté role.

   ![Spravované instance mají po převzetí služeb při selhání přepnuté role.](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Vraťte se do nové _sekundární_ spravované instance a znovu vyberte **převzetí služeb při selhání** , aby se primární instance znovu nezdařila do primární role.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testovací převzetí služeb při selhání ve skupině převzetí služeb při selhání pomocí PowerShellu

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="use-private-link"></a>Použití Private Linku

Pomocí privátního odkazu můžete přidružit logický Server ke konkrétní privátní IP adrese v rámci virtuální sítě a podsítě. 

Pokud chcete pro skupinu převzetí služeb při selhání použít privátní odkaz, udělejte toto:

1. Ujistěte se, že primární a sekundární servery jsou v [spárované oblasti](../../best-practices-availability-paired-regions.md). 
1. Vytvořte virtuální síť a podsíť v každé oblasti pro hostování privátních koncových bodů primárních a sekundárních serverů tak, aby nepřesahoval adresní prostory IP adres. Například překrývají se rozsah adres primárního virtuálního síťového rozsahu 10.0.0.0/16 a rozsah adres sekundární virtuální sítě s adresou 10.0.0.1/16. Další informace o rozsahu adres virtuální sítě najdete v blogu [navrhování virtuálních sítí Azure](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/).
1. Vytvoření [privátního koncového bodu a zóny Azure privátní DNS pro primární server](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint). 
1. Vytvořte také privátní koncový bod pro sekundární server, ale tentokrát se rozhodnete znovu použít stejnou zónu Privátní DNS vytvořenou pro primární server. 
1. Po vytvoření privátního propojení můžete skupinu převzetí služeb při selhání vytvořit podle kroků uvedených dříve v tomto článku. 


## <a name="locate-listener-endpoint"></a>Najít koncový bod naslouchacího procesu

Po nakonfigurování skupiny převzetí služeb při selhání aktualizujte připojovací řetězec pro vaši aplikaci na koncový bod naslouchacího procesu. Tím zůstane aplikace připojená k naslouchacího procesu skupiny převzetí služeb při selhání místo primární databáze, elastického fondu nebo databáze instance. Tímto způsobem nemusíte ručně aktualizovat připojovací řetězec pokaždé, když dojde k převzetí služeb databáze při selhání, a provoz se směruje do jakékoli entity, která je aktuálně primární.

Koncový bod naslouchacího procesu je ve formě `fog-name.database.windows.net` a při zobrazení skupiny převzetí služeb při selhání je zobrazený v Azure Portal:

![Připojovací řetězec skupiny převzetí služeb při selhání](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Poznámky

- Odebrání skupiny převzetí služeb při selhání pro jednu nebo sdruženou databázi neukončí replikaci a replikovaná databáze neodstraní. Pokud chcete přidat jednu nebo sdruženou databázi zpátky do skupiny převzetí služeb při selhání po jejím odebrání, budete muset ručně zastavit geografickou replikaci a odstranit databázi ze sekundárního serveru. Při selhání může dojít k chybě, která by mohla být podobná `The operation cannot be performed due to multiple errors` při pokusu o přidání databáze do skupiny převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky

Podrobný postup konfigurace skupiny převzetí služeb při selhání najdete v následujících kurzech:

- [Přidání jedné databáze do skupiny převzetí služeb při selhání](failover-group-add-single-database-tutorial.md)
- [Přidání elastického fondu do skupiny převzetí služeb při selhání](failover-group-add-elastic-pool-tutorial.md)
- [Přidání spravované instance do skupiny převzetí služeb při selhání](../managed-instance/failover-group-add-instance-tutorial.md)

Přehled možností Azure SQL Database vysoké dostupnosti najdete v tématu [geografická replikace](active-geo-replication-overview.md) a [skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md).