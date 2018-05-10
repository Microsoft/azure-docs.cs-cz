---
title: Klíč trezoru rozšíření virtuálního počítače pro Windows | Microsoft Docs
description: Nasaďte agenta provedení automatické aktualizaci Key Vault tajné klíče na virtuální počítače pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Rozšíření virtuálního počítače Key Vault pro Windows

## <a name="overview"></a>Přehled

Rozšíření virtuálního počítače klíč trezoru poskytuje automatické aktualizaci tajné klíče uložené v trezoru služby Azure klíče. Konkrétně rozšíření monitorování seznam zjištěnými certifikáty uložené v trezorů klíčů a při zjišťování změnu, načte a nainstaluje odpovídající certifikáty. Rozšíření virtuálního počítače Key Vault je publikována a podporovaný společností Microsoft, nyní na virtuálních počítačích systému Windows. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače klíč trezoru pro systém Windows. 

## <a name="prerequisites"></a>Požadavky

Rozšíření virtuálního počítače klíč trezoru závisí na rozšíření virtuálního počítače identita spravované služby, aby bylo možné ověření samotné služby Key Vault. Naleznete v dokumentaci k rozšíření virtuálního počítače MSI další podrobnosti.

### <a name="operating-system"></a>Operační systém

Rozšíření virtuálního počítače klíč trezoru podporuje aktuálně všechny verze systému Windows.

| Distribuce | Verze |
|---|---|
| Windows | Jádro |

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření virtuálního počítače klíč trezoru pro Windows vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následujícím kódu JSON znázorňuje schéma pro rozšíření virtuálního počítače klíč trezoru. Rozšíření nevyžaduje chráněné nastavení – všechny jeho nastavení jsou považovány za informace bez dopad na zabezpečení. Rozšíření vyžaduje seznam monitorovaných tajné klíče, frekvence cyklického dotazování a cílové úložiště certifikátů. Zejména:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota nebo příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | datum |
| Vydavatele | Microsoft.Azure.KeyVault.Edp | řetězec |
| type | KeyVaultForWindows | řetězec |
| typeHandlerVersion | 0.0 | celá čísla |
| pollingIntervalInS | 3600 | celá čísla |
| Název_úložiště_certifikátů | MOJE | řetězec |
| CertificateStoreLocation  | LOCAL_MACHINE | řetězec |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | Pole řetězců


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Šablony jsou ideální, pokud nasazujete jednu nebo víc virtuálních počítačů, které vyžadují post nasazení aktualizace certifikátů. Rozšíření se dá nasadit na jednotlivé virtuální počítače nebo virtuálního počítače sady škálování. Schéma a konfigurace jsou společné pro oba typy šablon. 

Konfigurace JSON pro rozšíření virtuálního počítače musí být vnořena ve fragmentu prostředků virtuálního počítače v šabloně, konkrétně `"resources": []` objektu virtuálního počítače.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Nasazení Azure PowerShell

Prostředí Azure PowerShell můžete použít k nasazení rozšíření virtuálního počítače klíč trezoru do existujícího virtuálního počítače nebo škálovací sadu virtuálních počítačů. 

* Nasazení rozšíření na virtuálním počítači:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Nasazení rozšíření pro sadu škálování virtuálního počítače, například jako součást clusteru Service Fabric:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

Upozorňujeme následující omezení nebo požadavky:
- Nasazení je třeba provést v oblasti jihu USA
- Omezení Key Vault:
    - Musí existovat v době nasazení 
    - Musí být umístěný ve stejné oblasti a skupině prostředků jako nasazení
    - Musí být povolena pro nasazení a nasazení šablony

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Ukázky pro nasazení rozšíření klíč trezoru virtuálního počítače v Azure CLI, bude brzy k dispozici. 

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření mohou být načteny z portálu Azure a pomocí prostředí Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure PowerShell.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

Výstupu spuštění rozšíření je zaznamenána do následujícího souboru:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte Get podpory. Informace o používání Azure podporovat, najdete v tématu [podporu Microsoft Azure – nejčastější dotazy](https://azure.microsoft.com/support/faq/).
