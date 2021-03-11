---
title: Nastavení přístupu WinRM pro virtuální počítač Azure
description: Nastavte přístup WinRM pro použití s virtuálním počítačem Azure vytvořeným v modelu nasazení Správce prostředků.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2016
ms.author: mimckitt
ms.openlocfilehash: ab676e7595a8ccd902eea27612e4c2fd035fae0c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555716"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Nastavení přístupu WinRM pro Virtual Machines v Azure Resource Manager

Tady jsou kroky, které musíte provést při nastavování virtuálního počítače pomocí připojení WinRM.

1. Vytvoření trezoru klíčů
2. Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)
3. Nahrání certifikátu podepsaného svým držitelem do Key Vault
4. Získat adresu URL certifikátu podepsaného svým držitelem v Key Vault
5. Odkazování na adresu URL certifikátů podepsaných svým držitelem při vytváření virtuálního počítače

 

## <a name="step-1-create-a-key-vault"></a>Krok 1: vytvoření Key Vault
K vytvoření Key Vault můžete použít následující příkaz.

```azurepowershell
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Krok 2: vytvoření certifikátu podepsaného svým držitelem
Pomocí tohoto skriptu PowerShellu můžete vytvořit certifikát podepsaný svým držitelem.

```azurepowershell
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Krok 3: nahrání certifikátu podepsaného svým držitelem do Key Vault
Před nahráním certifikátu do Key Vault vytvořeného v kroku 1 se musí převést na formát, který bude rozumět poskytovateli prostředků Microsoft. Compute. Níže uvedený skript prostředí PowerShell vám umožní

```azurepowershell
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

[System.Collections.HashTable]$TableForJSON = @{
    "data"     = $filecontentencoded;
    "dataType" = "pfx";
    "password" = "<password>";
}
[System.String]$JSONObject = $TableForJSON | ConvertTo-Json

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Krok 4: získání adresy URL certifikátu podepsaného svým držitelem v Key Vault
Poskytovatel prostředků Microsoft. COMPUTE potřebuje při zřizování virtuálního počítače adresu URL tajného kódu uvnitř Key Vault. To umožňuje poskytovateli prostředků Microsoft. COMPUTE stáhnout tajný kód a vytvořit ekvivalentní certifikát na virtuálním počítači.

> [!NOTE]
> Adresa URL tajného klíče musí obsahovat také verzi. Příklad adresy URL vypadá jako https: \/ /contosovault.Vault.Azure.NET:443/Secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Šablony
Odkaz na adresu URL v šabloně můžete získat pomocí následujícího kódu.

```json
"certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"
```

#### <a name="powershell"></a>PowerShell
Tuto adresu URL můžete získat pomocí následujícího příkazu PowerShellu.

```azurepowershell
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
```

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Krok 5: odkazování na adresu URL certifikátů podepsaných svým držitelem při vytváření virtuálního počítače
#### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Při vytváření virtuálního počítače prostřednictvím šablon se na certifikát odkazuje v části tajné klíče a v části winRM následujícím způsobem:

```json
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
```

Ukázkovou šablonu pro výše uvedenou možnost najdete tady: [201-VM-WinRM-klíčů Trezor – Windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Zdrojový kód pro tuto šablonu najdete na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows) .

#### <a name="powershell"></a>PowerShell
```azurepowershell
$vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
$credential = Get-Credential
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
$sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
$CertificateStore = "My"
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL
```

## <a name="step-6-connecting-to-the-vm"></a>Krok 6: připojení k virtuálnímu počítači
Než se budete moct připojit k virtuálnímu počítači, musíte se ujistit, že je váš počítač nakonfigurovaný pro vzdálenou správu WinRM. Spusťte PowerShell jako správce a spusťte následující příkaz, abyste se ujistili, že jste nastavili.

```azurepowershell
Enable-PSRemoting -Force
```

> [!NOTE]
> Pokud výše uvedená nefunguje, možná budete muset zkontrolovat, jestli je spuštěná služba WinRM. Můžete to udělat pomocí `Get-Service WinRM`
> 
> 

Po dokončení instalace se můžete připojit k virtuálnímu počítači pomocí příkazu níže.

```azurepowershell
Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
```
