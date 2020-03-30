---
title: Rozšíření virtuálního počítače Azure Key Vault pro Linux
description: Nasazení agenta provádějícího automatickou aktualizaci certifikátů trezoru klíčů na virtuálních počítačích pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: a4fb3ad2ce6225528910bbda9d98a38001242710
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298984"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Rozšíření virtuálního počítače Key Vault pro Linux

Rozšíření virtuálního počítače trezoru klíčů poskytuje automatickou aktualizaci certifikátů uložených v trezoru klíčů Azure. Rozšíření konkrétně monitoruje seznam pozorovaných certifikátů uložených v trezorech klíčů.  Po zjištění změny rozšíření načte a nainstaluje odpovídající certifikáty. Rozšíření virtuálního počítače trezoru klíčů je publikováno a podporováno společností Microsoft, která je aktuálně na virtuálních počítačích s Linuxem. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače trezoru klíčů pro Linux. 

### <a name="operating-system"></a>Operační systém

Rozšíření virtuálního počítače Trezoru klíčů podporuje tyto distribuce Linuxu:

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>Podporované typy obsahu certifikátů

- #12 PKCS

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON ukazuje schéma rozšíření virtuálního počítače trezoru klíčů. Rozšíření nevyžaduje chráněné nastavení - všechna jeho nastavení jsou považována za informace bez dopadu na zabezpečení. Rozšíření vyžaduje seznam sledovaných tajných kódů, frekvence dotazování a cílovéúložiště certifikátů. Konkrétně:  
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
> Adresy URL s pozorovanými certifikáty `https://myVaultName.vault.azure.net/secrets/myCertName`by měly být ve formuláři .
> 
> Důvodem je, že `/secrets` cesta vrátí úplný certifikát, `/certificates` včetně soukromého klíče, zatímco cesta není. Více informací o certifikátech naleznete zde: [Certifikáty trezoru klíčů](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota / Příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| vydavatel | Microsoft.Azure.KeyVault | řetězec |
| type | KeyVaultForlinux | řetězec |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | řetězec |
| certificateStoreName | MY | řetězec |
| linkOnRenewal | false (nepravda) | Boolean |
| certificateStoreLocation  | Místní stroj | řetězec |
| requiredInitialSync | true | Boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | pole řetězců


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Šablony jsou ideální při nasazování jednoho nebo více virtuálních počítačů, které vyžadují aktualizaci certifikátů po nasazení. Rozšíření lze nasadit na jednotlivé virtuální počítače nebo škálovací sady virtuálních strojů. Schéma a konfigurace jsou společné pro oba typy šablon. 

Konfigurace JSON pro rozšíření virtuálního počítače musí být vnořená uvnitř `"resources": []` fragmentu prostředku virtuálního počítače šablony, konkrétně `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` objektu pro šablonu virtuálního počítače a v případě škálovací sady virtuálních počítačů pod objektem.

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
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Nasazení Azure PowerShellu

Azure PowerShell se dá použít k nasazení rozšíření virtuálního počítače trezoru klíčů do existujícího virtuálního počítače nebo škálovací sady virtuálních strojů. 

* Nasazení rozšíření na virtuální ms:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Nasazení rozšíření na škálovací sadu virtuálních strojů :

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Nasazení azure cli

Azure CLI lze použít k nasazení rozšíření virtuálního počítače trezoru klíčů na existující virtuální počítač nebo škálovací sadu virtuálních strojů. 
 
* Nasazení rozšíření na virtuální ms:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Nasazení rozšíření na škálovací sadu virtuálních strojů :

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Mějte prosím na paměti následující omezení/požadavky:
- Omezení trezoru klíčů:
  - Musí existovat v době nasazení 
  - Zásady přístupu trezoru klíčů jsou nastavené pro identitu VM/VMSS pomocí MSI


## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z webu Azure Portal a pomocí Azure PowerShellu. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure PowerShellu.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
