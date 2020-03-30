---
title: Konfigurace skupiny převzetí služeb při selhání
description: Zjistěte, jak nakonfigurovat skupinu automatického převzetí služeb při selhání pro jednu databázi Azure SQL Database, elastický fond a spravovanou instanci pomocí portálu Azure, rozhraní příkazového příkazu Az a prostředí PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461798"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Konfigurace skupiny převzetí služeb při selhání pro Azure SQL Database

Toto téma vás naučí, jak nakonfigurovat [skupinu automatického převzetí služeb při selhání](sql-database-auto-failover-group.md) pro jednu databázi Azure SQL Database, elastický fond a spravovanou instanci pomocí portálu Azure nebo PowerShellu. 

## <a name="single-database"></a>Izolovaná databáze
Vytvořte skupinu s podporou převzetí služeb při selhání a přidejte do ní jednu databázi pomocí portálu Azure nebo PowerShellu.

### <a name="prerequisites"></a>Požadavky

Zvažte následující předpoklady:

- Nastavení přihlášení a brány firewall pro sekundární server se musí shodovat s nastavením primárního serveru. 

### <a name="create-failover-group"></a>Vytvořit skupinu převzetí služeb při selhání

# <a name="portal"></a>[Portál](#tab/azure-portal)
Vytvořte skupinu s podporou převzetí služeb při selhání a přidejte do ní jednu databázi pomocí portálu Azure.


1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte jednu databázi, kterou chcete přidat do skupiny převzetí služeb při selhání. 
1. Vyberte název serveru v části **Název serveru** a otevřete nastavení serveru.

   ![Otevřít server pro jeden db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu,** abyste vytvořili novou skupinu převzetí služeb při selhání. 

    ![Přidat novou skupinu převzetí služeb při selhání](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stránce **Skupina s podporou převzetí služeb při selhání** zadejte nebo vyberte požadované hodnoty a pak vyberte **Vytvořit**.

   - **Databáze v rámci skupiny**: Zvolte databázi, kterou chcete přidat do skupiny převzetí služeb při selhání. Přidání databáze do skupiny převzetí služeb při selhání automaticky spustí proces geografické replikace. 
        
    ![Přidání sql db do skupiny převzetí služeb při selhání](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vytvořte skupinu s podporou převzetí služeb při selhání a přidejte do ní jednu databázi pomocí PowerShellu. 

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
   Write-host "Creating a secondary logical server in the failover region..."
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

Otestujte převzetí služeb při selhání skupiny převzetí služeb při selhání pomocí portálu Azure nebo PowerShellu. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Otestujte převzetí služeb při selhání skupiny převzetí služeb při selhání pomocí portálu Azure. 

1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte jednu databázi, kterou chcete přidat do skupiny převzetí služeb při selhání. 

   ![Otevřít server pro jeden db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny s podporou převzetí služeb při selhání** a pak zvolte skupinu s podporou převzetí služeb při selhání, kterou jste právě vytvořili. 
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární. 
1. Vyberte **převzetí služeb při selhání** z podokna úloh, chcete-li přepojit skupinu převzetí služeb při selhání obsahující jednu databázi. 
1. Vyberte **Ano** na upozornění, které vás upozorní, že relace TDS budou odpojeny. 

   ![Převzetí služeb při selhání skupiny převzetí služeb při selhání obsahující databázi SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Zkontrolujte, který server je nyní primární a který server je sekundární. Pokud je převzetí služeb při selhání úspěšné, měly mít dva servery vyměněné role. 
1. Chcete-li servery vrátit zpět do původních rolí, vyberte znovu **možnost Převzetí služeb při selhání.** 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Otestujte převzetí služeb při selhání skupiny s podporou převzetí služeb při selhání pomocí prostředí PowerShell.  

Zkontrolujte roli sekundární repliky: 

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
Převzetí služeb při selhání na sekundární server: 

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

Vrátit skupinu převzetí služeb při selhání zpět na primární server:

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
> Pokud potřebujete sekundární databázi odstranit, odeberte ji ze skupiny převzetí služeb při selhání, než ji odstraníte. Odstranění sekundární databáze před odebráním ze skupiny převzetí služeb při selhání může způsobit nepředvídatelné chování. 

## <a name="elastic-pool"></a>Elastický fond
Vytvořte skupinu s podporou převzetí služeb při selhání a přidejte do ní elastický fond pomocí portálu Azure nebo PowerShellu.  

### <a name="prerequisites"></a>Požadavky

Zvažte následující předpoklady:

- Nastavení přihlášení a brány firewall pro sekundární server se musí shodovat s nastavením primárního serveru. 

### <a name="create-the-failover-group"></a>Vytvoření skupiny převzetí služeb při selhání 

Vytvořte skupinu převzetí služeb při selhání pro elastický fond pomocí portálu Azure nebo PowerShellu. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Vytvořte skupinu s podporou převzetí služeb při selhání a přidejte elastický fond pomocí portálu Azure.

1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte elastický fond, který chcete přidat do skupiny převzetí služeb při selhání. 
1. V podokně **Přehled** vyberte název serveru v části **Název serveru** a otevřete nastavení serveru.
  
    ![Otevřený server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu,** abyste vytvořili novou skupinu převzetí služeb při selhání. 

    ![Přidat novou skupinu převzetí služeb při selhání](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stránce **Skupina s podporou převzetí služeb při selhání** zadejte nebo vyberte požadované hodnoty a pak vyberte **Vytvořit**. Vytvořte nový sekundární server nebo vyberte existující sekundární server. 

1. Vyberte **databáze ve skupině** a pak zvolte elastický fond, který chcete přidat do skupiny převzetí služeb při selhání. Pokud elastický fond ještě neexistuje na sekundárním serveru, zobrazí se upozornění s výzvou k vytvoření elastického fondu na sekundárním serveru. Vyberte upozornění a pak vyberte **OK,** chcete-li vytvořit elastický fond na sekundárním serveru. 
        
    ![Přidání elastického fondu do skupiny převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Vyberte **Vybrat,** chcete-li použít nastavení elastického fondu pro skupinu s podporou převzetí služeb při selhání, a pak vyberte **Vytvořit,** chcete-li vytvořit skupinu převzetí služeb při selhání. Přidáním elastického fondu do skupiny převzetí služeb při selhání se automaticky spustí proces geografické replikace. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu s podporou převzetí služeb při selhání a přidejte do ní elastický fond pomocí PowerShellu. 

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

Otestujte převzetí služeb při selhání vašeho elastického fondu pomocí portálu Azure nebo PowerShellu. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Selhat skupiny převzetí služeb při selhání převést na sekundární server a pak navrácení služeb po selhání pomocí portálu Azure. 

1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte elastický fond, který chcete přidat do skupiny převzetí služeb při selhání. 
1. V podokně **Přehled** vyberte název serveru v části **Název serveru** a otevřete nastavení serveru.
  
    ![Otevřený server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. V podokně **Nastavení** vyberte **skupiny s podporou převzetí služeb při selhání** a pak zvolte skupinu s podporou převzetí služeb při selhání, kterou jste vytvořili v části 2. 
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární. 
1. Vyberte **převzetí služeb při selhání** z podokna úloh, chcete-li přepojit skupinu převzetí služeb při selhání obsahující elastický fond. 
1. Vyberte **Ano** na upozornění, které vás upozorní, že relace TDS budou odpojeny. 

   ![Převzetí služeb při selhání skupiny převzetí služeb při selhání obsahující databázi SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Zkontrolujte, který server je primární, který server je sekundární. Pokud je převzetí služeb při selhání úspěšné, měly mít dva servery vyměněné role. 
1. Vyberte **převzetí služeb při selhání** znovu, chcete-li skupinu převzetí služeb při selhání vrátit zpět do původního nastavení. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Otestujte převzetí služeb při selhání skupiny s podporou převzetí služeb při selhání pomocí prostředí PowerShell.

Zkontrolujte roli sekundární repliky: 

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

Převzetí služeb při selhání na sekundární server: 

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
> Pokud potřebujete sekundární databázi odstranit, odeberte ji ze skupiny převzetí služeb při selhání, než ji odstraníte. Odstranění sekundární databáze před odebráním ze skupiny převzetí služeb při selhání může způsobit nepředvídatelné chování. 

## <a name="managed-instance"></a>Spravovaná instance

Vytvořte skupinu převzetí služeb při selhání mezi dvěma spravovanými instancemi pomocí portálu Azure nebo PowerShellu. 

Budete muset buď nakonfigurovat [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) nebo vytvořit bránu pro virtuální síť každé spravované instance, připojit dvě brány a pak vytvořit skupinu převzetí služeb při selhání. 

### <a name="prerequisites"></a>Požadavky
Zvažte následující předpoklady:

- Sekundární spravovaná instance musí být prázdná.
- Rozsah podsítí pro sekundární virtuální síť nesmí překrývat rozsah podsítí primární virtuální sítě. 
- Řazení a časové pásmo sekundární instance se musí shodovat s primární instancí. 
- Při připojování dvou bran by měl být **sdílený klíč** stejný pro obě připojení. 

### <a name="create-primary-virtual-network-gateway"></a>Vytvoření primární brány virtuální sítě 

Pokud jste [expressroute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)nenakonfigurovali , můžete vytvořit primární bránu virtuální sítě pomocí portálu Azure nebo PowerShellu. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Vytvořte primární bránu virtuální sítě pomocí portálu Azure. 

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do skupiny prostředků a vyberte prostředek **virtuální sítě** pro primární spravovanou instanci. 
1. V části **Nastavení** vyberte **Podsítě** a pak vyberte přidání nové **podsítě Brány**. Ponechte výchozí hodnoty. 

   ![Přidání brány pro primární spravovanou instanci](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Po vytvoření brány podsítě vyberte **Vytvořit prostředek** z levého `Virtual network gateway` navigačního podokna a zadejte do vyhledávacího pole. Vyberte prostředek **brány virtuální sítě** publikovaný společností **Microsoft**. 

   ![Vytvoření nové brány virtuální sítě](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Vyplňte požadovaná pole pro konfiguraci brány primární spravované instance. 

   V následující tabulce jsou uvedeny hodnoty nezbytné pro bránu pro primární spravovanou instanci:
 
    | **Pole** | Hodnota |
    | --- | --- |
    | **Předplatné** |  Předplatné, kde je vaše primární spravovaná instance. |
    | **Název** | Název brány virtuální sítě. | 
    | **Oblasti** | Oblast, kde je sekundární spravovaná instance. |
    | **Typ brány** | Vyberte **možnost VPN**. |
    | **Typ vpn** | Vybrat **na základě trasy** |
    | **Sku**| Ponechat `VpnGw1`výchozí hodnotu . |
    | **Umístění**| Umístění, kde je sekundární spravovaná instance a sekundární virtuální síť.   |
    | **Virtuální síť**| Vyberte virtuální síť pro sekundární spravovanou instanci. |
    | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
    | **Název veřejné IP adresy**| Zadejte název své IP adresy. |
    | &nbsp; | &nbsp; |

1. Ostatní hodnoty ponechejte jako výchozí a pak vyberte **Revize + vytvořit a** zkontrolujte nastavení brány virtuální sítě.

   ![Nastavení primární brány](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Vyberte **Vytvořit,** chcete-li vytvořit novou bránu virtuální sítě. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte primární bránu virtuální sítě pomocí PowerShellu. 

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

### <a name="create-secondary-virtual-network-gateway"></a>Vytvoření sekundární brány virtuální sítě

Vytvořte sekundární bránu virtuální sítě pomocí portálu Azure nebo PowerShellu. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Opakováním kroků v předchozí části vytvořte podsíť virtuální sítě a bránu pro sekundární spravovanou instanci. Vyplňte požadovaná pole pro konfiguraci brány pro sekundární spravovanou instanci. 

   V následující tabulce jsou uvedeny hodnoty nezbytné pro bránu pro sekundární spravovanou instanci:

   | **Pole** | Hodnota |
   | --- | --- |
   | **Předplatné** |  Předplatné, kde je vaše sekundární spravovaná instance. |
   | **Název** | Název brány virtuální sítě, například `secondary-mi-gateway`. | 
   | **Oblasti** | Oblast, kde je sekundární spravovaná instance. |
   | **Typ brány** | Vyberte **možnost VPN**. |
   | **Typ vpn** | Vybrat **na základě trasy** |
   | **Sku**| Ponechat `VpnGw1`výchozí hodnotu . |
   | **Umístění**| Umístění, kde je sekundární spravovaná instance a sekundární virtuální síť.   |
   | **Virtuální síť**| Vyberte virtuální síť, která byla vytvořena `vnet-sql-mi-secondary`v části 2, například . |
   | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
   | **Název veřejné IP adresy**| Zadejte název adresy IP, `secondary-gateway-IP`například . |
   | &nbsp; | &nbsp; |

   ![Nastavení sekundární brány](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte sekundární bránu virtuální sítě pomocí PowerShellu. 

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
Vytvořte připojení mezi dvěma branami pomocí portálu Azure nebo PowerShellu. 

Je třeba vytvořit dvě připojení – připojení z primární brány k sekundární bráně a potom připojení ze sekundární brány k primární bráně. 

Sdílený klíč používaný pro obě připojení by měl být pro každé připojení stejný. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Vytvořte připojení mezi dvěma branami pomocí portálu Azure. 

1. Vyberte **Vytvořit prostředek** z [webu Azure Portal](https://portal.azure.com).
1. Zadejte `connection` do vyhledávacího pole a stisknutím klávesy enter vyhledejte, které vás přenese k prostředku **připojení** vydanému společností Microsoft.
1. Chcete-li vytvořit připojení, vyberte **Vytvořit.** 
1. Na kartě **Základy** vyberte následující hodnoty a pak vyberte **OK**. 
    1. Tuto `VNet-to-VNet` možnost vyberte pro **typ připojení**. 
    1. Vyberte vaše předplatné z rozevíracího seznamu. 
    1. V rozevíracím seznamku vyberte skupinu prostředků pro spravovanou instanci. 
    1. V rozevíracím centru vyberte umístění primární spravované instance. 
1. Na kartě **Nastavení** vyberte nebo zadejte následující hodnoty a pak vyberte **OK**:
    1. Zvolte primární síťovou bránu pro **první bránu virtuální sítě**, například `Primary-Gateway`.  
    1. Zvolte bránu sekundární sítě pro **druhou bránu virtuální sítě**, například `Secondary-Gateway`. 
    1. Zaškrtněte políčko vedle **položky Nařídit obousměrné připojení**. 
    1. Ponechte buď výchozí název primárního připojení, nebo jej přejmenujte na hodnotu podle vašeho výběru. 
    1. Zadejte **sdílený klíč (PSK)** pro `mi1m2psk`připojení, například . 

   ![Vytvořit připojení brány](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Na kartě **Souhrn** zkontrolujte nastavení obousměrné připojení a pak vyberte **OK,** chcete-li vytvořit připojení. 

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
Vytvořte skupinu převzetí služeb při selhání pro spravované instance pomocí portálu Azure nebo PowerShellu. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Vytvořte skupinu převzetí služeb při selhání pro spravované instance pomocí portálu Azure Portal. 

1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte primární spravovanou instanci, kterou chcete přidat do skupiny převzetí služeb při selhání.  
1. V části **Nastavení**přejděte do **skupin y převzetí služeb při selhání instance** a pak zvolte Přidat **skupinu** a otevřete stránku **Skupina převzetí služeb při selhání instance.** 

   ![Přidání skupiny převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání instance** zadejte název skupiny s podporou převzetí služeb při selhání a z rozevíracího seznamu zvolte sekundární spravovanou instanci. Chcete-li vytvořit skupinu s podporou převzetí služeb při selhání, vyberte **možnost Vytvořit.** 

   ![Vytvořit skupinu převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Po dokončení nasazení skupiny s podporou převzetí služeb při selhání budete převedena zpět na stránku **skupiny převzetí služeb při selhání.** 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu převzetí služeb při selhání pro spravované instance pomocí PowerShellu. 

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

Otestujte převzetí služeb při selhání skupiny převzetí služeb při selhání pomocí portálu Azure nebo PowerShellu. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Otestujte převzetí služeb při selhání skupiny převzetí služeb při selhání pomocí portálu Azure. 

1. Přejděte na _sekundární_ spravovanou instanci na [webu Azure Portal](https://portal.azure.com) a v nastavení vyberte **skupiny převzetí služeb při selhání instance.** 
1. Zkontrolujte, která spravovaná instance je primární a která spravovaná instance je sekundární. 
1. Vyberte **převzetí služeb při selhání** a pak vyberte **Ano** na upozornění na odpojené relace TDS. 

   ![Převzetí služeb při selhání skupiny převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Zkontrolujte, která instance manged je primární a která instance je sekundární. Pokud je převzetí služeb při selhání úspěšné, měly dvě instance přepínat role. 

   ![Spravované instance přepnuly role po převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Přejděte na novou _sekundární_ spravovanou instanci a znovu vyberte **převzetí služeb při selhání,** aby primární instance selhala zpět do primární role. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Otestujte převzetí služeb při selhání skupiny s podporou převzetí služeb při selhání pomocí prostředí PowerShell. 

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

## <a name="locate-listener-endpoint"></a>Vyhledání koncového bodu posluchače

Po konfiguraci skupiny převzetí služeb při selhání aktualizujte připojovací řetězec pro vaši aplikaci na koncový bod posluchače. Tím zachováte aplikaci připojenou k naslouchací proces skupiny převzetí služeb při selhání, nikoli k primární databázi, elastickém fondu nebo spravované instanci. Tímto způsobem není nutné ručně aktualizovat připojovací řetězec pokaždé, když vaše entita databáze Azure SQL databáze převzetí služby při selhání a provoz je směrován do kterékoli entity je aktuálně primární. 

Koncový bod naslouchací `fog-name.database.windows.net`proces je ve formě aplikace a je viditelný na webu Azure Portal při zobrazení skupiny převzetí služeb při selhání:

![Připojovací řetězec skupiny převzetí služeb při selhání](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Poznámky

- Odebráním skupiny převzetí služeb při selhání pro jednu nebo sdruženou databázi replikaci nezastavíte a replikovanou databázi neodstraníte. Pokud chcete přidat jednu nebo sdruženou databázi zpět do skupiny převzetí služeb při selhání po odebrání, budete muset ručně zastavit geografickou replikaci a odstranit databázi ze sekundárního serveru. Pokud to neuděláte, může dojít `The operation cannot be performed due to multiple errors` k chybě podobné při pokusu o přidání databáze do skupiny převzetí služeb při selhání. 


## <a name="next-steps"></a>Další kroky

Podrobné kroky konfigurace skupiny s podporou převzetí služeb při selhání naleznete v následujících kurzech:
- [Přidání jedné databáze do skupiny převzetí služeb při selhání](sql-database-single-database-failover-group-tutorial.md)
- [Přidání elastického fondu do skupiny převzetí služeb při selhání](sql-database-elastic-pool-failover-group-tutorial.md)
- [Přidání spravovaných instancí do skupiny převzetí služeb při selhání](sql-database-managed-instance-failover-group-tutorial.md)
 
Přehled možností vysoké dostupnosti Azure SQL Database najdete v [tématu geografická replikace](sql-database-active-geo-replication.md) a [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md). 
