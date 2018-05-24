---
title: Kurz – zabezpečení webového serveru Windows pomocí certifikátů SSL v Azure | Microsoft Docs
description: V tomto kurzu se naučíte používat Azure PowerShell k zabezpečení virtuálního počítače s Windows, na kterém běží webový server IIS, pomocí certifikátů SSL uložených ve službě Azure Key Vault.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 76d1170f4696c4221233d2b3c1d358375adfe5c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>Kurz: Zabezpečení webového serveru na virtuálním počítači s Windows v Azure pomocí certifikátů SSL uložených ve službě Key Vault

K zabezpečení webových serverů můžete použít certifikáty SSL (Secure Sockets Layer), které šifrují webový provoz. Tyto certifikáty SSL můžete ukládat do služby Azure Key Vault a umožnit zabezpečená nasazování certifikátů do virtuálních počítačů s Windows v Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření služby Azure Key Vault
> * Generování nebo nahrání certifikátu do služby Key Vault
> * Vytvoření virtuálního počítače a instalace webového serveru služby IIS
> * Vložení certifikátu do virtuálního počítače a konfigurace vazby SSL na serveru služby IIS

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.


## <a name="overview"></a>Přehled
Azure Key Vault chrání kryptografické klíče a tajné kódy, jako jsou certifikáty a hesla. Key Vault pomáhá zjednodušit proces správy certifikátů a zajišťuje kontrolu nad klíči, které se používají k přístupu k těmto certifikátům. V rámci služby Key Vault můžete vytvořit certifikát podepsaný svým držitelem nebo nahrát stávající důvěryhodný certifikát, který již vlastníte.

Místo použití vlastní image virtuálního počítače, která zahrnuje integrované certifikáty, vložíte certifikáty do spuštěného virtuálního počítače. Tento proces zajistí, že se při nasazování na webový server nainstalují nejnovější certifikáty. Zároveň pokud obnovíte nebo nahradíte certifikát, nebudete muset vytvářet novou vlastní image virtuálního počítače. Při vytváření dalších virtuálních počítačů se automaticky vloží nejnovější certifikáty. Během celého procesu certifikáty neopustí platformu Azure ani nejsou zveřejněné v žádném skriptu, historii příkazového řádku nebo šabloně.


## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault
Než vytvoříte službu Key Vault a certifikáty, vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupSecureWeb* v umístění *East US*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Potom vytvořte službu Key Vault pomocí rutiny [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Každá služba Key Vault vyžaduje jedinečný název, který by měl být malými písmeny. Nahraďte `mykeyvault` v následujícím příkladu vlastním jedinečným názvem služby Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Vygenerování certifikátu a jeho uložení do služby Key Vault
V případě použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem pomocí rutiny [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). Pro účely tohoto kurzu následující příklad ukazuje, jak můžete pomocí rutiny [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) vygenerovat certifikát podepsaný svým držitelem, který využívá výchozí zásady certifikátu z rutiny [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní můžete vytvořit virtuální počítač pomocí rutiny [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS*. Pokud zatím neexistují, vytvoří se podpůrné síťové prostředky. Za účelem povolení zabezpečeného webového provozu rutina také otevře port *443*.

```azurepowershell-interactive
# Create a VM
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Vytvoření virtuálního počítače trvá několik minut. Poslední krok používá rozšíření vlastních skriptů Azure k instalaci webového serveru služby IIS pomocí rutiny [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Přidání certifikátu ze služby Key Vault do virtuálního počítače
Pokud chcete přidat certifikát ze služby Key Vault do virtuálního počítače, získejte ID certifikátu pomocí rutiny [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). Certifikát do virtuálního počítače přidejte pomocí rutiny [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRmVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurace služby IIS na použití certifikátu
Znovu použijte rozšíření vlastních skriptů s rutinou [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) a aktualizujte konfiguraci služby IIS. Tato aktualizace použije certifikát vložený ze služby Key Vault do IIS a nakonfiguruje webovou vazbu:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testování zabezpečené webové aplikace
Získejte veřejnou IP adresu virtuálního počítače pomocí rutiny [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro `myPublicIP`:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Nyní můžete otevřít webový prohlížeč a do adresního řádku zadat `https://<myPublicIP>`. Pokud chcete přijímat upozornění zabezpečení v případě použití certifikátu podepsaného svým držitelem, vyberte **Podrobnosti** a potom **Pokračovat na web**:

![Přijetí upozornění zabezpečení ve webovém prohlížeči](./media/tutorial-secure-web-server/browser-warning.png)

Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

![Zobrazení spuštěného zabezpečeného webu služby IIS](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zabezpečili webový server služby IIS pomocí certifikátu SSL uloženého ve službě Azure Key Vault. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření služby Azure Key Vault
> * Generování nebo nahrání certifikátu do služby Key Vault
> * Vytvoření virtuálního počítače a instalace webového serveru služby IIS
> * Vložení certifikátu do virtuálního počítače a konfigurace vazby SSL na serveru služby IIS

Na tomto odkazu najdete předem připravené ukázky skriptů pro virtuální počítače.

> [!div class="nextstepaction"]
> [Ukázky skriptů pro virtuální počítače s Windows](./powershell-samples.md)
