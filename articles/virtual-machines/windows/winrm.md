---
title: Nastavit přístup WinRM pro virtuální počítač Azure | Microsoft Docs
description: Instalační program přístup WinRM pro použití s virtuálního počítače Azure vytvořené v modelu nasazení Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 5fa82dd4a85ff2e62848df0fdc6006922005a84b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Nastavení přístupu WinRM pro virtuální počítače ve službě Správce prostředků Azure
## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM v sadě vs Azure Service Management Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

* Přehled Azure Resource Manager, najdete v tématu to [článku](../../azure-resource-manager/resource-group-overview.md)
* Rozdíly mezi Azure Service Management a Azure Resource Manager, najdete v tématu to [článku](../../resource-manager-deployment-model.md)

V nastavení konfigurace služby WinRM mezi dvěma zásobníky klíčovým rozdílem je, jak získá certifikát nainstalovaný na virtuálním počítači. V zásobníku Azure Resource Manager jsou certifikáty modelován jako prostředky, které spravuje zprostředkovatele prostředku trezoru klíčů. Proto uživatel musí zadat své vlastní certifikát a odešlete ji do trezoru klíč před jeho použitím ve virtuálním počítači.

Tady jsou kroky, které je třeba provést nastavit virtuální počítač s připojením služby WinRM

1. Vytvoření trezoru klíčů
2. Vytvořit certifikát podepsaný svým držitelem
3. Nahrání certifikátu podepsaného svým držitelem pro Key Vault
4. Získat adresu URL pro certifikátu podepsaného svým držitelem v Key Vault
5. Odkazovat na adresu URL vašeho certifikáty podepsané svým držitelem při vytváření virtuálního počítače

## <a name="step-1-create-a-key-vault"></a>Krok 1: Vytvoření trezoru klíčů
Můžete použít následující příkaz pro vytvoření Key Vault

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Krok 2: Vytvořte certifikát podepsaný svým držitelem
Můžete vytvořit certifikát podepsaný svým držitelem pomocí tohoto skriptu prostředí PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Krok 3: Odeslání certifikátu podepsaného svým držitelem do služby Key Vault
Než odešlete certifikát do služby Key Vault vytvořili v kroku 1, je nutné převést do formátu, který pochopili zprostředkovatele prostředků Microsoft.Compute. Níže prostředí PowerShell vám umožní skriptu, můžete udělat

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Krok 4: Získáte adresu URL pro certifikátu podepsaného svým držitelem v Key Vault
Zprostředkovateli prostředků Microsoft.Compute. adresu URL tajný klíč v Key Vault musí při zřizování virtuálního počítače. To umožňuje zprostředkovatele prostředků Microsoft.Compute ke stažení tajný klíč a vytvořit ekvivalentní certifikát ve virtuálním počítači.

> [!NOTE]
> Adresa URL tajný klíč musí obsahovat verzi také. Adresu URL příklad vypadá níže https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve
> 
> 

#### <a name="templates"></a>Šablony
Odkaz na adresu URL pomocí šablony můžete získat níže uvedeného kódu

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Můžete získat pomocí této adresy URL následující příkaz prostředí PowerShell

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Krok 5: Odkazovat na adresu URL vašeho certifikáty podepsané svým držitelem při vytváření virtuálního počítače
#### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Při vytváření virtuálního počítače prostřednictvím šablony, získá certifikát odkazuje v části tajných klíčů a v části winRM, jak je uvedeno níže:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Vzorové šablony pro výše je zde uveden v [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Zdrojový kód pro tuto šablonu lze najít na [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Krok 6: Připojení k virtuálnímu počítači
Před připojením k virtuálnímu počítači, budete muset zajistěte, aby váš počítač je nakonfigurován pro vzdálenou správu přes WinRM. Spusťte prostředí PowerShell jako správce a spusťte následující příkaz, abyste měli jistotu, že nastavíte.

    Enable-PSRemoting -Force

> [!NOTE]
> Možná budete muset Ujistěte se, že Služba WinRM je spuštěná, pokud výše nefunguje. Můžete to, že pomocí `Get-Service WinRM`
> 
> 

Po dokončení instalace se můžete připojit k virtuální počítač pomocí pod příkaz

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
