---
title: "Konfigurace koncových bodů služby virtuální sítě Azure | Dokumentace Microsoftu"
description: "Zjistěte, jak povolit a zakázat koncové body služby z virtuální sítě."
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2018
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: ee01b83101d4ab4ee8665c32aca22a793b41ac0d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="configure-virtual-network-service-endpoints"></a>Konfigurace koncových bodů služby virtuální sítě

Koncové body služby virtuální sítě umožňují svázat prostředky služeb Azure s virtuální sítí Azure a úplně tak odebrat internetový přístup k těmto prostředkům. Koncové body služby zajišťují přímé připojení z virtuální sítě ke službě Azure a umožňují pro přístup ke službám Azure používat privátní adresní prostor virtuální sítě. Provoz směřující do služeb Azure prostřednictvím koncových bodů služby zůstává vždy v páteřní síti Microsoft Azure. Další informace o [koncových bodech služby virtuální sítě](virtual-network-service-endpoints-overview.md)

Tento článek obsahuje postup pro povolení a zakázání koncových bodů služby. Po povolení koncových bodů pro službu Azure v podsíti můžete svázat konkrétní prostředky služby s virtuální sítí.

Koncové body služby je možné konfigurovat pomocí webu [Azure Portal](#azure-portal), [Azure PowerShellu](#azure-powershell), [rozhraní příkazového řádku Azure](#azure-cli) nebo [šablony](#resource-manager-template) Azure Resource Manageru.

>[!NOTE]
Během období Preview je funkce koncových bodů služby virtuální sítě podporována pro konkrétní oblasti. Seznam podporovaných oblastí najdete na stránce [Aktualizace služby Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="service-endpoint-configuration-overview"></a>Přehled konfigurace koncového bodu služby

- Koncové body služby je možné konfigurovat pouze ve virtuálních sítích nasazených prostřednictvím modelu nasazení Azure Resource Manager.

- Koncové body služby se nastavují v každé podsíti virtuální sítě.

- V jedné podsíti je možné pro každou službu nakonfigurovat pouze jeden koncový bod služby. Pro různé služby (například Azure Storage nebo Azure SQL) můžete nakonfigurovat několik koncových bodů služby.

- Koncové body můžete povolit v nové nebo existující podsíti.

- Umístění se pro koncový bod konfiguruje automaticky. Ve výchozím nastavení se koncové body služby konfigurují v oblasti virtuální sítě. V případě služby Azure Storage se koncové body pro zajištění podpory scénářů místního převzetí služeb při selhání automaticky konfigurují ve [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

  >[!NOTE]
  V závislosti na velikosti virtuální sítě a podsítě může dokončení povolení koncového bodu služby nějakou dobu trvat. Ujistěte se, že při povolování koncových bodů služby neprobíhají žádné důležité úlohy. Koncové body služby přepínají trasy na každém síťovém rozhraní v podsíti a můžou ukončit otevřená připojení TCP. 

- Volání koncového bodu služby vrátí úspěch po přepnutí toku provozu do služby na všech síťových rozhraních v podsíti na privátní IP adresy virtuální sítě.

- Ověření konfigurace koncového bodu pomocí efektivních tras:

   Správnou konfiguraci koncového bodu služby ověříte tak, že se u efektivních tras na všech síťových rozhraních v podsíti pro jednotlivé služby a oblasti zobrazí nová výchozí trasa s typem dalšího segmentu směrování VirtualNetworkServiceEndpoint. Další informace o [řešení potíží pomocí efektivních tras](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   Efektivní trasy můžete zobrazit, pouze pokud pro spuštěný virtuální počítač v dané podsíti máte nakonfigurované a přidružené jedno nebo více síťových rozhraní.

## <a name="azure-portal"></a>Azure Portal

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>Nastavení koncového bodu služby v podsíti při vytváření virtuální sítě

1. Otevřete [Azure Portal](https://portal.azure.com/).
Přihlaste se k Azure pomocí svého účtu Azure. Pokud účet Azure nemáte, můžete si zaregistrovat bezplatnou zkušební verzi. Účet musí mít potřebná [oprávnění](#provisioning) k vytvoření virtuální sítě a koncového bodu služby.
2. Klikněte na **Vytvořit prostředek** > **Sítě** > **Virtuální síť** > **+ Přidat**.
3. V části Vytvořit virtuální síť zadejte následující hodnoty a pak klikněte na **Vytvořit**:

Nastavení | Hodnota
------- | -----
Název    | myVnet
Adresní prostor | 10.0.0.0/16
Název podsítě|mySubnet
Rozsah adres podsítě|10.0.0.0/24
Skupina prostředků|Ponechte vybranou možnost Vytvořit novou a zadejte název.
Umístění|Libovolná podporovaná oblast, například Austrálie – východ.
Předplatné|Vyberte své předplatné.
__Koncové body služby__|Povoleno
__Služby__ | Vyberte jednu nebo všechny dostupné služby. Podporované služby: __Microsoft.Storage a Microsoft.Sql__.

Vyberte služby pro koncové body: ![Výběr služeb pro koncové body služby](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. Ověřte správnost všech nastavení a klikněte na Vytvořit.

![Nastavení koncového bodu služby](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. Pokud chcete dokončit svázání prostředků služeb Azure s virtuální sítí, klikněte na dokumentaci služby v části [Další kroky](#Next%20Steps).


### <a name="validating-service-endpoint-configuration"></a>Ověření konfigurace koncového bodu služby

Pomocí následujících kroků potvrďte konfiguraci koncových bodů služby:

- V prostředcích klikněte na Virtuální sítě. Vyhledejte virtuální síť.
- Klikněte na název virtuální sítě a přejděte do části Koncové body služby.
- U nakonfigurovaných koncových bodů se zobrazí Úspěch. Zobrazena jsou také automaticky nakonfigurovaná umístění.

![Potvrzení konfigurace koncového bodu služby](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>Ověření konfigurace koncového bodu pomocí efektivních tras

Pokud chcete zobrazit efektivní trasu na síťovém rozhraní v podsíti, klikněte na libovolné síťové rozhraní v příslušné podsíti. V části Podpora a řešení potíží klikněte na Efektivní trasy. Pokud je nakonfigurovaný koncový bod, zobrazí se nová výchozí trasa s předponami adresy služby jako cílem a typem dalšího segmentu směrování VirtualNetworkServiceEndpoint.

![Efektivní trasy pro koncové body služby](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>Nastavení koncových bodů služby pro existující podsítě ve virtuální síti

1. V prostředcích klikněte na Virtuální sítě a vyhledejte libovolnou existující virtuální síť.
2. Klikněte na název virtuální sítě a přejděte do části Koncové body služby.
3. Klikněte na Přidat. Vyberte Služba. Najednou můžete vytvořit koncový bod pouze pro jednu službu. 
4. Vyberte všechny podsítě, ve kterých chcete koncový bod použít. Klikněte na Přidat.

![Konfigurace koncového bodu služby podsítě](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>Odstranění koncových bodů služby
1. V prostředcích klikněte na Virtuální sítě. Filtrováním podle názvu virtuální sítě vyhledejte existující virtuální síť.
2. Klikněte na název virtuální sítě a přejděte do části Koncové body služby.
3. Klikněte na název služby a potom klikněte pravým tlačítkem na položku koncového bodu služby.
4. Vyberte Odstranit.

![Odstranění koncového bodu služby](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure Powershell

Požadavky pro nastavení:

- Nainstalujte nejnovější verzi modulu [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) pro PowerShell. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Pokud chcete spustit relaci PowerShellu, přejděte do nabídky **Start**, zadejte **powershell** a potom klikněte na **PowerShell**.
- V PowerShellu se přihlaste k Azure zadáním příkazu `login-azurermaccount`. Účet musí mít potřebná [oprávnění](#provisioning) k vytvoření virtuální sítě a koncového bodu služby.

### <a name="get-available-service-endpoints-for-azure-region"></a>Získání dostupných koncových bodů služby pro oblast Azure

Pomocí následujícího příkazu získejte seznam podporovaných služeb pro koncové body v dané oblasti Azure.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

Výstup: 
Název | ID | Typ
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Přidání koncového bodu služby Azure Storage do podsítě *mySubnet* při vytváření virtuální sítě *myVNet*

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
Pomocí seznamu čárkami oddělených názvů služeb můžete povolit více služeb.
Příklad: "Microsoft.Storage", "Microsoft.Sql"

Očekávaný výstup:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

Pokud chcete dokončit svázání prostředků služeb Azure s virtuální sítí, klikněte na dokumentaci služby v části [Další kroky](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Přidání několika koncových bodů služby do existující podsítě

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

Očekávaný výstup: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Zobrazení koncových bodů služby nakonfigurovaných v podsíti

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
Výstup:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Odstranění koncových bodů služby v podsíti
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Azure CLI

Požadavky pro nastavení:
- Přihlaste se k předplatnému Azure pomocí příkazu [az login](/cli/azure/reference-index#az_login) a postupujte podle pokynů na obrazovce. Další informace o přihlašování najdete v tématu [Začínáme s Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
 - Účet musí mít potřebná [oprávnění](#provisioning) k vytvoření virtuální sítě a koncového bodu služby.

 Úplný seznam příkazů pro virtuální sítě najdete na stránce s [příkazy Azure CLI pro virtuální sítě](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest).

### <a name="get-available-service-endpoints-for-azure-region"></a>Získání dostupných koncových bodů služby pro oblast Azure

Pomocí následujícího příkazu získejte seznam podporovaných služeb pro koncové body v dané oblasti Azure, například EastUS.
```azure-cli
az network vnet list-endpoint-services -l eastus
```
Výstup:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Přidání koncového bodu služby Azure Storage do podsítě *mySubnet* při vytváření virtuální sítě *myVNet*

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

Přidání více koncových bodů: --service-endpoints Microsoft.Storage Microsoft.Sql

Výstup:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

Pokud chcete dokončit svázání prostředků služeb Azure s virtuální sítí, klikněte na dokumentaci služby v části [Další kroky](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Přidání několika koncových bodů služby do existující podsítě

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

Očekávaný výstup:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Zobrazení koncových bodů služby nakonfigurovaných v podsíti

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Odstranění koncových bodů služby v podsíti
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

Výstup: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Šablona Resource Manageru

### <a name="securing-azure-service-resources-to-vnets"></a>Svázání prostředků služeb Azure s virtuálními sítěmi

Prostřednictvím koncových bodů služby můžete svázat konkrétní prostředky Azure s virtuální sítí.

Stáhněte si ukázkovou [šablonu Resource Manageru](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) pro svázání účtu úložiště s podsítí ve virtuální síti.

Šablona vytvoří virtuální síť se 2 podsítěmi a v obou podsítích virtuální počítač se síťovým rozhraním. V jedné podsíti povolí koncový bod a sváže s touto podsítí účet úložiště.

Šablonu si můžete stáhnout a částečně ji upravit tak, aby vyhovovala vašemu scénáři.

Součástí šablony jsou pokyny pro její nasazení pomocí webu Azure Portal, PowerShellu nebo Azure CLI. Ujistěte se, že máte potřebná [oprávnění](#provisioning) k nastavení koncového bodu a zabezpečení účtu.

Pokud chcete svázat prostředky Azure s podsítí:

- V příslušné podsíti musí být nakonfigurovaný koncový bod služby.
- Prostředek musí být svázán s virtuální sítí přidáním pravidla virtuální sítě v prostředku.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>Odstranění koncových bodů služby s prostředky svázanými s podsítí
Pokud jsou s podsítí svázané prostředky služeb Azure a koncový bod služby se odstraní, z dané podsítě už nebudete mít k prostředkům přístup.
Samotné opětovné povolení koncového bodu neobnoví přístup k prostředkům dříve svázaným s podsítí.

K opětovnému svázání prostředku služby s touto podsítí je potřeba:

- Znovu povolit koncový bod.
- Odebrat v prostředku staré pravidlo virtuální sítě.
- Přidat nové pravidlo, které sváže prostředek s podsítí.

## <a name="provisioning"></a>Zřizování

Koncové body služby může ve virtuálních sítích nezávisle na sobě konfigurovat uživatel s oprávněním k zápisu do virtuální sítě.

Pokud chce uživatel svázat prostředky služeb Azure s virtuální sítí, musí mít pro přidávané podsítě oprávnění k Microsoft.Network/JoinServicetoaSubnet. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD).

## <a name="next-steps"></a>Další kroky

Další pokyny ke svázání prostředku služby s virtuálními sítěmi najdete na následujících odkazech:

[Svázání účtů služby Azure Storage s virtuálními sítěmi](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Svázání služby Azure SQL s virtuálními sítěmi](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
