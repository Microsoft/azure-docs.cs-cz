---
title: Nasazení virtuálního počítače s certifikátem bezpečně uloženým ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nasadit virtuální počítač a push certificate problém napravit pomocí služby key vault ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 3e2ef77e0932c81746e24efecad020bd567a1fd5
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250397"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Vytvoření virtuálního počítače a nainstalovat certifikát načíst z trezoru klíčů Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Zjistěte, jak vytvořit virtuální počítač (VM) Azure Stack pomocí služby key vault certifikátu nainstalovaného.

## <a name="overview"></a>Přehled

Certifikáty se používají v mnoha scénářích, jako je například ověřování do služby Active Directory nebo šifrování webový provoz. Certifikáty můžete bezpečně ukládat jako tajné klíče v trezoru klíčů služby Azure Stack. Výhody používání služby Azure Stack Key Vault jsou:

* Certifikáty se nezobrazí ve skriptu, historii příkazového řádku nebo šabloně.
* Zjednodušené proces správy certifikátů.
* Máte pod kontrolou klíče, které přistupují k certifikáty.

### <a name="process-description"></a>Popis procesu

Následující kroky popisují proces nutný k nahrání certifikátu do virtuálního počítače:

1. Vytvoření tajného kódu Key Vault.
2. Aktualizace souboru azuredeploy.parameters.json.
3. Nasazení šablony.

> [!NOTE]
> Tyto kroky ze sady Azure Stack Development Kit, nebo z externího klienta můžete použít, pokud jsou připojené prostřednictvím sítě VPN.

## <a name="prerequisites"></a>Požadavky

* Můžete musí přihlásit k odběru nabídky, která obsahuje službu Key Vault.
* [Instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).
* [Konfigurace uživatele služby Azure Stack Powershellu prostředí](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Vytvoření tajného kódu Key Vault

Tento skript vytvoří certifikát ve formátu .pfx, vytvoří trezor klíčů a ukládá certifikát jako tajný kód trezoru klíčů.

> [!IMPORTANT]
> Je nutné použít `-EnabledForDeployment` parametr při vytváření trezoru klíčů. Tento parametr se zajistí, že služby key vault může odkazovat ze šablon Azure Resource Manageru.

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

Při spuštění předchozí skript výstup obsahuje identifikátor URI tajného kódu. Poznamenejte si tento identifikátor URI. Je nutné na něj v odkazovat [certifikátu Push pro šablony Resource Manageru Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Stáhněte si [vm nabízeného certifikátu windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) složky šablony na vývojovém počítači. Tato složka obsahuje `azuredeploy.json` a `azuredeploy.parameters.json` soubory, které budete potřebovat v dalších krocích.

Upravit `azuredeploy.parameters.json` souboru souladu s hodnotami prostředí. Parametry zájmovou jsou název trezoru, skupina prostředků trezoru a tajný klíč identifikátoru URI (generovaná předchozí skript). Následující části je příkladem souboru parametrů.

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualizace souboru azuredeploy.parameters.json

Aktualizace `azuredeploy.parameters.json` souboru `vaultName`, tajný identifikátor URI `VmName`a jiné hodnoty podle vašeho prostředí. Následující soubor JSON ukazuje příklad soubor parametrů šablony:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Po úspěšném nasazení šablony, výsledkem následující výstup:

![Výsledky šablony nasazení](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

Azure Stack odešle certifikát do virtuálního počítače během nasazení. Umístění certifikátu závisí na operačním systému Virtuálního počítače:

* Ve Windows, je certifikát přidán do **LocalMachine** umístění s úložišti certifikátů, které uživatel zadal certifikátu.
* V systému Linux, certifikát je umístěn v rámci `/var/lib/waagent directory`, s názvem souboru &lt;UppercaseThumbprint&gt;CRT pro X509 soubor certifikátu a &lt;UppercaseThumbprint&gt;.prv privátního klíče.

## <a name="retire-certificates"></a>Vyřadit certifikáty

Po vyřazení certifikátů je součástí proces správy certifikátů. Nelze odstranit starší verzi certifikát, ale můžete ji vypnout pomocí `Set-AzureKeyVaultSecretAttribute` rutiny.

Následující příklad ukazuje, jak zakázat certifikát. Použijte vlastní hodnoty **VaultName**, **název**, a **verze** parametry.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Další postup

* [Nasazení virtuálního počítače s heslem Key Vaultu](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Povolit aplikaci přístup ke službě Key Vault](azure-stack-key-vault-sample-app.md)
