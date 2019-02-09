---
title: Nastavení přístupu WinRM pro virtuální počítač Azure | Dokumentace Microsoftu
description: Nastavení přístupu WinRM pro použití s virtuálního počítače Azure vytvořené v modelu nasazení Resource Manageru.
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
ms.openlocfilehash: 22a522fcde2b79d89e6084cdcfcbf64e4e5bd5ce
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977962"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Nastavení přístupu WinRM pro virtuální počítače v Azure Resource Manageru

Tady jsou kroky, které musíte provést nastavení virtuálního počítače s připojením služby WinRM

1. Vytvoření trezoru klíčů
2. Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
3. Nahrání certifikátu podepsaného svým držitelem do služby Key Vault
4. Získat adresu URL pro váš certifikát podepsaný svým držitelem ve službě Key Vault
5. Odkazovat na adresu URL svého certifikáty podepsané svým držitelem při vytváření virtuálního počítače

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="step-1-create-a-key-vault"></a>Krok 1: Vytvoření trezoru klíčů
Můžete použít následující příkaz k vytvoření služby Key Vault

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Krok 2: Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
Můžete vytvořit certifikát podepsaný svým držitelem pomocí tohoto skriptu prostředí PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Krok 3: Nahrání certifikátu podepsaného svým držitelem pro Key Vault
Než nahrajete certifikát do služby Key Vault vytvořili v kroku 1, je potřeba převést do formátu, který rozumět zprostředkovatele prostředků Microsoft.Compute. Níže prostředí PowerShell vám umožní skript, můžete to udělat

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

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Krok 4: Získat adresu URL pro váš certifikát podepsaný svým držitelem ve službě Key Vault
Zprostředkovateli prostředků Microsoft.Compute. adresa URL tajného klíče do služby Key Vault musí při zřizování virtuálního počítače. To umožňuje poskytovateli prostředků Microsoft.Compute ke stažení tajný kód a vytvořit ekvivalentní certifikát na virtuálním počítači.

> [!NOTE]
> Adresa URL tajného klíče musí obsahovat verzi také. Příklad adresy URL vypadá jako níže https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve
> 
> 

#### <a name="templates"></a>Šablony
Můžete získat odkaz na adresu URL pomocí šablony níže uvedeného kódu

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Můžete získat pomocí této adresy URL následující příkaz prostředí PowerShell

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Krok 5: Odkazovat na adresu URL svého certifikáty podepsané svým držitelem při vytváření virtuálního počítače
#### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Při vytváření virtuálního počítače prostřednictvím šablony, získá certifikát odkazuje tajných kódů a v části winRM, jak je uvedeno níže:

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

Ukázková šablona pro výše uvedené najdete tady na [201-vm-winrm – trezor klíčů – windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Zdrojový kód pro tuto šablonu můžete najít na [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Krok 6: Připojení k virtuálnímu počítači
Před připojením k virtuálnímu počítači bude potřeba Ujistěte se, že váš počítač je nakonfigurovaný pro vzdálenou správu přes WinRM. Spusťte PowerShell jako správce a spusťte následující příkaz k Ujistěte se, že jste nastavili.

    Enable-PSRemoting -Force

> [!NOTE]
> Můžete potřebovat, abyste měli jistotu, že Služba WinRM je spuštěná, pokud se výše uvedené nefunguje. Můžete provést, že při použití `Get-Service WinRM`
> 
> 

Po dokončení instalace můžete připojit k virtuálnímu počítači pomocí následující příkaz

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
