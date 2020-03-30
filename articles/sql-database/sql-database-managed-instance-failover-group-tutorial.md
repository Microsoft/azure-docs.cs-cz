---
title: 'Kurz: Přidání spravované instance do skupiny převzetí služeb při selhání'
description: Naučte se nakonfigurovat skupinu převzetí služeb při selhání pro vaši instanci spravované službou Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462645"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Kurz: Přidání instance spravované databází SQL do skupiny s podporou převzetí služeb při selhání

Přidejte instanci spravované databází SQL do skupiny s podporou převzetí služeb při selhání. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> - Vytvoření primární spravované instance
> - Vytvořte sekundární spravovanou instanci jako součást [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md). 
> - Testovací převzetí služeb při selhání

  > [!NOTE]
  > - Při procházení tohoto kurzu se ujistěte, že konfigurujete prostředky s [požadavky pro nastavení skupin převzetí služeb při selhání pro spravovanou instanci](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Vytvoření spravované instance může trvat značné množství času. V důsledku toho může dokončení tohoto kurzu trvat několik hodin. Další informace o době zřizování naleznete v tématu [operace správy spravovaných instancí](sql-database-managed-instance.md#managed-instance-management-operations). 
  > - Spravované instance, které se účastní skupiny s podporou převzetí služeb při selhání, vyžadují buď [ExpressRoute,](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) nebo dvě připojené brány VPN. Tento kurz obsahuje postup pro vytváření a připojení bran VPN. Pokud již máte nakonfigurovanou trasu ExpressRoute, přeskočte tyto kroky. 


## <a name="prerequisites"></a>Požadavky

# <a name="portal"></a>[Portál](#tab/azure-portal)
Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující: 

- Předplatné Azure. [Vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) pokud ho ještě nemáte.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li dokončit kurz, ujistěte se, že máte následující položky:

- Předplatné Azure. [Vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) pokud ho ještě nemáte.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 – Vytvoření skupiny prostředků a primární spravované instance
V tomto kroku vytvoříte skupinu prostředků a primární spravovanou instanci pro vaši skupinu převzetí služeb při selhání pomocí portálu Azure nebo Prostředí PowerShell. 


# <a name="portal"></a>[Portál](#tab/azure-portal) 

Vytvořte skupinu prostředků a primární spravovanou instanci pomocí portálu Azure. 

1. V levé nabídce portálu Azure vyberte **Azure SQL.** Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Výběrem **možnosti + Přidat** otevřete stránku **možnosti Vybrat nasazení SQL.** Další informace o různých databázích můžete zobrazit výběrem možnosti Zobrazit podrobnosti na dlaždici Databáze.
1. Na dlaždici **spravovaných instancí SQL vyberte** **Vytvořit.** 

    ![Vybrat spravovanou instanci](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na stránce **Vytvořit spravovanou instanci Azure SQL Database** na kartě **Základy**
    1. V části **Podrobnosti projektu**vyberte **předplatné** v rozevíracím seznamku a pak zvolte **Vytvořit novou** skupinu prostředků. Zadejte název skupiny prostředků, `myResourceGroup`například . 
    1. V části **Podrobnosti spravované instance**zadejte název spravované instance a oblast, do které chcete spravovanou instanci nasadit. Ponechejte **úložiště Compute +** na výchozí hodnoty. 
    1. V **části Účet správce**zadejte `azureuser`přihlášení správce, například , a komplexní heslo správce. 

    ![Vytvořit primární MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Zbývající nastavení ponechejte na výchozích hodnotách a vyberte **Zkontrolovat + vytvořit a** zkontrolujte nastavení spravované instance. 
1. Vyberte **Vytvořit,** chcete-li vytvořit primární spravovanou instanci. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu prostředků a primární spravovanou instanci pomocí PowerShellu. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
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
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
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
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
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
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
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
   Write-host "Primary managed instance created successfully."
   ```

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure.  |
| [Nová virtuální síť Az](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť.  |
| [Doplněk AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Virtuální síť Get-Az](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Získá podsíť ve virtuální síti. | 
| [Nová skupina zabezpečení sítě AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. | 
| [Nová-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Vytvoří směrovací tabulku. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfiguraci podsítě pro virtuální síť.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Získá skupinu zabezpečení sítě. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje skupinu zabezpečení sítě.  | 
| [Doplněk AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Přidá trasu do směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje směrovací tabulku.  |
| [Nová instance AzSql](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci Azure SQL Database.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Vytvoření sekundární virtuální sítě
Pokud používáte portál Azure k vytvoření spravované instance, budete muset vytvořit virtuální síť samostatně, protože je požadavek, že podsíť primární a sekundární spravované instance nemají překrývající se rozsahy. Pokud ke konfiguraci spravované instance používáte PowerShell, přeskočte na krok 3. 

# <a name="portal"></a>[Portál](#tab/azure-portal) 
Chcete-li ověřit rozsah podsítí primární virtuální sítě, postupujte takto:
1. Na [webu Azure Portal](https://portal.azure.com)přejděte do skupiny prostředků a vyberte virtuální síť pro primární instanci. 
1. V části **Nastavení** vyberte **Podsítě** a poznamenejte si **rozsah Adresa**. Rozsah adres podsítě virtuální sítě pro sekundární spravovanou instanci to nemůže překrývat. 


   ![Primární podsíť](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Chcete-li vytvořit virtuální síť, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Vytvořit prostředek** a vyhledejte *virtuální síť*. 
1. Vyberte možnost **Virtuální síť** publikovaná společností Microsoft a pak na další stránce vyberte **Vytvořit.** 
1. Vyplňte požadovaná pole pro konfiguraci virtuální sítě pro sekundární spravovanou instanci a pak vyberte **Vytvořit**. 

   V následující tabulce jsou uvedeny hodnoty nezbytné pro sekundární virtuální síť:

    | **Pole** | Hodnota |
    | --- | --- |
    | **Název** |  Název virtuální sítě, která má být použita sekundární `vnet-sql-mi-secondary`spravovanou instancí, například . |
    | **Adresní prostor** | Adresní prostor pro virtuální síť, například `10.128.0.0/16`. | 
    | **Předplatné** | Předplatné, kde se nacházejí primární spravovaná instance a skupina prostředků. |
    | **Oblasti** | Umístění, kde budete nasazovat sekundární spravované instance. |
    | **Podsíť** | Název podsítě. `default`je k dispozici ve výchozím nastavení. |
    | **Rozsah adres**| Rozsah adres pro podsíť. To se musí lišit od rozsahu adres podsítě používaného virtuální sítí `10.128.0.0/24`primární spravované instance, například .  |
    | &nbsp; | &nbsp; |

    ![Hodnoty sekundární virtuální sítě](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento krok je nutný jenom v případě, že k nasazení spravované instance používáte portál Azure. Pokud používáte PowerShell, přeskočíte na krok 3. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 – Vytvoření sekundární spravované instance
V tomto kroku vytvoříte sekundární spravovanou instanci na webu Azure Portal, která také nakonfiguruje síť mezi dvěma spravovanými instancemi. 

Druhá spravovaná instance musí:
- Buďte prázdní. 
- Mají jinou podsíť a rozsah IP adres než primární spravovaná instance. 

# <a name="portal"></a>[Portál](#tab/azure-portal) 

Vytvořte sekundární spravovanou instanci pomocí portálu Azure. 

1. V levé nabídce portálu Azure vyberte **Azure SQL.** Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Výběrem **možnosti + Přidat** otevřete stránku **možnosti Vybrat nasazení SQL.** Další informace o různých databázích můžete zobrazit výběrem možnosti Zobrazit podrobnosti na dlaždici Databáze.
1. Na dlaždici **spravovaných instancí SQL vyberte** **Vytvořit.** 

    ![Vybrat spravovanou instanci](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. Na kartě **Základy** na stránce **Vytvořit spravovanou instanci azure SQL databáze** vyplňte požadovaná pole pro konfiguraci sekundární spravované instance. 

   V následující tabulce jsou uvedeny hodnoty nezbytné pro sekundární spravovanou instanci:
 
    | **Pole** | Hodnota |
    | --- | --- |
    | **Předplatné** |  Předplatné, kde je vaše primární spravovaná instance. |
    | **Skupina prostředků**| Skupina prostředků, kde je primární spravovaná instance. |
    | **Název spravované instance** | Název nové sekundární spravované instance, například`sql-mi-secondary`  | 
    | **Oblasti**| Umístění pro sekundární spravovanou instanci.  |
    | **Přihlašovací jméno správce spravované instance** | Přihlášení, které chcete použít pro novou sekundární spravovanou instanci, například `azureuser`. |
    | **Heslo** | Komplexní heslo, které bude používat přihlášení správce pro novou sekundární spravovanou instanci.  |
    | &nbsp; | &nbsp; |

1. Na kartě **Síť** vyberte v rozevíracím **seznamu**virtuální síť, kterou jste vytvořili pro sekundární spravovanou instanci.

   ![Sekundární SÍTĚ MI](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Na kartě **Další nastavení** v případě **geografické replikace**zvolte **možnost Ano** použít jako sekundární převzetí služeb _při selhání_. V rozevíracím souboru vyberte primární spravovanou instanci. 
    1. Ujistěte se, že řazení a časové pásmo odpovídá primární spravované instanci. Primární spravovaná instance vytvořená `SQL_Latin1_General_CP1_CI_AS` v tomto `(UTC) Coordinated Universal Time` kurzu používá výchozí řazení a časové pásmo. 

   ![Sekundární SÍTĚ MI](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Vyberte **Zkontrolovat + vytvořit** a zkontrolujte nastavení sekundární spravované instance. 
1. Vyberte **Vytvořit,** chcete-li vytvořit sekundární spravovanou instanci. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte sekundární spravovanou instanci pomocí PowerShellu. 

   ```powershell-interactive
   # Configure secondary virtual network
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
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
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
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
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
   Write-host "Secondary managed instance created successfully."
   ```

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure.  |
| [Nová virtuální síť Az](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť.  |
| [Doplněk AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Virtuální síť Get-Az](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Získá podsíť ve virtuální síti. | 
| [Nová skupina zabezpečení sítě AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. | 
| [Nová-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Vytvoří směrovací tabulku. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfiguraci podsítě pro virtuální síť.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Získá skupinu zabezpečení sítě. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje skupinu zabezpečení sítě.  | 
| [Doplněk AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Přidá trasu do směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje směrovací tabulku.  |
| [Nová instance AzSql](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci Azure SQL Database.  |

---

## <a name="4---create-primary-gateway"></a>4 - Vytvoření primární brány 
Pro dvě spravované instance, které se mají účastnit skupiny s podporou převzetí služeb při selhání, musí být mezi virtuálními sítěmi dvou spravovaných instancí nakonfigurována buď ExpressRoute, nebo brána, aby byla povolena síťová komunikace. Pokud se rozhodnete nakonfigurovat [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) namísto připojení dvou bran VPN, přejděte na [krok 7](#7---create-a-failover-group).  

Tento článek obsahuje kroky k vytvoření dvou bran VPN a jejich připojení, ale můžete přeskočit na vytvoření skupiny převzetí služeb při selhání, pokud jste místo toho nakonfigurovali ExpressRoute. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Vytvořte bránu pro virtuální síť primární spravované instance pomocí portálu Azure. 


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
    | **Název** | Název brány virtuální sítě, například `primary-mi-gateway`. | 
    | **Oblasti** | Oblast, kde je sekundární spravovaná instance. |
    | **Typ brány** | Vyberte **možnost VPN**. |
    | **Typ vpn** | Vybrat **na základě trasy** |
    | **Sku**| Ponechat `VpnGw1`výchozí hodnotu . |
    | **Umístění**| Umístění, kde je primární spravovaná instance a primární virtuální síť.   |
    | **Virtuální síť**| Vyberte virtuální síť, která byla vytvořena `vnet-sql-mi-primary`v části 2, například . |
    | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
    | **Název veřejné IP adresy**| Zadejte název adresy IP, `primary-gateway-IP`například . |
    | &nbsp; | &nbsp; |

1. Ostatní hodnoty ponechejte jako výchozí a pak vyberte **Revize + vytvořit a** zkontrolujte nastavení brány virtuální sítě.

   ![Nastavení primární brány](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Vyberte **Vytvořit,** chcete-li vytvořit novou bránu virtuální sítě. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte bránu pro virtuální síť primární spravované instance pomocí PowerShellu. 

   ```powershell-interactive
   # Create primary gateway
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

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Virtuální síť Get-Az](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. |
| [Doplněk AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Získá podsíť ve virtuální síti. |
| [Nová adresa AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [Nový-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci IP adresy pro bránu virtuální sítě. |
| [Nová-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu virtuální sítě. |


---


## <a name="5---create-secondary-gateway"></a>5 - Vytvoření sekundární brány 
V tomto kroku vytvořte bránu pro virtuální síť sekundární spravované instance pomocí portálu Azure, 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Pomocí portálu Azure opakujte kroky v předchozí části a vytvořte podsíť virtuální sítě a bránu pro sekundární spravovanou instanci. Vyplňte požadovaná pole pro konfiguraci brány pro sekundární spravovanou instanci. 

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
   
   Write-host "Creating primary gateway..."
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

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Virtuální síť Get-Az](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. |
| [Doplněk AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Získá podsíť ve virtuální síti. |
| [Nová adresa AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [Nový-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci IP adresy pro bránu virtuální sítě. |
| [Nová-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu virtuální sítě. |

---


## <a name="6---connect-the-gateways"></a>6 - Připojení bran
V tomto kroku vytvořte obousměrné připojení mezi dvěma branami dvou virtuálních sítí. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Připojte dvě brány pomocí portálu Azure. 


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

Tato část kurzu používá následující rutinu prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Vytvoří připojení mezi dvěma branami virtuální sítě.   |

---


## <a name="7---create-a-failover-group"></a>7 - Vytvoření skupiny převzetí služeb při selhání
V tomto kroku vytvoříte skupinu převzetí služeb při selhání a přidáte do ní obě spravované instance. 


# <a name="portal"></a>[Portál](#tab/azure-portal)
Vytvořte skupinu převzetí služeb při selhání pomocí portálu Azure. 


1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte primární spravovanou instanci, `sql-mi-primary`kterou jste vytvořili v první části, například . 
1. V části **Nastavení**přejděte do **skupin y převzetí služeb při selhání instance** a pak zvolte Přidat **skupinu** a otevřete stránku **Skupina převzetí služeb při selhání instance.** 

   ![Přidání skupiny převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání instance** zadejte `failovergrouptutorial` název skupiny s podporou převzetí `sql-mi-secondary` služeb při selhání, například a pak zvolte sekundární spravovanou instanci, například z rozevíracího souboru. Chcete-li vytvořit skupinu s podporou převzetí služeb při selhání, vyberte **možnost Vytvořit.** 

   ![Vytvořit skupinu převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Po dokončení nasazení skupiny s podporou převzetí služeb při selhání budete převedena zpět na stránku **skupiny převzetí služeb při selhání.** 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vytvořte skupinu s podporou převzetí služeb při selhání pomocí prostředí PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Tato část kurzu používá následující rutinu prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Vytvoří novou skupinu převzetí služeb při selhání azure SQL Database spravované instance.  |


---


## <a name="8---test-failover"></a>8 - Zkušební převzetí služeb při selhání
V tomto kroku se nezdaří vaše skupina převzetí služeb při selhání přejdete na sekundární server a potom poselháníní pomocí portálu Azure. 


# <a name="portal"></a>[Portál](#tab/azure-portal)
Otestujte převzetí služeb při selhání pomocí portálu Azure. 


1. Přejděte na _sekundární_ spravovanou instanci na [webu Azure Portal](https://portal.azure.com) a v nastavení vyberte **skupiny převzetí služeb při selhání instance.** 
1. Zkontrolujte, která spravovaná instance je primární a která spravovaná instance je sekundární. 
1. Vyberte **převzetí služeb při selhání** a pak vyberte **Ano** na upozornění na odpojené relace TDS. 

   ![Převzetí služeb při selhání skupiny převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Zkontrolujte, která instance manged je primární a která instance je sekundární. Pokud převzetí služeb při selhání proběhlo úspěšně, měly dvě instance přepínat role. 

   ![Spravované instance přepnuly role po převzetí služeb při selhání](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Přejděte na novou _sekundární_ spravovanou instanci a znovu vyberte **převzetí služeb při selhání,** aby primární instance selhala zpět do primární role. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Otestujte převzetí služeb při selhání pomocí prostředí PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Vrátit skupinu převzetí služeb při selhání zpět na primární server:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Získá nebo zobrazí seznam skupin služeb převzetí služeb při selhání spravované instance.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Provede převzetí služeb při selhání skupiny převzetí služeb při selhání spravované instance. | 

---



## <a name="clean-up-resources"></a>Vyčištění prostředků
Vyčistěte prostředky nejprve odstraněním spravované instance, pak virtuálního clusteru, pak všech zbývajících prostředků a nakonec skupiny prostředků. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Přejděte do skupiny prostředků na [webu Azure Portal](https://portal.azure.com). 
1. Vyberte spravované instance a pak vyberte **Odstranit**. Zadáním `yes` textového pole potvrďte, že chcete prostředek odstranit, a pak vyberte **Odstranit**. Tento proces může trvat nějakou dobu na pozadí a dokud se to neudělá, nebude možné odstranit *virtuální cluster* nebo jiné závislé prostředky. Sledujte odstranění na kartě Aktivita a potvrďte, že vaše spravovaná instance byla odstraněna. 
1. Po odstranění spravované instance odstraňte *virtuální cluster* tak, že ho vyberete ve skupině prostředků a pak **zvolíte Odstranit**. Zadáním `yes` textového pole potvrďte, že chcete prostředek odstranit, a pak vyberte **Odstranit**. 
1. Odstraňte všechny zbývající prostředky. Zadáním `yes` textového pole potvrďte, že chcete prostředek odstranit, a pak vyberte **Odstranit**. 
1. Skupinu prostředků odstraňte tak, že vyberete **Odstranit skupinu** `myResourceGroup`prostředků , zadáte název skupiny prostředků a pak vyberete **Odstranit**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skupinu prostředků budete muset odebrat dvakrát. První odebrání skupiny prostředků odebere spravované instance a virtuální clustery, `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`ale poté se chybová zpráva nezdaří . Spusťte příkaz Remove-AzResourceGroup podruhé, abyste odebrali všechny zbývající prostředky i skupinu prostředků.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

Tato část kurzu používá následující rutinu prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. |

---

## <a name="full-script"></a>Celý skript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure.  |
| [Nová virtuální síť Az](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť.  |
| [Doplněk AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Virtuální síť Get-Az](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Získá podsíť ve virtuální síti. | 
| [Nová skupina zabezpečení sítě AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. | 
| [Nová-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Vytvoří směrovací tabulku. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfiguraci podsítě pro virtuální síť.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Získá skupinu zabezpečení sítě. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje skupinu zabezpečení sítě.  | 
| [Doplněk AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Přidá trasu do směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje směrovací tabulku.  |
| [Nová instance AzSql](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci Azure SQL Database.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Vrátí informace o instanci spravované databáze Azure SQL. |
| [Nová adresa AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [Nový-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci IP adresy pro bránu virtuální sítě. |
| [Nová-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu virtuální sítě. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Vytvoří připojení mezi dvěma branami virtuální sítě.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Vytvoří novou skupinu převzetí služeb při selhání azure SQL Database spravované instance.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Získá nebo zobrazí seznam skupin služeb převzetí služeb při selhání spravované instance.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Provede převzetí služeb při selhání skupiny převzetí služeb při selhání spravované instance. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

# <a name="portal"></a>[Portál](#tab/azure-portal) 

Pro portál Azure portal nejsou k dispozici žádné skripty.

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali skupinu převzetí služeb při selhání mezi dvěma spravovanými instancemi. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření primární spravované instance
> - Vytvořte sekundární spravovanou instanci jako součást [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md). 
> - Testovací převzetí služeb při selhání

Převést na další rychlý start o tom, jak se připojit ke spravované instanci a jak obnovit databázi do spravované instance: 

> [!div class="nextstepaction"]
> [Připojení ke spravované instanci](sql-database-managed-instance-configure-vm.md)
> [Obnovení databáze ke spravované instanci](sql-database-managed-instance-get-started-restore.md)


