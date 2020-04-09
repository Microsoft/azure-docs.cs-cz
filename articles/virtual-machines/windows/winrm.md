---
title: Nastavení přístupu winrm pro virtuální počítač Azure
description: Instalační přístup k winrm pro použití s virtuálním počítačem Azure vytvořeným v modelu nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: mimckitt
ms.openlocfilehash: 317e9376e0b8242758cd6e3f455b3f3dc9c0dc78
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879560"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Nastavení přístupu winrm pro virtuální počítače ve Správci prostředků Azure

Tady jsou kroky, které je třeba podniknout, abyste nastavili virtuální ho s připojením WinRM.

1. Vytvoření trezoru klíčů
2. Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
3. Nahrání certifikátu podepsaného svým držitelem do trezoru klíčů
4. Získání adresy URL certifikátu podepsaného svým držitelem v trezoru klíčů
5. Odkaz na adresu URL certifikátů podepsaných svým držitelem při vytváření virtuálního počítače

 

## <a name="step-1-create-a-key-vault"></a>Krok 1: Vytvoření trezoru klíčů
Pomocí níže uvedeného příkazu můžete vytvořit trezor klíčů.

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Krok 2: Vytvoření certifikátu podepsaného svým držitelem
Pomocí tohoto skriptu prostředí PowerShell můžete vytvořit certifikát podepsaný svým držitelem.

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Krok 3: Nahrání certifikátu podepsaného svým držitelem do trezoru klíčů
Před nahráním certifikátu do trezoru klíčů vytvořeného v kroku 1 je třeba jej převést do formátu, kterému bude rozumět poskytovatel prostředků Microsoft.Compute. Níže uvedený skript Prostředí PowerShell vám to umožní

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
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Krok 4: Získání adresy URL certifikátu podepsaného svým držitelem v trezoru klíčů
Poskytovatel prostředků Microsoft.Compute potřebuje adresu URL tajného klíče uvnitř trezoru klíčů při zřizování virtuálního počítače. To umožňuje poskytovateli prostředků Microsoft.Compute stáhnout tajný klíč a vytvořit ekvivalentní certifikát na virtuálním počítači.

> [!NOTE]
> Adresa URL tajného klíče musí obsahovat také verzi. Příklad URL vypadá jako\/níže https: /contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Šablony
Odkaz na adresu URL v šabloně můžete získat pomocí níže uvedeného kódu

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Tuto adresu URL můžete získat pomocí níže uvedeného příkazu PowerShellu.

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Krok 5: Odkaz na adresu URL certifikátů podepsaných svým držitelem při vytváření virtuálního počítače
#### <a name="azure-resource-manager-templates"></a>Šablony Azure Správce prostředků
Při vytváření virtuálního počítače prostřednictvím šablon se certifikát odkazuje v části tajných kódů a v části winRM, jak je uvedeno níže:

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

Ukázka šablony pro výše uvedené lze nalézt zde na [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Zdrojový kód pro tuto šablonu lze nalézt na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Krok 6: Připojení k virtuálnímu virtuálnímu mísu
Než se budete moct připojit k virtuálnímu počítači, musíte se ujistit, že je váš počítač nakonfigurovaný pro vzdálenou správu WinRM. Spusťte PowerShell jako správce a spusťte níže uvedený příkaz, abyste se ujistili, že jste nastaveni.

    Enable-PSRemoting -Force

> [!NOTE]
> Pokud výše uvedené služby nefungují, může být nutné se ujistit, že je spuštěna služba WinRM. Můžete to udělat pomocí`Get-Service WinRM`
> 
> 

Po dokončení nastavení se můžete připojit k virtuálnímu počítači pomocí níže uvedeného příkazu

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
