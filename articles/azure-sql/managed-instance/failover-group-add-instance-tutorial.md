---
title: 'Kurz: Přidání spravované instance SQL do skupiny převzetí služeb při selhání'
titleSuffix: Azure SQL Managed Instance
description: V tomto kurzu se naučíte vytvořit skupinu převzetí služeb při selhání mezi primární a sekundární spravovanou instancí SQL Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, sstein
ms.date: 08/27/2019
ms.openlocfilehash: 034940a0990fc97118e62caab051a5a9e2ffd3e7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578559"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Kurz: Přidání spravované instance SQL do skupiny převzetí služeb při selhání
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Přidejte spravované instance spravované instance Azure SQL do skupiny převzetí služeb při selhání. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> - Vytvoří primární spravovanou instanci.
> - Vytvořte sekundární spravovanou instanci jako součást [skupiny převzetí služeb při selhání](../database/auto-failover-group-overview.md). 
> - Testovací převzetí služeb při selhání.

  > [!NOTE]
  > - Při procházení tohoto kurzu se ujistěte, že konfigurujete prostředky s [požadavky pro nastavení skupin převzetí služeb při selhání pro spravovanou instanci SQL](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Vytvoření spravované instance může trvat poměrně dlouhou dobu. V důsledku toho může dokončení tohoto kurzu trvat několik hodin. Další informace o časech zřizování najdete v tématu [operace správy spravované instance SQL](sql-managed-instance-paas-overview.md#management-operations). 
  > - Spravované instance účastnící se skupiny převzetí služeb při selhání vyžadují buď [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) , nebo dvě připojené brány VPN Gateway. Globální VNET Peering se nepodporuje. Tento kurz popisuje kroky pro vytvoření a připojení bran VPN. Tento postup přeskočte, pokud již máte nakonfigurovanou ExpressRoute. 


## <a name="prerequisites"></a>Požadavky

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující: 

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
K dokončení tohoto kurzu se ujistěte, že máte následující položky:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>Vytvoření skupiny prostředků a primární spravované instance

V tomto kroku vytvoříte skupinu prostředků a primární spravovanou instanci pro skupinu převzetí služeb při selhání pomocí Azure Portal nebo PowerShellu. 

Nasaďte spravované instance do [spárovaných oblastí](../../best-practices-availability-paired-regions.md) z důvodů výkonu. Spravované instance nacházející se v geograficky spárované oblasti mají mnohem lepší výkon v porovnání s nespárovanými oblastmi. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal) 

Vytvořte skupinu prostředků a svoji primární spravovanou instanci pomocí Azure Portal. 

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a potom `Azure SQL` do vyhledávacího pole zadejte. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o různých databázích můžete zobrazit tak, že na dlaždici **databáze** vyberete **Zobrazit podrobnosti** .
1. Na dlaždici **spravované instance SQL** vyberte **vytvořit** . 

    ![Vybrat spravovanou instanci SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Na stránce **Vytvoření spravované instance SQL Azure** na kartě **základy** :
    1. V části **Podrobnosti o projektu**vyberte v rozevíracím seznamu své **předplatné** a pak zvolte **vytvořit novou** skupinu prostředků. Zadejte název vaší skupiny prostředků, jako je například `myResourceGroup` . 
    1. V části **Podrobnosti spravované instance SQL**zadejte název vaší spravované instance a oblast, do které chcete nasadit spravovanou instanci. Ve výchozích hodnotách ponechte **výpočetní prostředky + úložiště** . 
    1. V části **účet správce**zadejte přihlašovací jméno správce, například `azureuser` a složitá hesla správce. 

    ![Vytvořit primární spravovanou instanci](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Ponechte zbývající nastavení na výchozí hodnoty a vyberte **zkontrolovat + vytvořit** a zkontrolujte nastavení spravované instance SQL. 
1. Vyberte **vytvořit** k vytvoření vaší primární spravované instance. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu prostředků a primární spravovanou instanci pomocí prostředí PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podsíť ve virtuální síti. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Vytvoří směrovací tabulku. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfiguraci podsítě pro virtuální síť.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Načte skupinu zabezpečení sítě. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje skupinu zabezpečení sítě.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Přidá trasu do směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje směrovací tabulku.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci.  |

---

## <a name="create-secondary-virtual-network"></a>Vytvořit sekundární virtuální síť

Pokud používáte Azure Portal k vytvoření spravované instance, bude nutné vytvořit virtuální síť samostatně, protože existuje požadavek, aby podsíť primární a sekundární spravované instance nepřesahoval rozsahy. Pokud ke konfiguraci spravované instance používáte PowerShell, přeskočte dopředu ke kroku 3. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal) 

Pokud chcete ověřit rozsah podsítě vaší primární virtuální sítě, postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)přejděte do skupiny prostředků a vyberte virtuální síť pro vaši primární instanci.  
2. V části **Nastavení** vyberte **podsítě** a poznamenejte si **Rozsah adres**. Rozsah adres podsítě virtuální sítě pro sekundární spravovanou instanci se nemůže překrývat. 


   ![Primární podsíť](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Pokud chcete vytvořit virtuální síť, postupujte takto:

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek** a vyhledejte *virtuální síť*. 
1. Vyberte možnost **Virtual Network** publikovanou Microsoftem a pak na další stránce vyberte **vytvořit** . 
1. Vyplňte požadovaná pole pro konfiguraci virtuální sítě pro sekundární spravovanou instanci a pak vyberte **vytvořit**. 

   Následující tabulka uvádí hodnoty nutné pro sekundární virtuální síť:

    | **Pole** | Hodnota |
    | --- | --- |
    | **Název** |  Název virtuální sítě, která se má použít u sekundární spravované instance, například `vnet-sql-mi-secondary` . |
    | **Adresní prostor** | Adresní prostor pro virtuální síť, například `10.128.0.0/16` . | 
    | **Předplatné** | Předplatné, ve kterém se nachází vaše primární spravovaná instance a skupina prostředků |
    | **Oblast** | Umístění, do kterého budete nasazovat sekundární spravovanou instanci. |
    | **Podsíť** | Název vaší podsítě. `default` je ve výchozím nastavení k dispozici pro vás. |
    | **Rozsah adres**| Rozsah adres pro vaši podsíť. Musí se lišit od rozsahu adres podsítě používaného virtuální sítí vaší primární spravované instance, například `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Hodnoty sekundární virtuální sítě](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento krok je nezbytný pouze v případě, že používáte Azure Portal k nasazení spravované instance SQL. Pokud používáte PowerShell, přejděte dopředu ke kroku 3. 

---

## <a name="create-a-secondary-managed-instance"></a>Vytvoření sekundární spravované instance
V tomto kroku vytvoříte sekundární spravovanou instanci v Azure Portal, která bude také konfigurovat síť mezi dvěma spravovanými instancemi. 

Vaše druhá spravovaná instance musí:
- Být prázdné. 
- Mít jinou podsíť a rozsah IP adres než primární spravovaná instance. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal) 

Pomocí Azure Portal Vytvořte sekundární spravovanou instanci. 

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a potom `Azure SQL` do vyhledávacího pole zadejte. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o různých databázích můžete zobrazit tak, že na dlaždici **databáze** vyberete **Zobrazit podrobnosti** .
1. Na dlaždici **spravované instance SQL** vyberte **vytvořit** . 

    ![Vybrat spravovanou instanci SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Na kartě **základy** na stránce **Vytvoření spravované instance Azure SQL** vyplňte požadovaná pole pro konfiguraci sekundární spravované instance. 

   Následující tabulka uvádí hodnoty nutné pro sekundární spravovanou instanci:
 
    | **Pole** | Hodnota |
    | --- | --- |
    | **Předplatné** |  Předplatné, ve kterém je vaše primární spravovaná instance. |
    | **Skupina prostředků**| Skupina prostředků, ve které je vaše primární spravovaná instance. |
    | **Název spravované instance SQL** | Název vaší nové sekundární spravované instance, například `sql-mi-secondary` .  | 
    | **Oblast**| Umístění sekundární spravované instance.  |
    | **Přihlašovací jméno správce spravované instance SQL** | Přihlašovací jméno, které chcete použít pro novou sekundární spravovanou instanci, například `azureuser` . |
    | **Heslo** | Složitá hesla, která budou používána přihlášením správce pro novou sekundární spravovanou instanci.  |
    | &nbsp; | &nbsp; |

1. Na kartě **sítě** pro **Virtual Network**v rozevíracím seznamu vyberte virtuální síť, kterou jste vytvořili pro sekundární spravovanou instanci.

   ![Sekundární síť MI](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Na kartě **Další nastavení** pro **geografickou replikaci**vyberte **Ano** , aby se _použil jako sekundární převzetí služeb při selhání_. Z rozevíracího seznamu vyberte primární spravovanou instanci. 
    
   Ujistěte se, že se kolace a časové pásmo shodují s primární spravovanou instancí. Primární spravovaná instance vytvořená v tomto kurzu použila výchozí `SQL_Latin1_General_CP1_CI_AS` řazení a `(UTC) Coordinated Universal Time` časové pásmo. 

   ![Síť sekundárních spravovaných instancí](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte nastavení sekundární spravované instance. 
1. Vyberte **vytvořit** a vytvořte svou sekundární spravovanou instanci. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte sekundární spravovanou instanci pomocí prostředí PowerShell. 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podsíť ve virtuální síti. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Vytvoří směrovací tabulku. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfiguraci podsítě pro virtuální síť.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Načte skupinu zabezpečení sítě. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje skupinu zabezpečení sítě.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Přidá trasu do směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje směrovací tabulku.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci.  |

---

## <a name="create-a-primary-gateway"></a>Vytvoření primární brány 

Aby se dvě spravované instance účastnily skupiny převzetí služeb při selhání, musí existovat buď ExpressRoute, nebo brána nakonfigurovaná mezi virtuálními sítěmi dvou spravovaných instancí, aby bylo možné síťovou komunikaci. Pokud se rozhodnete nakonfigurovat [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) místo připojení dvou bran VPN Gateway, přejděte ke [kroku 7](#create-a-failover-group).  

Tento článek popisuje kroky pro vytvoření dvou bran sítě VPN a jejich připojení, ale můžete přeskočit k vytvoření skupiny převzetí služeb při selhání, pokud jste místo toho nakonfigurovali ExpressRoute. 

> [!NOTE]
> SKU brány má vliv na výkon propustnosti. Tento kurz nasadí bránu se základní JEDNOTKou ( `HwGw1` ). Pokud `VpnGw3` chcete dosáhnout vyšší propustnosti, nasaďte vyšší hodnotu SKU (příklad:). Všechny dostupné možnosti najdete v tématu [SKU brány](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) .

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte bránu pro virtuální síť vaší primární spravované instance pomocí Azure Portal. 


1. V [Azure Portal](https://portal.azure.com)přejdete do skupiny prostředků a vyberete prostředek **virtuální sítě** pro vaši primární spravovanou instanci. 
1. V části **Nastavení** vyberte **podsítě** a pak vyberte, pokud chcete přidat novou **podsíť brány**. Ponechte výchozí hodnoty. 

   ![Přidat bránu pro primární spravovanou instanci](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Po vytvoření brány podsítě v levém navigačním podokně vyberte **vytvořit prostředek** a potom `Virtual network gateway` do vyhledávacího pole zadejte. Vyberte prostředek **brány virtuální sítě** publikovaný **Microsoftem**. 

   ![Vytvořit novou bránu virtuální sítě](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Vyplňte požadovaná pole a nakonfigurujte bránu pro vaši primární spravovanou instanci. 

   Následující tabulka uvádí hodnoty nutné pro bránu pro primární spravovanou instanci:
 
    | **Pole** | Hodnota |
    | --- | --- |
    | **Předplatné** |  Předplatné, ve kterém je vaše primární spravovaná instance. |
    | **Název** | Název brány virtuální sítě, například `primary-mi-gateway` . | 
    | **Oblast** | Oblast, ve které je vaše primární spravovaná instance. |
    | **Typ brány** | Vyberte **VPN**. |
    | **Typ sítě VPN** | Vyberte **směrování založené na trasách**. |
    | **Skladová jednotka (SKU)**| Ponechte výchozí hodnotu `VpnGw1` . |
    | **Virtuální síť**| Vyberte virtuální síť, která byla vytvořena v části 2, například `vnet-sql-mi-primary` . |
    | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
    | **Název veřejné IP adresy**| Zadejte název vaší IP adresy, například `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Ostatní hodnoty ponechte jako výchozí a potom vyberte **zkontrolovat + vytvořit** a zkontrolujte nastavení brány virtuální sítě.

   ![Nastavení primární brány](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Vyberte **vytvořit** a vytvořte novou bránu virtuální sítě. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte bránu pro virtuální síť vaší primární spravované instance pomocí PowerShellu. 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podsíť ve virtuální síti. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci protokolu IP pro bránu virtuální sítě. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu virtuální sítě. |


---


## <a name="create-secondary-gateway"></a>Vytvořit sekundární bránu 
V tomto kroku vytvoříte bránu pro virtuální síť sekundární spravované instance pomocí Azure Portal. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí Azure Portal vytvořte podsíť virtuální sítě a bránu pro sekundární spravovanou instanci opakováním kroků v předchozí části. Vyplňte požadovaná pole a nakonfigurujte bránu pro sekundární spravovanou instanci. 

   V následující tabulce jsou uvedeny hodnoty nutné pro bránu sekundární spravované instance:

   | **Pole** | Hodnota |
   | --- | --- |
   | **Předplatné** |  Předplatné, ve kterém je vaše sekundární spravovaná instance. |
   | **Název** | Název brány virtuální sítě, například `secondary-mi-gateway` . | 
   | **Oblast** | Oblast, ve které je vaše sekundární spravovaná instance. |
   | **Typ brány** | Vyberte **VPN**. |
   | **Typ sítě VPN** | Vyberte **směrování založené na trasách**. |
   | **Skladová jednotka (SKU)**| Ponechte výchozí hodnotu `VpnGw1` . |
   | **Virtuální síť**| Vyberte virtuální síť pro sekundární spravovanou instanci, například `vnet-sql-mi-secondary` . |
   | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
   | **Název veřejné IP adresy**| Zadejte název vaší IP adresy, například `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Nastavení sekundární brány](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte bránu pro virtuální síť sekundární spravované instance pomocí PowerShellu. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating secondary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podsíť ve virtuální síti. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci protokolu IP pro bránu virtuální sítě. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu virtuální sítě. |

---


## <a name="connect-the-gateways"></a>Připojení bran
V tomto kroku vytvořte obousměrné připojení mezi dvěma branami obou virtuálních sítí. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Připojte dvě brány pomocí Azure Portal. 


1. Vyberte **vytvořit prostředek** z [Azure Portal](https://portal.azure.com).
1. Do `connection` vyhledávacího pole zadejte a stiskněte klávesu ENTER pro hledání, které vás přesměruje do prostředku **připojení** publikovaného společností Microsoft.
1. Vyberte **vytvořit** a vytvořte připojení. 
1. Na stránce **základy** vyberte následující hodnoty a pak vyberte **OK**. 
    1. Jako `VNet-to-VNet` **Typ připojení**vyberte. 
    1. Vyberte vaše předplatné z rozevíracího seznamu. 
    1. V rozevíracím seznamu vyberte skupinu prostředků pro spravovanou instanci SQL. 
    1. Z rozevíracího seznamu vyberte umístění vaší primární spravované instance. 
1. Na stránce **Nastavení** vyberte nebo zadejte následující hodnoty a pak vyberte **OK**:
    1. Vyberte bránu primárního síťového rozhraní pro **první bránu virtuální sítě**, například `primaryGateway` .  
    1. Vyberte bránu sekundární sítě pro **druhou bránu virtuální sítě**, například `secondaryGateway` . 
    1. Zaškrtněte políčko vedle možnosti **navázat obousměrné připojení**. 
    1. Buď ponechte výchozí název primárního připojení, nebo ho přejmenujte na hodnotu podle vašeho výběru. 
    1. Zadejte **sdílený klíč (PSK)** pro připojení, například `mi1m2psk` . 
    1. Kliknutím na **OK** uložte nastavení. 

    ![Vytvořit připojení brány](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. Na stránce **Revize + vytvořit** zkontrolujte nastavení obousměrného připojení a pak vyberte **OK** . tím vytvoříte připojení. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Připojte dvě brány pomocí PowerShellu. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Tato část kurzu používá následující rutinu PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Vytvoří připojení mezi dvěma branami virtuální sítě.   |

---


## <a name="create-a-failover-group"></a>Vytvoření skupiny převzetí služeb při selhání
V tomto kroku vytvoříte skupinu převzetí služeb při selhání a přidáte do ní obě spravované instance. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Vytvořte skupinu převzetí služeb při selhání pomocí Azure Portal. 


1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a potom `Azure SQL` do vyhledávacího pole zadejte. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Vyberte primární spravovanou instanci, kterou jste vytvořili v první části, například `sql-mi-primary` . 
1. V části **Nastavení**přejděte na **instance skupiny převzetí služeb při selhání** a pak zvolte **Přidat skupinu** . otevře se stránka **Skupina převzetí služeb při selhání instance** . 

   ![Přidat skupinu převzetí služeb při selhání](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Na stránce **skupiny převzetí služeb při selhání** zadejte název skupiny převzetí služeb při selhání, například `failovergrouptutorial` . Pak v rozevíracím seznamu zvolte sekundární spravovanou instanci, například `sql-mi-secondary` . Vyberte **vytvořit** a vytvořte skupinu převzetí služeb při selhání. 

   ![Vytvořit skupinu převzetí služeb při selhání](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. Po dokončení nasazení skupiny převzetí služeb při selhání se vrátíte zpátky na stránku **skupiny převzetí služeb při selhání** . 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vytvořte skupinu převzetí služeb při selhání pomocí PowerShellu. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Tato část kurzu používá následující rutinu PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Vytvoří novou skupinu převzetí služeb při selhání spravované instance SQL Azure.  |


---


## <a name="test-failover"></a>Testovací převzetí služeb při selhání
V tomto kroku dojde k selhání skupiny převzetí služeb při selhání pro sekundární server a následnému navrácení služeb po obnovení pomocí Azure Portal. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Testovací převzetí služeb při selhání pomocí Azure Portal. 


1. V [Azure Portal](https://portal.azure.com) přejděte do _sekundární_ spravované instance a v části nastavení vyberte **instance skupiny převzetí služeb při selhání** . 
1. Zkontrolujte, která spravovaná instance je primární a která spravovaná instance je sekundární. 
1. Vyberte **převzetí služeb při selhání** a pak pro upozornění na odpojené relace TDS vyberte **Ano** . 

   ![Převzít služby při selhání skupiny převzetí služeb při selhání](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Zkontrolujte, která spravovaná instance je primární a která spravovaná instance je sekundární. Pokud převzetí služeb při selhání proběhlo úspěšně, měly by tyto dvě instance mít přepnuté role. 

   ![Spravované instance mají po převzetí služeb při selhání přepnuté role.](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Vraťte se do nové _sekundární_ spravované instance a znovu vyberte **převzetí služeb při selhání** , aby se primární instance znovu nezdařila do primární role. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Testovací převzetí služeb při selhání pomocí PowerShellu 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Vraťte skupinu převzetí služeb při selhání zpět na primární server:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Načte nebo vypíše skupiny převzetí služeb při selhání spravované instance SQL.| 
| [Switch – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Provede převzetí služeb při selhání pro skupinu převzetí služeb při selhání spravované instance SQL. | 

---



## <a name="clean-up-resources"></a>Vyčištění prostředků
Vyčistěte prostředky tak, že nejprve odstraníte spravované instance, potom virtuální cluster, potom všechny zbývající prostředky a nakonec skupinu prostředků. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com). 
1. Vyberte spravované instance a pak vyberte **Odstranit**. `yes`Do textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. Dokončení tohoto procesu může nějakou dobu trvat na pozadí a až do dokončení, nebudete moci odstranit *virtuální cluster* ani žádné jiné závislé prostředky. Sledujte odstranění na kartě **aktivita** a potvrďte, že se vaše spravovaná instance odstranila. 
1. Po odstranění spravované instance odstraňte *virtuální cluster* tak, že ho vyberete ve vaší skupině prostředků, a pak zvolíte **Odstranit**. `yes`Do textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. 
1. Odstraňte všechny zbývající prostředky. `yes`Do textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. 
1. Odstraňte skupinu prostředků výběrem možnosti **Odstranit skupinu prostředků**, zadáním názvu skupiny prostředků `myResourceGroup` a pak výběrem možnosti **Odstranit**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skupinu prostředků budete muset odebrat dvakrát. Při prvním odebrání skupiny prostředků se spravované instance a virtuální clustery odstraní, ale chybová zpráva se pak nezdaří `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` . Spusťte příkaz Remove-AzResourceGroup a podruhé odstraňte všechny zbývající prostředky i skupinu prostředků.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

Tato část kurzu používá následující rutinu PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. |

---

## <a name="full-script"></a>Celý skript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podsíť ve virtuální síti. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Vytvoří směrovací tabulku. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfiguraci podsítě pro virtuální síť.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Načte skupinu zabezpečení sítě. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje skupinu zabezpečení sítě.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Přidá trasu do směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje směrovací tabulku.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Vrátí informace o spravované instanci Azure SQL. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci protokolu IP pro bránu virtuální sítě. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu virtuální sítě. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Vytvoří připojení mezi dvěma branami virtuální sítě.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Vytvoří novou skupinu převzetí služeb při selhání spravované instance SQL.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Načte nebo vypíše skupiny převzetí služeb při selhání spravované instance SQL.| 
| [Switch – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Provede převzetí služeb při selhání pro skupinu převzetí služeb při selhání spravované instance SQL. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal) 

Pro Azure Portal nejsou k dispozici žádné skripty.

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali skupinu převzetí služeb při selhání mezi dvěma spravovanými instancemi. Naučili jste se:

> [!div class="checklist"]
> - Vytvoří primární spravovanou instanci.
> - Vytvořte sekundární spravovanou instanci jako součást [skupiny převzetí služeb při selhání](../database/auto-failover-group-overview.md). 
> - Testovací převzetí služeb při selhání.

Přejděte k dalšímu rychlému startu, jak se připojit ke spravované instanci SQL a jak obnovit databázi do spravované instance SQL: 

> [!div class="nextstepaction"]
> [Připojit k spravované instanci SQL](connect-vm-instance-configure.md) 
>  [Obnovení databáze do spravované instance SQL](restore-sample-database-quickstart.md)


