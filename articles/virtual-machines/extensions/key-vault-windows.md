---
title: Azure Key Vault rozšíření virtuálního počítače pro Windows
description: Nasaďte agenta, který provádí automatickou aktualizaci Key Vault tajných klíčů na virtuálních počítačích pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
author: msmbaldwin
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 09/23/2018
ms.author: mbaldwin
ms.openlocfilehash: 03351e964fc7247f87d0b823fae06fc03ff18de7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152098"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Key Vault rozšíření virtuálního počítače pro Windows

Rozšíření virtuálního počítače Key Vault poskytuje automatickou aktualizaci certifikátů uložených v trezoru klíčů Azure. Konkrétně rozšíření monitoruje seznam pozorovaných certifikátů uložených v trezorech klíčů a při zjištění změny, načtení a instalaci odpovídajících certifikátů. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače s Key Vault pro Windows. 

### <a name="operating-system"></a>Operační systém

Rozšíření VM Key Vault podporuje následující verze systému Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření Key Vault virtuálního počítače. Přípona nevyžaduje chráněná nastavení – veškerá jeho nastavení se považují za veřejné informace. Přípona vyžaduje seznam monitorovaných certifikátů, četnost dotazování a cílové úložiště certifikátů. Konkrétně:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
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

| Název | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault| řetězec |
| type | KeyVaultForWindows | řetězec |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | řetězec |
| certificateStoreName | MY | řetězec |
| linkOnRenewal | false | Boolean |
| certificateStoreLocation  | LocalMachine | řetězec |
| requiredInitialSync | true (pravda) | Boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | pole řetězců


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují aktualizaci po nasazení certifikátů. Toto rozšíření se dá nasadit na jednotlivé virtuální počítače nebo sady škálování virtuálních počítačů. Schéma a konfigurace jsou společné pro oba typy šablon. 

Konfigurace JSON pro rozšíření virtuálního počítače musí být vnořená v rámci fragmentu prostředků virtuálního počítače šablony, konkrétně `"resources": []` objekt pro šablonu virtuálního počítače a v případě sady škálování virtuálního počítače v objektu `"virtualMachineProfile":"extensionProfile":{"extensions" :[]`.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
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
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
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
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Pomocí rozhraní příkazového řádku Azure můžete nasadit rozšíření Key Vault virtuálního počítače do existujícího virtuálního počítače nebo sady škálování virtuálních počítačů. 
 
* Nasazení rozšíření na virtuální počítač:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Nasazení rozšíření do sady škálování virtuálních počítačů:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
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

Rozšíření provádění výstup je zaznamenán do následujícího souboru:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
