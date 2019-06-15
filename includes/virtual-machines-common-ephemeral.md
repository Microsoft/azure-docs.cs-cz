---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 3e9885466d422a0428311ed3013e2ab34341cd25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391439"
---
Dočasné disky s operačním systémem jsou vytvořené v místním úložišti virtuální počítač (VM) a není trvalý do vzdáleného úložiště Azure. Dočasné disky s operačním systémem fungují dobře u bezstavových úloh, kdy aplikace jsou odolné proti selhání jednotlivých virtuálních počítačů, ale více jde o dobu potřebnou pro rozsáhlá nasazení nebo dobu obnovení z Image jednotlivých instancí virtuálních počítačů. Je také vhodné pro aplikace nasazené pomocí modelu nasazení classic, přesunout do modelu nasazení Resource Manager. U dočasných disků s operačním systémem se projevuje nižší latence čtení/zápisu na disk s operačním systémem a rychlejší obnovení virtuálního počítače z image. Kromě toho je disk s operačním systémem dočasné zdarma, vám být účtovány žádné náklady na úložiště pro disk s operačním systémem. 
 
Klíčové funkce dočasné disky jsou: 
- Může se použít s Tržiště imagí i vlastních imagí.
- Image virtuálních počítačů můžete nasadit až do velikosti mezipaměti virtuálního počítače.
- Možnost rychle resetovat nebo obnovit z Image své virtuální počítače do původního stavu spuštění.  
- Nižší latence běhu podobně jako dočasný disk. 
- Zdarma pro disk s operačním systémem. 
 
 
Hlavní rozdíly mezi trvalé a dočasné disky s operačním systémem:

|                             | Trvalý Disk s operačním systémem                          | Dočasný disk s operačním systémem                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Maximální velikost disku s operačním systémem      | 2 TiB                                                                                        | Velikost pro velikost virtuálního počítače nebo 2TiB, mezipaměti podle toho, co je menší - [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), a [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| Podporované velikosti virtuálních počítačů          | Vše                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Podporu pro disky typu           | Spravovaných a nespravovaných disků operačního systému                                                                | Spravovaný disk s operačním systémem jenom                                                               |
| Oblasti podpory              | Všechny oblasti                                                                                  | Všechny oblasti                              |
| Trvalost dat            | Data na disku operačního systému zapsaných na disk s operačním systémem ukládají ve službě Azure Storage                                  | Data zapsaná na disk s operačním systémem je uložit do místního úložiště virtuálního počítače a není trvale uložena do úložiště Azure. |
| Zastavit a uvolnit stavu      | Virtuálních počítačů a instancí škálovací sady můžete zastavit a uvolnit a byla restartována ze stavu zastavit a uvolnit | Virtuálních počítačů a instancí škálovací sady nelze zastavit a uvolnit                                  |
| Specializované podporu pro disky operačního systému | Ano                                                                                          | Ne                                                                                 |
| Změna velikosti disku operačního systému              | Nepodporuje během vytváření virtuálního počítače a poté, co je virtuální počítač zastavit a uvolnit                                | Nepodporuje během vytváření virtuálních počítačů pouze                                                  |
| Změna velikosti novou velikost virtuálních počítačů   | Data na disku operačního systému se zachovají.                                                                    | Odstranění dat na disk s operačním systémem, operační systém je znovu zřízeny                                      |

## <a name="scale-set-deployment"></a>Nasazení škálovací sady  
Proces vytvoření škálovací sady, který používá dočasný disk s operačním systémem je přidáte `diffDiskSettings` vlastnost `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` typ prostředku v šabloně. Zásady ukládání do mezipaměti, musí být nastavena na `ReadOnly` pro dočasný disk s operačním systémem. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-deployment"></a>Nasazení virtuálního počítače 
Nasazení virtuálního počítače s dočasným diskem operačního systému pomocí šablony. Proces vytvoření virtuálního počítače, který používá dočasné disky s operačním systémem je přidáte `diffDiskSettings` vlastnost Microsoft.Compute/virtualMachines typ prostředku v šabloně. Zásady ukládání do mezipaměti, musí být nastavena na `ReadOnly` pro dočasný disk s operačním systémem. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Obnovení z Image virtuálního počítače pomocí rozhraní REST
V současné době je jedinou metodou, jak obnovit z Image instance virtuálního počítače s dočasné disky s operačním systémem pomocí rozhraní REST API. Pro škálovací sady obnovování z Image je již k dispozici prostřednictvím prostředí Powershell, rozhraní příkazového řádku a na portálu.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jaká je velikost místní disky operačního systému?**

Odpověď: Pro verzi preview budeme podporovat platform a/nebo obrázky až do velikosti mezipaměti virtuálního počítače, kde budou všechny čtení/Zápisy na disk s operačním systémem místní na stejný uzel jako virtuální počítač. 

**Otázka: Velikost dočasného disku s operačním systémem**

Odpověď: Ne, po zřízení dočasný disk s operačním systémem nejde velikost disku s operačním systémem. 

**Otázka: Můžete připojit Managed Disks na dočasný virtuální počítač?**

Odpověď: Ano, můžete připojit spravovaného datového disku k virtuálnímu počítači, který používá dočasný disk s operačním systémem. 

**Otázka: Bude pro dočasné disky s operačním systémem podporuje všechny velikosti virtuálních počítačů?**

Odpověď: Ne, všech velikostí virtuálního počítače služby Premium Storage jsou podporované (DS, ES, služby FS, GS a min) s výjimkou B-series, řada N-series a velikosti řady H-series.  
 
**Otázka: Dočasný disk s operačním systémem lze stávající virtuální počítače a škálovací sady?**

Odpověď: Ne, dočasný disk s operačním systémem jde použít jenom během virtuálního počítače a vytvoření sady škálování. 

**Otázka: Je běžné, že dočasné disky s operačním systémem ve škálovací sadě kombinovat?**

Odpověď: Ne, nemůžete mít směs dočasné a trvalé instance disku operačního systému v rámci stejné škálovací sady. 

**Otázka: Dočasný disk s operačním systémem možné vytvářet pomocí Powershellu nebo rozhraní příkazového řádku?**

Odpověď: Ano, můžete vytvořit virtuální počítače s dočasný Disk s operačním systémem pomocí REST, šablon, Powershellu a rozhraní příkazového řádku.

**Otázka: Jaké funkce se dočasné disky s operačním systémem nepodporují?**

Odpověď: Dočasné disky nepodporují:
- Zachycení Image virtuálních počítačů
- Snímky disků 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Prohození disku operačního systému 
