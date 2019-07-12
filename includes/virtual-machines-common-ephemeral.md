---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805876"
---
Dočasné disky s operačním systémem se vytvoří v úložišti místního virtuálního počítače (VM) a nebyl uložen do vzdáleného úložiště Azure. Dočasné disky s operačním systémem fungují dobře u bezstavových úloh, kdy aplikace jsou toleranci vůči selhání jednotlivých virtuálních počítačů, ale jsou více ovlivněny čas nasazení virtuálního počítače nebo obnovování z Image jednotlivých instancí virtuálních počítačů. Díky disk s operačním systémem dočasné budete mít nižší latence čtení a zápisu na disk s operačním systémem a rychlejší obnovení z Image virtuálního počítače. 
 
Klíčové funkce dočasné disky jsou: 
- Ideální pro bezstavové aplikace.
- Bylo možné se webu Marketplace a vlastních imagí.
- Instance možnost rychlé resetování nebo obnovení z Image virtuálních počítačů a škálovací sady do původního stavu spuštění.  
- Nižší latenci, podobně jako dočasný disk. 
- Dočasné disky s operačním systémem je zdarma, vám být účtovány žádné náklady na úložiště pro disk s operačním systémem.
- Jsou k dispozici ve všech oblastech Azure. 
- Dočasné disky s operačním systémem podporuje [Galerie obrázků Shared](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Hlavní rozdíly mezi trvalé a dočasné disky s operačním systémem:

|                             | Trvalý Disk s operačním systémem                          | Dočasný disk s operačním systémem                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Maximální velikost disku s operačním systémem      | 2 TiB                                                                                        | Velikost pro velikost virtuálního počítače nebo 2TiB, mezipaměti podle toho, co je menší. Pro **velikost v GB mezipaměti**, naleznete v tématu [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), a [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Podporované velikosti virtuálních počítačů          | Vše                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Podporu pro disky typu           | Spravovaných a nespravovaných disků operačního systému                                                                | Spravovaný disk s operačním systémem jenom                                                               |
| Oblasti podpory              | Všechny oblasti                                                                                  | Všechny oblasti                              |
| Trvalost dat            | Data na disku operačního systému zapsaných na disk s operačním systémem ukládají ve službě Azure Storage                                  | Data zapsaná na disk s operačním systémem je uložit do místního úložiště virtuálního počítače a není trvale uložena do úložiště Azure. |
| Zastavit a uvolnit stavu      | Virtuálních počítačů a instancí škálovací sady můžete zastavit a uvolnit a byla restartována ze stavu zastavit a uvolnit | Virtuálních počítačů a instancí škálovací sady nelze zastavit a uvolnit                                  |
| Specializované podporu pro disky operačního systému | Ano                                                                                          | Ne                                                                                 |
| Změna velikosti disku operačního systému              | Nepodporuje během vytváření virtuálního počítače a poté, co je virtuální počítač zastavit a uvolnit                                | Nepodporuje během vytváření virtuálních počítačů pouze                                                  |
| Změna velikosti novou velikost virtuálních počítačů   | Data na disku operačního systému se zachovají.                                                                    | Odstranění dat na disk s operačním systémem, operační systém je znovu zřízeny                                      |

## <a name="size-requirements"></a>Požadavky na velikost

Můžete nasadit Image virtuálního počítače a instance až do velikosti mezipaměti virtuálního počítače. Například Image Windows serveru Standard z webu marketplace jsou přibližně 127 GB, což znamená, že potřebujete velikost virtuálního počítače, který má větší než 127 GB mezipaměti. V takovém případě [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) má velikost mezipaměti 86 GiB, který není dostatečně velký. Standard_DS2_v2 má velikost mezipaměti 172 GiB, což je příliš velká. V tomto případě Standard_DS3_v2 je nejmenší velikost v DSv2 series, které můžete použít u této image. Základní Image Linuxu Image Marketplace a Windows Server, které jsou označeny `[smallsize]` jsou obvykle přibližně 30 GB a lze použít i většinu dostupných velikostí virtuálních počítačů.

Dočasné disky vyžadují také, že velikost virtuálního počítače podporuje storage úrovně Premium. Velikosti obvykle (ale ne vždy) mít `s` v názvu, jako jsou DSv2 a EsV3. Další informace najdete v tématu [velikosti virtuálních počítačů Azure](../articles/virtual-machines/linux/sizes.md) podrobnosti velikostí kolem, které podporují službu Premium storage.

## <a name="powershell"></a>PowerShell

Chcete-li používat dočasné disky pro nasazení virtuálního počítače prostředí PowerShell, použijte [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) v konfiguraci virtuálního počítače. Nastavte `-DiffDiskSetting` k `Local` a `-Caching` k `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Pro škálovací sady nasazení, použijte [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) rutiny ve vaší konfiguraci. Nastavte `-DiffDiskSetting` k `Local` a `-Caching` k `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Rozhraní příkazového řádku

Chcete-li použít dočasný disk pro nasazení virtuálního počítače rozhraní příkazového řádku, nastavte `--ephemeral-os-disk` parametr [vytvořit az vm](/cli/azure/vm#az-vm-create) k `true` a `--os-disk-caching` parametr `ReadOnly`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Pro škálovací sady, můžete použít stejné `--ephemeral-os-disk true` parametr pro [az vmss create](/cli/azure/vmss#az-vmss-create) a nastavit `--os-disk-caching` parametr `ReadOnly`.

## <a name="portal"></a>Portál   

Na webu Azure Portal, můžete použít dočasné disky při nasazování virtuálního počítače tak, že otevřete **Upřesnit** část **disky** kartu. Pro **použít dočasný disk s operačním systémem** vyberte **Ano**.

![Snímek obrazovky zobrazující přepínač pro výběr použít dočasný disk s operačním systémem](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Pokud je možnost pro používají dočasné disky s šedá, pravděpodobně jste vybrali velikost virtuálního počítače, který nemá velikost mezipaměti, která je větší než image operačního systému nebo, která nepodporuje Premium storage. Přejděte zpět **Základy** stránku a zkuste zvolit jinou velikost virtuálního počítače.

Můžete také vytvořit škálovací sady s pomocí portálu pro dočasné disky s operačním systémem. Dejte pozor, vyberte velikost virtuálního počítače s dostatečně velká velikost mezipaměti a pak v **použít dočasný disk s operačním systémem** vyberte **Ano**.

![Snímek obrazovky zobrazující přepínač pro výběr má použít dočasný disk s operačním systémem pro škálovací sady](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Nasazení šablony škálovací sady  
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

## <a name="vm-template-deployment"></a>Nasazení šablony virtuálního počítače 
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

Odpověď: Podporuje platformy a vlastních imagí až do velikosti mezipaměti virtuálního počítače, kde budou všechny čtení/Zápisy na disk s operačním systémem místní na stejný uzel jako virtuální počítač. 

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
