---
title: Rozšíření virtuálního počítače s Azure Key Vault pro Linux
description: Nasaďte agenta, který provádí automatickou aktualizaci Key Vault certifikátů na virtuálních počítačích pomocí rozšíření virtuálního počítače.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 23a0d7cd45ceef8f97bb56d65f4807f8d60735dc
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601045"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Key Vault rozšíření virtuálního počítače pro Linux

Rozšíření virtuálního počítače Key Vault poskytuje automatickou aktualizaci certifikátů uložených v trezoru klíčů Azure. Konkrétně rozšíření monitoruje seznam pozorovaných certifikátů uložených v trezorech klíčů.  Při zjištění změny rozšíření načte a nainstaluje odpovídající certifikáty. Rozšíření nainstaluje úplný řetěz certifikátů na virtuálním počítači. Rozšíření virtuálního počítače Key Vault zveřejňuje a podporuje společnost Microsoft, aktuálně na virtuálních počítačích se systémem Linux. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření Key Vault virtuálního počítače pro Linux. 

### <a name="operating-system"></a>Operační systém

Rozšíření virtuálních počítačů Key Vault podporuje tyto distribuce systému Linux:

- Ubuntu-1804
- SUSE-15 

> [!NOTE]
> Chcete-li získat rozšířené funkce zabezpečení, připravte se na upgrade systémů Ubuntu-1604 a Debian-9, protože tyto verze dosáhnou konce stanoveného období podpory.
> 

### <a name="supported-certificate-content-types"></a>Podporované typy obsahu certifikátu

- #12 PKCS
- PEM

## <a name="prerequisities"></a>Konfigurátoru
  - Key Vault instance s certifikátem Viz [vytvoření Key Vault](../../key-vault/general/quick-create-portal.md)
  - Virtuální počítač/VMSS musí mít přiřazenou [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) .
  - Zásady přístupu Key Vault musí být nastavené s tajnými klíči `get` a `list` oprávněním pro SPRAVOVANOU identitu VM/VMSS k načtení části certifikátu tajného klíče. Další informace najdete v tématu [ověření Key Vault](../../key-vault/general/authentication.md) a [přiřazení zásad Key Vault přístupu](../../key-vault/general/assign-access-policy-cli.md).
  -  VMSS by měla mít následující nastavení identity: ` 
  "identity": {
  "type": "UserAssigned",
  "userAssignedIdentities": {
  "[parameters('userAssignedIdentityResourceId')]": {}
  }
  }
  `
  
 - Rozšíření integrace by mělo mít toto nastavení: `
                 "authenticationSettings": {
                    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
                    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
                  }
   `

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření Key Vault virtuálního počítače. Rozšíření nevyžaduje chráněná nastavení – veškerá jeho nastavení jsou považována za informace bez dopadu na zabezpečení. Přípona vyžaduje seznam monitorovaných tajných kódů, četnost dotazování a cílové úložiště certifikátů. Konkrétně se jedná o tyto:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Vaše sledované adresy URL certifikátů by měly být ve formátu `https://myVaultName.vault.azure.net/secrets/myCertName` .
> 
> Důvodem je to `/secrets` , že cesta vrátí úplný certifikát, včetně privátního klíče, ale `/certificates` cesta ne. Další informace o certifikátech najdete tady: [Key Vault certifikátů](../../key-vault/general/about-keys-secrets-certificates.md) .

> [!IMPORTANT]
> Vlastnost ' authenticationSettings ' je **požadována** pouze pro virtuální počítače s **identitami přiřazenými uživatelem**.
> Určuje identitu, která se má použít k Key Vault ověřování.


### <a name="property-values"></a>Hodnoty vlastností

| Name | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| vydavatel | Microsoft.Azure.KeyVault | řetězec |
| typ | KeyVaultForLinux | řetězec |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | řetězec |
| certificateStoreName | Ignoruje se na Linux. | řetězec |
| linkOnRenewal | false (nepravda) | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | řetězec |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | pole řetězců
| msiEndpoint | http://169.254.169.254/metadata/identity | řetězec |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | řetězec |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují aktualizaci po nasazení certifikátů. Toto rozšíření se dá nasadit na jednotlivé virtuální počítače nebo sady škálování virtuálních počítačů. Schéma a konfigurace jsou společné pro oba typy šablon. 

Konfigurace JSON pro rozšíření virtuálního počítače musí být vnořená v rámci fragmentu prostředků virtuálního počítače v šabloně, konkrétně `"resources": []` objekt pro šablonu virtuálního počítače a v případě sady škálování virtuálního počítače v `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` objektu Object.

 > [!NOTE]
> Rozšíření virtuálních počítačů by vyžadovalo přiřazení spravované identity systému nebo uživatele k ověření do trezoru klíčů.  Další informace najdete v tématu [ověření Key Vault a přiřazení zásad Key Vault přístupu.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Řazení závislosti rozšíření
Rozšíření virtuálního počítače Key Vault podporuje řazení rozšíření, pokud je nakonfigurováno. Ve výchozím nastavení hlásí rozšíření, že se úspěšně spustila hned po zahájení cyklického dotazování. Dá se ale nakonfigurovat tak, aby čekal na úspěšné stažení kompletního seznamu certifikátů před tím, než dohlásí úspěšné spuštění. Pokud jsou jiné přípony závislé na tom, že je před zahájením nainstalována úplná sada certifikátů, pak povolení tohoto nastavení umožní, aby tato rozšíření deklarovala závislost na rozšíření Key Vault. Tím se zabrání v zahájení těchto rozšíření, dokud nebudou nainstalovány všechny certifikáty, které jsou na nich závislé. Rozšíření zopakuje prvotní stažení po neomezenou dobu a zůstane ve `Transitioning` stavu.

Pokud to chcete zapnout, nastavte následující:
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```
> Značte Použití této funkce není kompatibilní se šablonou ARM, která vytváří identitu přiřazenou systémem a aktualizuje zásadu přístupu Key Vault s touto identitou. Výsledkem je zablokování, protože zásady přístupu k trezoru se nedají aktualizovat, dokud se nespustila všechna rozšíření. Místo toho byste měli před nasazením použít *jednu přiřazenou IDENTITU MSI* a před nasazením vaše trezory s touto identitou.

## <a name="azure-powershell-deployment"></a>Nasazení Azure PowerShell
> [!WARNING]
> Klienti PowerShellu se často přidávají `\` do `"` v settings.js, na kterých bude akvvm_service selže s chybou: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Azure PowerShell lze použít k nasazení Key Vault rozšíření virtuálního počítače do existujícího virtuálního počítače nebo sady škálování virtuálních počítačů. 

* Nasazení rozšíření na virtuální počítač:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Nasazení rozšíření do sady škálování virtuálních počítačů:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Pomocí rozhraní příkazového řádku Azure můžete nasadit rozšíření Key Vault virtuálního počítače do existujícího virtuálního počítače nebo sady škálování virtuálních počítačů. 
 
* Nasazení rozšíření na virtuální počítač:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Nasazení rozšíření do sady škálování virtuálních počítačů:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vmss-name "<vmssName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
Mějte na paměti následující omezení/požadavky:
- Omezení Key Vault:
  - Musí existovat v době nasazení. 
  - Zásady přístupu Key Vault musí být nastavené pro identitu VM/VMSS pomocí spravované identity. Další informace najdete v tématu [ověření Key Vault](../../key-vault/general/authentication.md) a [přiřazení zásad Key Vault přístupu](../../key-vault/general/assign-access-policy-cli.md).

### <a name="frequently-asked-questions"></a>Nejčastější dotazy

* Existuje omezení počtu observedCertificates, která můžete nastavit?
  Ne, Key Vault rozšíření virtuálního počítače nemá omezení počtu observedCertificates.


### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure PowerShell.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```
#### <a name="logs-and-configuration"></a>Protokoly a konfigurace

```
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>Použití symlink

Symbolické odkazy nebo symbolických odkazů jsou v podstatě pokročilé zkratky. Abyste se vyhnuli monitorování složky a k automatickému získání nejnovějšího certifikátu, můžete použít tento symlink `([VaultName].[CertificateName])` k získání nejnovější verze certifikátu v systému Linux.

### <a name="frequently-asked-questions"></a>Nejčastější dotazy

* Existuje omezení počtu observedCertificates, která můžete nastavit?
  Ne, Key Vault rozšíření virtuálního počítače nemá omezení počtu observedCertificates.

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
