---
title: 'Kurz: přidání do skupiny s automatickým převzetím služeb při selhání'
titleSuffix: Azure SQL Managed Instance
description: V tomto kurzu vytvoříte dvě spravované instance Azure SQL jako primární a sekundární a pak je přidáte do skupiny s automatickým převzetím služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: 925e6788035952a4e7b54b8d50b910243a754a09
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041280"
---
# <a name="tutorial-add-a-sql-managed-instance-to-a-failover-group"></a>Kurz: Přidání spravované instance SQL do skupiny převzetí služeb při selhání
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Přidejte spravovanou instanci Azure SQL do skupiny převzetí služeb při selhání. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> - Vytvoření primární spravované instance SQL
> - Vytvořte sekundární spravovanou instanci SQL jako součást [skupiny převzetí služeb při selhání](../database/auto-failover-group-overview.md). 
> - Testovací převzetí služeb při selhání

  > [!NOTE]
  > - Při procházení tohoto kurzu se ujistěte, že konfigurujete prostředky s [požadavky pro nastavení skupin převzetí služeb při selhání pro spravovanou instanci SQL](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Vytvoření spravované instance SQL může trvat poměrně dlouhou dobu. V důsledku toho může dokončení tohoto kurzu trvat několik hodin. Další informace o časech zřizování najdete v tématu [operace správy spravované instance SQL](sql-managed-instance-paas-overview.md#management-operations). 
  > - Spravované instance SQL účastnící se skupiny převzetí služeb při selhání vyžadují buď [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) , nebo dvě připojené brány VPN Gateway. Tento kurz popisuje kroky pro vytvoření a připojení bran VPN. Tento postup přeskočte, pokud již máte nakonfigurovanou ExpressRoute. 


## <a name="prerequisites"></a>Požadavky

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující: 

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
K dokončení tohoto kurzu se ujistěte, že máte následující položky:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-sql-mi"></a>1. Vytvoření skupiny prostředků a primárního SQL MI

V tomto kroku vytvoříte skupinu prostředků a primární spravovanou instanci SQL pro skupinu převzetí služeb při selhání pomocí Azure Portal nebo PowerShellu. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal) 

Vytvořte skupinu prostředků a svoji primární SQL spravovanou instanci pomocí Azure Portal. 

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a potom `Azure SQL` do vyhledávacího pole zadejte. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o různých databázích můžete zobrazit tak, že na dlaždici databáze vyberete Zobrazit podrobnosti.
1. Na dlaždici **spravované instance SQL** vyberte **vytvořit** . 

    ![Vybrat spravovanou instanci SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Na stránce **Vytvoření spravované instance SQL Azure** na kartě **základy**
    1. V části **Podrobnosti o projektu**vyberte v rozevíracím seznamu své **předplatné** a pak zvolte **vytvořit novou** skupinu prostředků. Zadejte název vaší skupiny prostředků, jako je například `myResourceGroup` . 
    1. V části **Podrobnosti spravované instance SQL**zadejte název spravované instance SQL a oblast, do které chcete nasadit SPRAVOVANOU instanci SQL. Ve službě **COMPUTE + Storage** ponechte výchozí hodnoty. 
    1. V části **účet správce**zadejte přihlašovací jméno správce, například `azureuser` a složitá hesla správce. 

    ![Vytvořit primární MI](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Ponechte zbývající nastavení na výchozí hodnoty a vyberte **zkontrolovat + vytvořit** a zkontrolujte nastavení spravované instance SQL. 
1. Vyberte **vytvořit** a vytvořte svoji primární SPRAVOVANOU instanci SQL. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu prostředků a primární spravovanou instanci SQL pomocí PowerShellu. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary SQL Managed Instance
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
   
   
   # Set the networking values for your secondary SQL Managed Instance
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
   
   
   
   # Set the SQL Managed Instance name for the new SQL Managed Instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
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
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
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
   
   
   # Create primary SQL Managed Instance
   
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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci Azure SQL.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 – vytvoření sekundární virtuální sítě

Pokud používáte Azure Portal k vytvoření spravované instance SQL, budete muset vytvořit virtuální síť samostatně, protože existuje požadavek, aby podsíť primární a sekundární spravované instance SQL nepřesahoval rozsahy. Pokud ke konfiguraci spravované instance SQL používáte PowerShell, přeskočte dopředu ke kroku 3. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal) 

Pokud chcete ověřit rozsah podsítě vaší primární virtuální sítě, postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)přejděte do skupiny prostředků a vyberte virtuální síť pro vaši primární instanci.  
2. V části **Nastavení** vyberte **podsítě** a poznamenejte si **Rozsah adres**. Rozsah adres podsítě virtuální sítě pro sekundární spravovanou instanci SQL se nemůže překrývat. 


   ![Primární podsíť](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Pokud chcete vytvořit virtuální síť, postupujte takto:

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek** a vyhledejte *virtuální síť*. 
1. Vyberte možnost **Virtual Network** publikovanou Microsoftem a pak na další stránce vyberte **vytvořit** . 
1. Vyplňte požadovaná pole pro konfiguraci virtuální sítě pro sekundární spravovanou instanci SQL a pak vyberte **vytvořit**. 

   Následující tabulka uvádí hodnoty nutné pro sekundární virtuální síť:

    | **Pole** | Hodnota |
    | --- | --- |
    | **Název** |  Název virtuální sítě, která se má použít u sekundární instance spravované SQL, například `vnet-sql-mi-secondary` . |
    | **Adresní prostor** | Adresní prostor pro virtuální síť, například `10.128.0.0/16` . | 
    | **Předplatné** | Předplatné, ve kterém se nachází vaše primární instance spravované SQL a skupina prostředků |
    | **Věřitel** | Umístění, do kterého budete nasazovat sekundární spravovanou instanci SQL. |
    | **Podsíť** | Název vaší podsítě. `default`je ve výchozím nastavení k dispozici pro vás. |
    | **Rozsah adres**| Rozsah adres pro vaši podsíť. Musí se lišit od rozsahu adres podsítě, který používá virtuální síť vaší primární spravované instance SQL, například `10.128.0.0/24` .  |
    | &nbsp; | &nbsp; |

    ![Hodnoty sekundární virtuální sítě](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento krok je nutný jenom v případě, že k nasazení spravované instance SQL používáte Azure Portal. Pokud používáte PowerShell, přejděte dopředu ke kroku 3. 

---

## <a name="3---create-a-secondary-sql-managed-instance"></a>3. vytvoření sekundární spravované instance SQL
V tomto kroku vytvoříte sekundární instanci SQL spravovanou v Azure Portal, která bude také konfigurovat síť mezi dvěma spravovanými instancemi SQL. 

Vaše druhá spravovaná instance SQL musí:
- Být prázdné. 
- Mít jinou podsíť a rozsah IP adres než primární spravovaná instance SQL. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal) 

Pomocí Azure Portal Vytvořte sekundární spravovanou instanci SQL. 

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o různých databázích můžete zobrazit tak, že na dlaždici databáze vyberete Zobrazit podrobnosti.
1. Na dlaždici **spravované instance SQL** serveru vyberte **vytvořit** . 

    ![Vybrat spravovanou instanci SQL](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Na kartě **základy** na stránce **Vytvoření spravované instance Azure SQL** vyplňte požadovaná pole pro konfiguraci sekundární spravované instance SQL. 

   Následující tabulka uvádí hodnoty nutné pro sekundární spravovanou instanci SQL:
 
    | **Pole** | Hodnota |
    | --- | --- |
    | **Předplatné** |  Předplatné, ve kterém je vaše primární spravovaná instance SQL. |
    | **Skupina prostředků**| Skupina prostředků, ve které je vaše primární spravovaná instance SQL. |
    | **Název spravované instance SQL** | Název vaší nové sekundární spravované instance SQL, například`sql-mi-secondary`  | 
    | **Věřitel**| Umístění sekundární spravované instance SQL  |
    | **Přihlašovací jméno správce spravované instance SQL** | Přihlašovací jméno, které chcete použít pro novou sekundární spravovanou instanci SQL, například `azureuser` . |
    | **Heslo** | Složitá hesla, která budou používána přihlašovacími údaji správce pro novou sekundární instanci SQL Managed.  |
    | &nbsp; | &nbsp; |

1. Na kartě **sítě** pro **Virtual Network**vyberte z rozevíracího seznamu virtuální síť, kterou jste vytvořili pro sekundární SQL spravovanou instanci.

   ![Sekundární síť MI](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Na kartě **Další nastavení** pro **geografickou replikaci**vyberte **Ano** , aby se _použil jako sekundární převzetí služeb při selhání_. Z rozevíracího seznamu vyberte primární spravovanou instanci SQL. 
    1. Ujistěte se, že kolace a časová zóna odpovídají hodnotě primární spravované instance SQL. Primární SQL spravovaná instance vytvořená v tomto kurzu použila výchozí `SQL_Latin1_General_CP1_CI_AS` řazení a `(UTC) Coordinated Universal Time` časové pásmo. 

   ![Sekundární síť MI](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Vyberte **zkontrolovat + vytvořit** a zkontrolujte nastavení sekundární SQL spravované instance. 
1. Vyberte **vytvořit** a vytvořte svoji sekundární SPRAVOVANOU instanci SQL. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte sekundární spravovanou instanci SQL pomocí PowerShellu. 

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
   
   # Configure secondary SQL Managed Instance subnet
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
   
   # Create secondary SQL Managed Instance
   
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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci Azure SQL.  |

---

## <a name="4---create-primary-gateway"></a>4 – vytvoření primární brány 

Aby se dvě spravované instance SQL účastnily skupiny převzetí služeb při selhání, musí existovat buď ExpressRoute, nebo brána nakonfigurovaná mezi virtuálními sítěmi dvou spravovaných instancí SQL, aby bylo možné síťovou komunikaci. Pokud se rozhodnete nakonfigurovat [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) místo připojení dvou bran VPN Gateway, přejděte ke [kroku 7](#7---create-a-failover-group).  

Tento článek popisuje kroky pro vytvoření dvou bran sítě VPN a jejich připojení, ale můžete přeskočit k vytvoření skupiny převzetí služeb při selhání, pokud jste místo toho nakonfigurovali ExpressRoute. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte bránu pro virtuální síť vaší primární spravované instance SQL pomocí Azure Portal. 


1. V [Azure Portal](https://portal.azure.com)přejdete do skupiny prostředků a vyberete prostředek **virtuální sítě** pro vaši primární spravovanou instanci SQL. 
1. V části **Nastavení** vyberte **podsítě** a pak vyberte, pokud chcete přidat novou **podsíť brány**. Ponechte výchozí hodnoty. 

   ![Přidat bránu pro primární spravovanou instanci SQL](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Po vytvoření brány podsítě v levém navigačním podokně vyberte **vytvořit prostředek** a potom `Virtual network gateway` do vyhledávacího pole zadejte. Vyberte prostředek **brány virtuální sítě** publikovaný **Microsoftem**. 

   ![Vytvořit novou bránu virtuální sítě](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Vyplňte požadovaná pole a nakonfigurujte bránu vaší primární SQL spravované instance. 

   Následující tabulka uvádí hodnoty nutné pro bránu primární spravované instance SQL:
 
    | **Pole** | Hodnota |
    | --- | --- |
    | **Předplatné** |  Předplatné, ve kterém je vaše primární spravovaná instance SQL. |
    | **Název** | Název brány virtuální sítě, například `primary-mi-gateway` . | 
    | **Věřitel** | Oblast, ve které je vaše primární spravovaná instance SQL. |
    | **Typ brány** | Vyberte **VPN**. |
    | **Typ sítě VPN** | Vybrat **na základě trasy** |
    | **SKLADOVÉ**| Ponechte výchozí hodnotu `VpnGw1` . |
    | **Umístění**| Umístění, kde je vaše primární instance SQL spravované a primární virtuální síť.   |
    | **Virtuální síť**| Vyberte virtuální síť, která byla vytvořena v části 2, například `vnet-sql-mi-primary` . |
    | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
    | **Název veřejné IP adresy**| Zadejte název vaší IP adresy, například `primary-gateway-IP` . |
    | &nbsp; | &nbsp; |

1. Ostatní hodnoty ponechte jako výchozí a potom vyberte **zkontrolovat + vytvořit** a zkontrolujte nastavení brány virtuální sítě.

   ![Nastavení primární brány](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Vyberte **vytvořit** a vytvořte novou bránu virtuální sítě. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte bránu pro virtuální síť vaší primární spravované instance SQL pomocí PowerShellu. 

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

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podsíť ve virtuální síti. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci protokolu IP pro bránu Virtual Network. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu Virtual Network. |


---


## <a name="5---create-secondary-gateway"></a>5 – vytvoření sekundární brány 
V tomto kroku vytvoříte bránu pro virtuální síť sekundární spravované instance SQL pomocí Azure Portal, 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí Azure Portal pro vytvoření podsítě virtuální sítě a brány pro sekundární spravovanou instanci SQL opakujte postup v předchozí části. Vyplňte požadovaná pole a nakonfigurujte bránu pro sekundární spravovanou instanci SQL. 

   V následující tabulce jsou uvedeny hodnoty nutné pro bránu sekundární spravované instance SQL:

   | **Pole** | Hodnota |
   | --- | --- |
   | **Předplatné** |  Předplatné, ve kterém je vaše sekundární spravovaná instance SQL. |
   | **Název** | Název brány virtuální sítě, například `secondary-mi-gateway` . | 
   | **Věřitel** | Oblast, ve které je vaše sekundární spravovaná instance SQL. |
   | **Typ brány** | Vyberte **VPN**. |
   | **Typ sítě VPN** | Vybrat **na základě trasy** |
   | **SKLADOVÉ**| Ponechte výchozí hodnotu `VpnGw1` . |
   | **Umístění**| Umístění, kde je sekundární instance spravované SQL a sekundární virtuální sítí.   |
   | **Virtuální síť**| Vyberte virtuální síť, která byla vytvořena v části 2, například `vnet-sql-mi-secondary` . |
   | **Veřejná IP adresa**| Vyberte, že chcete **vytvořit novou** IP adresu. |
   | **Název veřejné IP adresy**| Zadejte název vaší IP adresy, například `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Nastavení sekundární brány](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte bránu pro virtuální síť sekundární spravované instance SQL pomocí PowerShellu. 

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

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podsíť ve virtuální síti. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci protokolu IP pro bránu Virtual Network. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu Virtual Network. |

---


## <a name="6---connect-the-gateways"></a>6. připojení bran
V tomto kroku vytvořte obousměrné připojení mezi dvěma branami obou virtuálních sítí. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Připojte dvě brány pomocí Azure Portal. 


1. Vyberte **vytvořit prostředek** z [Azure Portal](https://portal.azure.com).
1. Do `connection` vyhledávacího pole zadejte a stiskněte klávesu ENTER pro hledání, které vás přesměruje do prostředku **připojení** publikovaného společností Microsoft.
1. Vyberte **vytvořit** a vytvořte připojení. 
1. Na kartě **základy** vyberte následující hodnoty a pak vyberte **OK**. 
    1. Jako `VNet-to-VNet` **Typ připojení**vyberte. 
    1. Vyberte vaše předplatné z rozevíracího seznamu. 
    1. V rozevíracím seznamu vyberte skupinu prostředků pro spravovanou instanci SQL. 
    1. Z rozevíracího seznamu vyberte umístění vaší primární spravované instance SQL. 
1. Na kartě **Nastavení** vyberte nebo zadejte následující hodnoty a pak vyberte **OK**:
    1. Vyberte bránu primárního síťového rozhraní pro **první bránu virtuální sítě**, například `Primary-Gateway` .  
    1. Vyberte bránu sekundární sítě pro **druhou bránu virtuální sítě**, například `Secondary-Gateway` . 
    1. Zaškrtněte políčko vedle možnosti **navázat obousměrné připojení**. 
    1. Buď ponechte výchozí název primárního připojení, nebo ho přejmenujte na hodnotu podle vašeho výběru. 
    1. Zadejte **sdílený klíč (PSK)** pro připojení, například `mi1m2psk` . 

   ![Vytvořit připojení brány](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

1. Na kartě **Souhrn** zkontrolujte nastavení obousměrného připojení a pak vyberte **OK** . tím vytvoříte připojení. 


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


## <a name="7---create-a-failover-group"></a>7. Vytvoření skupiny převzetí služeb při selhání
V tomto kroku vytvoříte skupinu převzetí služeb při selhání a přidáte do ní obě spravované instance SQL. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Vytvořte skupinu převzetí služeb při selhání pomocí Azure Portal. 


1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Vyberte primární spravovanou instanci SQL, kterou jste vytvořili v první části, například `sql-mi-primary` . 
1. V části **Nastavení**přejděte na **instance skupiny převzetí služeb při selhání** a pak zvolte **Přidat skupinu** a otevřete stránku **skupiny převzetí služeb při selhání instance** . 

   ![Přidat skupinu převzetí služeb při selhání](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte název skupiny převzetí služeb při selhání, například `failovergrouptutorial` a pak zvolte sekundární SQL spravovanou instanci, například `sql-mi-secondary` z rozevíracího seznamu. Vyberte **vytvořit** a vytvořte skupinu převzetí služeb při selhání. 

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


## <a name="8---test-failover"></a>8. testování převzetí služeb při selhání
V tomto kroku dojde k selhání skupiny převzetí služeb při selhání pro sekundární server a následnému navrácení služeb po obnovení pomocí Azure Portal. 


# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Testovací převzetí služeb při selhání pomocí Azure Portal. 


1. V [Azure Portal](https://portal.azure.com) přejděte do _sekundární_ spravované instance SQL a v části nastavení vyberte **instance skupiny převzetí služeb při selhání** . 
1. Zkontrolujte, která spravovaná instance SQL je primární a která spravovaná instance SQL je sekundární. 
1. Vyberte **převzetí služeb při selhání** a pak pro upozornění na odpojené relace TDS vyberte **Ano** . 

   ![Převzít služby při selhání skupiny převzetí služeb při selhání](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Zkontrolujte, která spravovaná instance SQL je primární a která spravovaná instance SQL je sekundární. Pokud se převzetí služeb při selhání úspěšně zdařilo, měly by tyto dvě instance mít přepnuté role. 

   ![Spravované instance SQL přepnuly role po převzetí služeb při selhání.](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Přejít na novou _sekundární_ spravovanou instanci SQL a znovu vyberte **převzetí služeb při selhání** , aby se primární instance znovu nezdařila do primární role. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Testovací převzetí služeb při selhání pomocí PowerShellu 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary SQL Managed Instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Vraťte skupinu převzetí služeb při selhání zpátky na primární server:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary SQL Managed Instance back to primary role
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
Vyčistěte prostředky tak, že nejprve odstraníte spravovanou instanci SQL, potom virtuální cluster, potom všechny zbývající prostředky a nakonec skupinu prostředků. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com). 
1. Vyberte spravované instance SQL a pak vyberte **Odstranit**. `yes`Do textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. Dokončení tohoto procesu může nějakou dobu trvat na pozadí a až do dokončení, nebudete moci odstranit *virtuální cluster* ani žádné jiné závislé prostředky. Sledujte odstranění na kartě aktivita a potvrďte, že se vaše spravovaná instance SQL odstranila. 
1. Po odstranění spravované instance SQL odstraňte *virtuální cluster* tak, že ho vyberete ve vaší skupině prostředků, a pak zvolíte **Odstranit**. `yes`Do textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. 
1. Odstraňte všechny zbývající prostředky. `yes`Do textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. 
1. Odstraňte skupinu prostředků výběrem možnosti **Odstranit skupinu prostředků**, zadáním názvu skupiny prostředků `myResourceGroup` a pak výběrem možnosti **Odstranit**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skupinu prostředků budete muset odebrat dvakrát. Při prvním odebrání skupiny prostředků se odeberou spravované instance SQL a virtuální clustery, ale tato chybová zpráva se pak nezdaří `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.` . Spusťte příkaz Remove-AzResourceGroup a podruhé odstraňte všechny zbývající prostředky i skupinu prostředků.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci Azure SQL.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Vrátí informace o instanci spravované databáze SQL Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci protokolu IP pro bránu Virtual Network. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu Virtual Network. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Vytvoří připojení mezi dvěma branami virtuální sítě.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Vytvoří novou skupinu převzetí služeb při selhání spravované instance SQL Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Načte nebo vypíše skupiny převzetí služeb při selhání spravované instance SQL.| 
| [Switch – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Provede převzetí služeb při selhání pro skupinu převzetí služeb při selhání spravované instance SQL. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal) 

Pro Azure Portal nejsou k dispozici žádné skripty.

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali skupinu převzetí služeb při selhání mezi dvěma spravovanými instancemi SQL. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření primární spravované instance SQL
> - Vytvořte sekundární spravovanou instanci SQL jako součást [skupiny převzetí služeb při selhání](../database/auto-failover-group-overview.md). 
> - Testovací převzetí služeb při selhání

Přejděte k dalšímu rychlému startu, jak se připojit ke spravované instanci SQL, a jak obnovit databázi do spravované instance SQL: 

> [!div class="nextstepaction"]
> [Připojení k spravované instanci SQL](connect-vm-instance-configure.md) 
>  [Obnovení databáze do spravované instance SQL](restore-sample-database-quickstart.md)


