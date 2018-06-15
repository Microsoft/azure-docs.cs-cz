---
title: Nasazení virtuálního počítače s certifikátem bezpečně uložené v zásobníku Azure | Microsoft Docs
description: Postup nasazení virtuálního počítače a vložit do jeho certifikát pomocí trezoru klíčů v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 3950c9dfc5ff5f7ea1d170da086b4f97048ed81c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069029"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Vytvoření virtuálního počítače a nainstalovat certifikát načíst z trezoru klíčů služby Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Naučte se vytvořit virtuální počítač (VM) Azure zásobník sítě s nainstalovaný certifikát trezoru klíčů.

## <a name="overview"></a>Přehled

Certifikáty se používají v mnoha případech, například ověřování do služby Active Directory nebo šifrování webový provoz. Certifikáty můžete bezpečně uložit jako tajných klíčů v trezoru klíčů služby Azure zásobníku. Výhody použití Azure zásobníku Key Vault jsou:

* Certifikáty se nezobrazí v skriptu, historie příkazového řádku nebo šablony.
* Zjednoduší proces správy certifikátů.
* Můžete mít kontrolu nad klíčů, které přístup certifikáty.

### <a name="process-description"></a>Popis procesu

Následující kroky popisují proces nutný k nabízení certifikát do virtuálního počítače:

1. Vytvořte tajný klíč trezoru.
2. Aktualizujte soubor azuredeploy.parameters.json.
3. Nasazení šablony

>[!NOTE]
>Tyto kroky ze Development Kit zásobník Azure nebo z externích klientských můžete použít, pokud jsou připojené prostřednictvím sítě VPN.

## <a name="prerequisites"></a>Požadavky

* Musí se přihlásíte na nabídku obsahující službu Key Vault.
* [Instalace prostředí PowerShell pro Azure zásobníku.](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md)

## <a name="create-a-key-vault-secret"></a>Vytvoření tajný klíč trezoru

Tento skript vytvoří certifikát ve formátu .pfx, vytvoří trezoru klíčů a certifikát uloží v trezoru klíčů jako tajný klíč.

>[!IMPORTANT]
>Je nutné použít `-EnabledForDeployment` parametr při vytváření klíče selhání. Tento parametr zajistí, že trezor klíčů můžete na něj odkazovat z šablon Azure Resource Manageru.

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

Když spustíte předchozí skript, zahrnuje tajný identifikátor URI. Poznamenejte si tento identifikátor URI. Máte na něj v odkazovat [nabízený certifikát do šablony správce prostředků Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Stažení [šablona virtuálního počítače nabízené certifikát windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) složka na vývojovém počítači. Tato složka obsahuje `azuredeploy.json` a `azuredeploy.parameters.json` soubory, které budete potřebovat v dalších krocích.

Změnit `azuredeploy.parameters.json` soubor na základě hodnoty prostředí. Parametry zajímají jsou název trezoru, skupině prostředků úložiště a tajný klíč identifikátor URI (generovaná předchozí skript). Následující soubor je příklad souboru parametrů:

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualizovat soubor azuredeploy.parameters.json

Aktualizujte soubor azuredeploy.parameters.json vaultName, tajný URI, VmName a jiné hodnoty, podle vašeho prostředí. Následující soubor JSON ukazuje příklad souboru parametrů šablony:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Nasazení šablony

Nasazení šablony pomocí následujícího skriptu prostředí PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Při nasazení šablony úspěšně výsledkem následující výstup:

![Výsledky nasazení šablony](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Azure zásobníku doručí certifikátu na virtuální počítač během nasazení. Umístění certifikátu, závisí na operačním systému Virtuálního počítače:

* V systému Windows je certifikát přidat do umístění certifikátu LocalMachine s úložiště certifikátů, které uživatele.
* V systému Linux, certifikát je umístěn v adresáři /var/lib/waagent s názvem souboru &lt;UppercaseThumbprint&gt;.crt pro X509 soubor certifikátu a &lt;UppercaseThumbprint&gt;.prv pro privátní klíč .

## <a name="retire-certificates"></a>Vyřadit certifikáty

Vyřazení certifikáty je součástí procesu správy certifikátů. Starší verzi certifikát nelze odstranit, ale ji můžete vypnout pomocí `Set-AzureKeyVaultSecretAttribute` rutiny.

Následující příklad ukazuje, jak zakázat k certifikátu. Použít vlastní hodnoty pro **VaultName**, **název**, a **verze** parametry.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Další postup

* [Nasazení virtuálního počítače s heslem Key Vaultu](azure-stack-kv-deploy-vm-with-secret.md)
* [Povolit aplikaci přístup k Key Vault](azure-stack-kv-sample-app.md)
