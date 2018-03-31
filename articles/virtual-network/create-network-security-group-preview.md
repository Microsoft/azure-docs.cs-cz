---
title: "Filtrování provozu sítě s Azure skupin zabezpečení sítě a aplikací (Preview) | Microsoft Docs"
description: "Informace o vytváření skupin zabezpečení sítě a aplikací (Preview), který omezí typ síťový provoz do/z virtuálních počítačů."
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ac9a1a8c59a26393d32f9c543e630c302b7ced9d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2018
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>Filtrování provozu sítě přenosů se skupinami zabezpečení sítě a aplikací (Preview)

V tomto kurzu zjistěte, jak vytvořit skupiny zabezpečení sítě pro filtrování síťový provoz do a z skupiny virtuálních počítačů pomocí skupin zabezpečení aplikací. Další informace o skupinách zabezpečení sítě a skupiny zabezpečení aplikací najdete v tématu [Přehled zabezpečení](security-overview.md). 

Následující části obsahují postupy, které můžete vytvořit síť pomocí rozhraní příkazového řádku Azure skupin zabezpečení ([rozhraní příkazového řádku Azure](#azure-cli)) a [prostředí Azure PowerShell](#powershell). Výsledkem je stejný, bez ohledu na to, jaký nástroj použijete k vytvoření skupin zabezpečení sítě. Kliknutím na odkaz nástroj přejděte do části tohoto kurzu. 

Tento článek obsahuje kroky k vytvoření sítě skupin zabezpečení pomocí modelu nasazení Resource Manager, který je model nasazení, které vám doporučujeme používat při vytváření skupin zabezpečení sítě. Pokud potřebujete vytvořit skupinu zabezpečení sítě (klasické), přečtěte si téma [vytvořit skupinu zabezpečení sítě (klasické)](virtual-networks-create-nsg-classic-ps.md). Pokud si nejste obeznámeni s modelech nasazení Azure, najdete v části [modelech nasazení Azure pochopit](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!NOTE]
> Tento kurz využívá funkce skupiny zabezpečení sítě, které jsou aktuálně ve verzi preview. Funkce ve verzi preview ještě nemá stejné dostupnost a spolehlivost jako funkce v hlavní verze. Pokud chcete implementovat skupiny pomocí pouze funkce obecně verzi zabezpečení sítě najdete v tématu [vytvořit skupinu zabezpečení sítě](virtual-networks-create-nsg-arm-pportal.md). 

## <a name="azure-cli"></a>Azure CLI

Rozhraní příkazového řádku Azure jsou stejné, zda spuštěním příkazů z Windows, Linux nebo systému macOS. Existují však skriptování rozdíly mezi součásti pro operační systém. Skripty v následujících krocích spustit v prostředí Bash. 

1. [Instalace a konfigurace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Ujistěte se, že používáte verzi 2.0.18 nebo vyšší rozhraní příkazového řádku Azure tak, že zadáte `az --version` příkaz. Pokud si nejste, nainstalujte nejnovější verzi.
3. Přihlaste se k Azure pomocí `az login` příkaz.
4. Registrovat verzi Preview zadáním následujících příkazů:
    
    ```azurecli
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. Potvrďte, že jste zaregistrováni ve verzi preview tak, že zadáte následující příkaz:

    ```azurecli
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    > [!WARNING]
    > Registrace může trvat až jednu hodinu, k dokončení. Nepokračujte, dokud zbývající kroky *registrovaná* zobrazí **stavu** ve výstupu vrácená z předchozí příkaz. Pokud budete pokračovat, než budete zaregistrováni, nezdaří se zbývající kroky.

6. Spusťte následující skript bash pro vytvoření skupiny prostředků:

    ```azurecli
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. Vytvořte tři skupiny zabezpečení aplikací, jednu pro každý typ serveru:

    ```azurecli
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. Vytvořte skupinu zabezpečení sítě:

    ```azurecli
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. Vytvoření pravidla zabezpečení v rámci NSG, nastavení zabezpečení skupiny aplikací jako cíl:
    
    ```azurecli    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. Vytvořte virtuální síť: 
    
    ```azurecli
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. Přidružte skupinu zabezpečení sítě pro podsíť ve virtuální síti:

    ```azurecli
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. Vytvořte tři rozhraní sítě, jednu pro každý typ serveru: 

    ```azurecli
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    Rozhraní sítě, na základě síťové rozhraní je členem skupiny zabezpečení aplikace se použije pouze odpovídající pravidlo zabezpečení, kterou jste vytvořili v kroku 9. Například pouze *AppRule* pravidlo je platná pro *myNic2*, protože síťové rozhraní je členem skupiny *AppServers* skupiny zabezpečení aplikací a pravidla Určuje, *AppServers* skupiny zabezpečení aplikací jako svůj cíl. *WebRule* a *DatabaseRule* pravidla se nepoužije *myNic2*, protože síťové rozhraní není členem *webové servery*a *DatabaseServers* skupin zabezpečení aplikací. Obě *WebRule* a *AppRule* pravidla jsou platné pro *myNic1* ale protože *myNic1* síťové rozhraní je členem skupiny Jak *webové servery* a *AppServers* zadejte skupiny zabezpečení aplikací a pravidla *webové servery* a *AppServers* skupin zabezpečení aplikací jako jejich cíle. 

13. Vytvořte jeden virtuální počítač pro každý typ serveru, připojení příslušné síťové rozhraní pro každý virtuální počítač. Tento příklad vytvoří virtuální počítače s Windows, ale můžete změnit *win2016datacenter* k *UbuntuLTS* k vytvoření virtuálních počítačů Linux místo.

    ```azurecli
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **Volitelné**: Odstraňte prostředky, které v tomto kurzu vytvoříte pomocí kroků v [odstranit prostředky](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Instalace a konfigurace [prostředí PowerShell](/powershell/azure/install-azurerm-ps).
2. Ujistěte se, máte verzi 4.4.0 nebo vyšší AzureRm modul nainstalován. Aktuálně nainstalovanou verzi můžete zkontrolovat tak, že zadáte `Get-Module -ListAvailable AzureRM` příkaz. Pokud potřebujete nainstalovat nebo upgradovat, nainstalujte nejnovější verzi modulu AzureRM z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureRM).
3. V relaci prostředí PowerShell přihlášení k Azure s vaší [účet Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) pomocí `login-azurermaccount` příkaz.
4. Registrovat verzi Preview zadáním následujících příkazů:
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. Potvrďte, že jste zaregistrováni ve verzi preview tak, že zadáte následující příkaz:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    > [!WARNING]
    > Registrace může trvat až jednu hodinu, k dokončení. Nepokračujte, dokud zbývající kroky *registrovaná* zobrazí **RegistrationState** ve výstupu vrácená z předchozí příkaz. Pokud budete pokračovat, než budete zaregistrováni, nezdaří se zbývající kroky.
        
6. Vytvořte skupinu prostředků:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. Vytvořte tři skupiny zabezpečení aplikací, jednu pro každý typ serveru:

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. Vytvoření pravidla zabezpečení pro každý typ serveru.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80
    
    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443
      
    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336
    ``` 

9. Vytvořte skupinu zabezpečení sítě:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. Vytvořte konfiguraci podsítě a přidružte skupinu zabezpečení sítě k němu:
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. Vytvořte virtuální síť: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. Vytvořte tři rozhraní sítě, jednu pro každý typ serveru. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $databaseAsg
    ```

    Rozhraní sítě, na základě síťové rozhraní je členem skupiny zabezpečení aplikace se použije pouze odpovídající pravidlo zabezpečení, kterou jste vytvořili v kroku 8. Například pouze *AppRule* pravidlo je platná pro *myNic2*, protože síťové rozhraní je členem skupiny *AppServers* skupiny zabezpečení aplikací a pravidla Určuje, *AppServers* skupiny zabezpečení aplikací jako svůj cíl. *WebRule* a *DatabaseRule* pravidla se nepoužije *myNic2*, protože síťové rozhraní není členem *webové servery*a *DatabaseServers* skupin zabezpečení aplikací. Obě *WebRule* a *AppRule* pravidla jsou platné pro *myNic1* ale protože *myNic1* síťové rozhraní je členem skupiny Jak *webové servery* a *AppServers* zadejte skupiny zabezpečení aplikací a pravidla *webové servery* a *AppServers* skupin zabezpečení aplikací jako jejich cíle. 

13. Vytvořte jeden virtuální počítač pro každý typ serveru, připojení příslušné síťové rozhraní pro každý virtuální počítač. Tento příklad vytvoří virtuální počítače s Windows, ale před spuštěním skriptu, můžete změnit *-Windows* k *- Linux*, *MicrosoftWindowsServer* k *Kanonický*, *Windows Server* k *UbuntuServer* a *2016 Datacenter* k *14.04.2-LTS*k vytvoření virtuálních počítačů Linux místo.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **Volitelné**: Odstraňte prostředky, které v tomto kurzu vytvoříte pomocí kroků v [odstranit prostředky](#delete-cli).

## <a name="remove-a-nic-from-an-asg"></a>Odebrání ASG síťový adaptér
Jakmile síťové rozhraní odeberete ze skupiny zabezpečení pro aplikace, žádná pravidla, která určete skupinu zabezpečení, aplikace se použijí pro síťové rozhraní, které je odebrat.

### <a name="azure-cli"></a>Azure CLI

Chcete-li odebrat *myNic3* ze všech skupin zabezpečení aplikací, zadejte následující příkaz:

```azurecli
az network nic update \
  --name myNic3 \
  --resource-group myResourceGroup \
  --remove ipConfigurations[0].applicationSecurityGroups
```

### <a name="powershell"></a>PowerShell

Chcete-li odebrat *myNic3* ze všech skupin zabezpečení aplikací, zadejte následující příkazy:

```powershell
$nic=Get-AzureRmNetworkInterface `
  -Name myNic3 `
  -ResourceGroupName myResourceGroup

$nic.IpConfigurations[0].ApplicationSecurityGroups = $null
$nic | Set-AzureRmNetworkInterface 
```

## <a name="delete"></a>Odstraňte prostředky

Po dokončení tohoto kurzu můžete chtít odstranit prostředky, které jste vytvořili, tak, aby vám zbytečně nenabíhaly poplatky za používání. Odstranění skupiny prostředků se také odstraní všechny prostředky, které jsou ve skupině prostředků.

### <a name="delete-portal"></a>Portál Azure

1. V dialogovém okně hledání portálu zadejte **myResourceGroup**. Ve výsledcích hledání klikněte na tlačítko **myResourceGroup**.
2. Na **myResourceGroup** okně klikněte **odstranit** ikonu.
3. Potvrďte odstranění, v **název skupiny prostředků typu** zadejte **myResourceGroup**a potom klikněte na **odstranit**.

### <a name="delete-cli"></a>Rozhraní příkazového řádku Azure

V relaci příkazového řádku zadejte následující příkaz:

```azurecli
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>Prostředí PowerShell

V relaci prostředí PowerShell zadejte následující příkaz:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

