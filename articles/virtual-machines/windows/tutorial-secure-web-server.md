---
title: 'Kurz: zabezpečení webového serveru Windows pomocí certifikátů TLS/SSL v Azure'
description: V tomto kurzu se naučíte používat Azure PowerShell k zabezpečení virtuálního počítače s Windows, na kterém běží webový server IIS s certifikáty TLS/SSL uloženými v Azure Key Vault.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e5bbe179c43508033e0747bc2c03089f283b6e2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873075"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Kurz: zabezpečení webového serveru na virtuálním počítači s Windows v Azure s certifikáty TLS/SSL uloženými v Key Vault

> [!NOTE]
> V současné době tento dokument funguje pouze pro generalizované obrázky. Při pokusu o použití specializovaného disku v tomto kurzu dojde k chybě. 

Aby bylo možné zabezpečit webové servery, lze pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL), použít certifikát k šifrování webového provozu. Tyto certifikáty TLS/SSL můžou být uložené v Azure Key Vault a umožňují zabezpečená nasazení certifikátů do virtuálních počítačů s Windows v Azure. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření služby Azure Key Vault
> * Generování nebo nahrání certifikátu do služby Key Vault
> * Vytvoření virtuálního počítače a instalace webového serveru služby IIS
> * Vložení certifikátu do virtuálního počítače a konfigurace služby IIS s použitím vazby TLS


## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="overview"></a>Přehled
Azure Key Vault chrání kryptografické klíče a tajné kódy, jako jsou certifikáty a hesla. Key Vault pomáhá zjednodušit proces správy certifikátů a zajišťuje kontrolu nad klíči, které se používají k přístupu k těmto certifikátům. V rámci služby Key Vault můžete vytvořit certifikát podepsaný svým držitelem nebo nahrát stávající důvěryhodný certifikát, který již vlastníte.

Místo použití vlastní image virtuálního počítače, která zahrnuje integrované certifikáty, vložíte certifikáty do spuštěného virtuálního počítače. Tento proces zajistí, že se při nasazování na webový server nainstalují nejnovější certifikáty. Zároveň pokud obnovíte nebo nahradíte certifikát, nebudete muset vytvářet novou vlastní image virtuálního počítače. Při vytváření dalších virtuálních počítačů se automaticky vloží nejnovější certifikáty. Během celého procesu certifikáty neopustí platformu Azure ani nejsou zveřejněné v žádném skriptu, historii příkazového řádku nebo šabloně.


## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault
Než budete moct vytvořit Key Vault a certifikáty, vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupSecureWeb* v umístění *East US*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Dále vytvořte Key Vault pomocí [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Každá služba Key Vault vyžaduje jedinečný název, který by měl být malými písmeny. Nahraďte `mykeyvault` v následujícím příkladu vlastním jedinečným názvem služby Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Vygenerování certifikátu a jeho uložení do služby Key Vault
V případě použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem pomocí [Import-AzKeyVaultCertificate](/powershell/module/az.keyvault/import-azkeyvaultcertificate). V tomto kurzu následující příklad ukazuje, jak můžete vygenerovat certifikát podepsaný svým držitelem pomocí [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/add-azkeyvaultcertificate) , který používá výchozí zásadu certifikátu z [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní můžete vytvořit virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS*. Pokud zatím neexistují, vytvoří se podpůrné síťové prostředky. Za účelem povolení zabezpečeného webového provozu rutina také otevře port *443*.

```azurepowershell-interactive
# Create a VM
New-AzVm `
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
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Vytvoření virtuálního počítače trvá několik minut. Poslední krok používá rozšíření vlastních skriptů Azure k instalaci webového serveru IIS pomocí [set-AzVmExtension](/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Přidání certifikátu ze služby Key Vault do virtuálního počítače
Pokud chcete přidat certifikát z Key Vault do virtuálního počítače, Získejte ID certifikátu pomocí [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret). Přidejte certifikát k virtuálnímu počítači pomocí [Add-AzVMSecret](/powershell/module/az.compute/add-azvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurace služby IIS na použití certifikátu
Znovu použijte rozšíření vlastních skriptů s příkazem [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) a aktualizujte konfiguraci služby IIS. Tato aktualizace použije certifikát vložený ze služby Key Vault do IIS a nakonfiguruje webovou vazbu:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testování zabezpečené webové aplikace
Získejte veřejnou IP adresu vašeho virtuálního počítače pomocí [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro `myPublicIP`:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Nyní můžete otevřít webový prohlížeč a do adresního řádku zadat `https://<myPublicIP>`. Pokud chcete upozornění zabezpečení přijmout, pokud jste použili certifikát podepsaný svým držitelem, vyberte **Podrobnosti** a pak **se na webovou stránku dostanete** takto:

![Přijetí upozornění zabezpečení ve webovém prohlížeči](./media/tutorial-secure-web-server/browser-warning.png)

Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

![Zobrazení spuštěného zabezpečeného webu služby IIS](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zabezpečeni webový server služby IIS pomocí certifikátu TLS/SSL, který je uložený v Azure Key Vault. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření služby Azure Key Vault
> * Generování nebo nahrání certifikátu do služby Key Vault
> * Vytvoření virtuálního počítače a instalace webového serveru služby IIS
> * Vložení certifikátu do virtuálního počítače a konfigurace služby IIS s použitím vazby TLS

Na tomto odkazu najdete předem připravené ukázky skriptů pro virtuální počítače.

> [!div class="nextstepaction"]
> [Ukázky skriptů pro virtuální počítače s Windows](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)