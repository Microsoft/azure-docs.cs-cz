---
title: Rozšíření virtuálního počítače s Azure Key Vault pro Linux | Microsoft Docs
description: Nasaďte agenta, který provádí automatickou aktualizaci Key Vault certifikátů na virtuálních počítačích pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
author: msmbaldwin
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/23/2018
ms.author: mbaldwin
ms.openlocfilehash: 5991d3d2197822b239b946de66f020dd258f835a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584385"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Key Vault rozšíření virtuálního počítače pro Linux

Rozšíření virtuálního počítače Key Vault poskytuje automatickou aktualizaci certifikátů uložených v trezoru klíčů Azure. Konkrétně rozšíření monitoruje seznam pozorovaných certifikátů uložených v trezorech klíčů.  Při zjištění změny rozsahu načte a nainstaluje odpovídající certifikáty. Rozšíření virtuálního počítače Key Vault zveřejňuje a podporuje společnost Microsoft, aktuálně na virtuálních počítačích se systémem Linux. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření Key Vault virtuálního počítače pro Linux. 

### <a name="operating-system"></a>Operační systém

Rozšíření virtuálních počítačů Key Vault podporuje tyto distribuce systému Linux:

- Ubuntu-1604
- Ubuntu-1804
- Debian – 9
- SUSE-15 

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření Key Vault virtuálního počítače. Rozšíření nevyžaduje chráněná nastavení – veškerá jeho nastavení jsou považována za informace bez dopadu na zabezpečení. Přípona vyžaduje seznam monitorovaných tajných kódů, četnost dotazování a cílové úložiště certifikátů. Konkrétně:  
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
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForLinux",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "linkOnRenewal": <Not available on Linux e.g.: false>,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "requireInitialSync": <initial synchronization of certificates e..g: true>,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```

> [!NOTE]
> Vaše sledované adresy URL certifikátů by měly být ve formátu `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Důvodem je to, že cesta `/secrets` vrátí úplný certifikát, včetně privátního klíče, a `/certificates` cestu ne. Další informace o certifikátech najdete tady: [Key Vault certifikátů](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates) .


### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| Microsoft | Microsoft. Azure. webtrezor. EDP | řetězec |
| type | KeyVaultForLinux | řetězec |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | řetězec |
| certificateStoreName | MY | řetězec |
| linkOnRenewal | false (nepravda) | Boolean |
| certificateStoreLocation  | LocalMachine | řetězec |
| requiredInitialSync | true (pravda) | Boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | pole řetězců


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují aktualizaci po nasazení certifikátů. Toto rozšíření se dá nasadit na jednotlivé virtuální počítače nebo sady škálování virtuálních počítačů. Schéma a konfigurace jsou společné pro oba typy šablon. 

Konfigurace JSON pro rozšíření virtuálního počítače musí být vnořená v rámci fragmentu prostředků virtuálního počítače šablony, konkrétně `"resources": []` objekt pro šablonu virtuálního počítače a v případě sady škálování virtuálního počítače v objektu `"virtualMachineProfile":"extensionProfile":{"extensions" :[]`.

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
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForLinux",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Nasazení Azure PowerShell

Azure PowerShell lze použít k nasazení Key Vault rozšíření virtuálního počítače do existujícího virtuálního počítače nebo sady škálování virtuálních počítačů. 

* Nasazení rozšíření na virtuální počítač:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
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
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
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
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Nasazení rozšíření do sady škálování virtuálních počítačů:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Mějte na paměti následující omezení/požadavky:
- Omezení Key Vault:
    - Musí existovat v době nasazení. 
    - Zásada přístupu Key Vault je nastavená pro identitu VM/VMSS pomocí MSI.


## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
