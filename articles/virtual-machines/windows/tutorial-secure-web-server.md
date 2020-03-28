---
title: 'Kurz: Zabezpečení webového serveru Windows s certifikáty TLS/SSL v Azure'
description: V tomto kurzu se dozvíte, jak pomocí Azure PowerShellu zabezpečit virtuální počítač s Windows, na kterém běží webový server Služby IIS s certifikáty TLS/SSL uloženými v trezoru klíčů Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5b084f8a226d1cfd5bab2cc81512fb51fa6bf41c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80154283"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Kurz: Zabezpečení webového serveru na virtuálním počítači s Windows v Azure pomocí certifikátů TLS/SSL uložených v trezoru klíčů

> [!NOTE]
> V současné době tento dokument pracuje pouze pro generalizované obrázky. Pokud se pokusíte o tento kurz pomocí specializovaného disku, zobrazí se chyba. 

K zabezpečení webových serverů lze k šifrování webového provozu použít zabezpečení transportní vrstvy (TLS), dříve označované jako SSL (Secure Sockets Layer), certifikát. Tyto certifikáty TLS/SSL lze uložit do trezoru klíčů Azure a umožňují zabezpečené nasazení certifikátů do virtuálních počítačů (VM) Windows v Azure. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření služby Azure Key Vault
> * Generování nebo nahrání certifikátu do služby Key Vault
> * Vytvoření virtuálního počítače a instalace webového serveru služby IIS
> * Vložte certifikát do virtuálního počítače a nakonfigurujte službu IIS pomocí vazby TLS


## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="overview"></a>Přehled
Azure Key Vault chrání kryptografické klíče a tajné kódy, jako jsou certifikáty a hesla. Key Vault pomáhá zjednodušit proces správy certifikátů a zajišťuje kontrolu nad klíči, které se používají k přístupu k těmto certifikátům. V rámci služby Key Vault můžete vytvořit certifikát podepsaný svým držitelem nebo nahrát stávající důvěryhodný certifikát, který již vlastníte.

Místo použití vlastní image virtuálního počítače, která zahrnuje integrované certifikáty, vložíte certifikáty do spuštěného virtuálního počítače. Tento proces zajistí, že se při nasazování na webový server nainstalují nejnovější certifikáty. Zároveň pokud obnovíte nebo nahradíte certifikát, nebudete muset vytvářet novou vlastní image virtuálního počítače. Při vytváření dalších virtuálních počítačů se automaticky vloží nejnovější certifikáty. Během celého procesu certifikáty neopustí platformu Azure ani nejsou zveřejněné v žádném skriptu, historii příkazového řádku nebo šabloně.


## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault
Před vytvořením trezoru klíčů a certifikátů vytvořte skupinu prostředků s [aplikací New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupSecureWeb* v umístění *East US*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Dále vytvořte trezor klíčů s [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault). Každá služba Key Vault vyžaduje jedinečný název, který by měl být malými písmeny. Nahraďte `mykeyvault` v následujícím příkladu vlastním jedinečným názvem služby Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Vygenerování certifikátu a jeho uložení do služby Key Vault
Pro použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem pomocí [importu AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/import-azkeyvaultcertificate). V tomto kurzu následující příklad ukazuje, jak můžete generovat certifikát podepsaný svým držitelem s [add-AzKeyVaultCertificate,](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultcertificate) který používá výchozí zásady certifikátu z [New-AzKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy). 

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
Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď můžete vytvořit virtuální hospodařící s [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS*. Pokud zatím neexistují, vytvoří se podpůrné síťové prostředky. Za účelem povolení zabezpečeného webového provozu rutina také otevře port *443*.

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

Vytvoření virtuálního počítače trvá několik minut. Poslední krok používá rozšíření Azure Custom Script Extension k instalaci webového serveru Služby IIS pomocí [rozšíření Set-AzVmExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Přidání certifikátu ze služby Key Vault do virtuálního počítače
Chcete-li přidat certifikát z trezoru klíčů do virtuálního počítače, získejte id certifikátu pomocí [služby Get-AzKeyVaultSecret](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvaultsecret). Přidejte certifikát do virtuálního virtuálního soudu s [add-AzVMSecret](https://docs.microsoft.com/powershell/module/az.compute/add-azvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurace služby IIS na použití certifikátu
K aktualizaci konfigurace služby IIS použijte znovu rozšíření Vlastní skript s [rozšířením Set-AzVMExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) Tato aktualizace použije certifikát vložený ze služby Key Vault do IIS a nakonfiguruje webovou vazbu:

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
Získejte veřejnou IP adresu virtuálního počítače pomocí [get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro `myPublicIP`:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Nyní můžete otevřít webový prohlížeč a do adresního řádku zadat `https://<myPublicIP>`. Pokud chcete přijmout upozornění zabezpečení, pokud jste použili certifikát podepsaný svým držitelem, vyberte **Podrobnosti** a **přejděte na webovou stránku**:

![Přijetí upozornění zabezpečení ve webovém prohlížeči](./media/tutorial-secure-web-server/browser-warning.png)

Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

![Zobrazení spuštěného zabezpečeného webu služby IIS](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zabezpečili webový server služby IIS s certifikátem TLS/SSL uloženým v úložišti klíčů Azure. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření služby Azure Key Vault
> * Generování nebo nahrání certifikátu do služby Key Vault
> * Vytvoření virtuálního počítače a instalace webového serveru služby IIS
> * Vložte certifikát do virtuálního počítače a nakonfigurujte službu IIS pomocí vazby TLS

Na tomto odkazu najdete předem připravené ukázky skriptů pro virtuální počítače.

> [!div class="nextstepaction"]
> [Ukázky skriptů pro virtuální počítače s Windows](./powershell-samples.md)
