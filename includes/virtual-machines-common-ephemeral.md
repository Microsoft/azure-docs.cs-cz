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
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155390"
---
Dočasné disky operačního systému se vytvářejí v místním úložišti virtuálních počítačů a nejsou uloženy do vzdáleného úložiště Azure. Dočasné disky operačního systému fungují dobře pro bezstavové úlohy, kde jsou aplikace tolerantní k jednotlivým selháním virtuálních počítačů, ale jsou více ovlivněny časem nasazení virtuálního počítače nebo reimagingem jednotlivých instancí virtuálních počítačů. S dočasným diskem operačního systému získáte nižší latenci čtení a zápisu na disk operačního systému a rychlejší reimage virtuálního počítače. 
 
Klíčové vlastnosti dočasných disků jsou: 
- Ideální pro bezstavové aplikace.
- Lze je použít s marketplace i s vlastními obrázky.
- Možnost rychlého resetování nebo reimage virtuálních mích a škálovací sady instancí do původního spouštěcího stavu.  
- Nižší latence, podobně jako dočasný disk. 
- Dočasné disky operačního systému jsou zdarma, nevznikají vám žádné náklady na úložiště pro disk operačního systému.
- Jsou dostupné ve všech oblastech Azure. 
- Dočasný disk operačního systému je podporován [galerií sdílených obrázků](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Klíčové rozdíly mezi trvalými a dočasnými disky operačního systému:

|                             | Trvalý disk operačního systému                          | Dočasný disk s operačním systémem                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limit velikosti disku operačního systému      | 2 TB                                                                                        | Velikost mezipaměti pro velikost virtuálního počítače nebo 2TiB, podle toho, co je menší. Velikost **mezipaměti v GiB**naleznete v [tématech DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md)a [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Podporované velikosti virtuálních míchana          | Všechny                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Podpora typu disku           | Spravovaný a nespravovaný disk operačního systému                                                                | Pouze spravovaný disk operačního systému                                                               |
| Podpora regionu              | Všechny regiony                                                                                  | Všechny regiony                              |
| Trvalost dat            | Data disku operačního systému zapsaná na disk operačního systému jsou uložena v úložišti Azure Storage                                  | Data zapsaná na disk operačního systému se ukládají do místního úložiště virtuálních počítačů a nejsou trvalá ve službě Azure Storage. |
| Stav stop-deallocated      | Virtuální chody a instance škálovací sady lze zastavit a restartovat ze stavu stop-deallocated | Virtuální aplikace a instance škálovací sady nelze zastavit deallocated                                  |
| Podpora specializovaného disku operačního systému | Ano                                                                                          | Ne                                                                                 |
| Změna velikosti disku operačního systému              | Podporované během vytváření virtuálních mísa a po virtuálním virtuálním ms se zastaví                                | Podporované pouze při vytváření virtuálních modulů                                                  |
| Změna velikosti na novou velikost virtuálního počítače   | Data disku operačního systému jsou zachována                                                                    | Data na disku s os jsou smazána, operační systém je znovu zřízen                                      |

## <a name="size-requirements"></a>Požadavky na velikost

Můžete nasadit image virtuálních počítače a instancí až do velikosti mezipaměti virtuálních počítače. Například standardní windows server image z trhu jsou asi 127 GiB, což znamená, že potřebujete velikost virtuálního počítače, který má cache větší než 127 GiB. V tomto případě [má Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) velikost mezipaměti 86 GiB, což není dostatečně velké. Standard_DS3_v2 má velikost mezipaměti 172 GiB, což je dostatečně velké. V tomto případě je Standard_DS3_v2 nejmenší velikost řady DSv2, kterou můžete použít s tímto obrázkem. Základní linuxové image na Marketplace a Windows `[smallsize]` Server image, které jsou označeny mají tendenci být kolem 30 GiB a můžete použít většinu dostupných velikostí virtuálních počítačových aplikací.

Dočasné disky také vyžadují, aby velikost virtuálního počítače podporovala úložiště Premium. Velikosti obvykle (ale ne vždy) mají `s` v názvu, jako DSv2 a EsV3. Další informace najdete v [tématu velikosti virtuálních počítačů Azure](../articles/virtual-machines/linux/sizes.md) podrobnosti o tom, které velikosti podporují úložiště Premium.

## <a name="powershell"></a>PowerShell

Pokud chcete použít dočasný disk pro nasazení virtuálního počítače powershellu, použijte v konfiguraci virtuálního počítače [set-azVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk) Nastavte `-DiffDiskSetting` na `Local` `-Caching` a `ReadOnly`na .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Pro nasazení škálovací sady použijte rutinu [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) ve vaší konfiguraci. Nastavte `-DiffDiskSetting` na `Local` `-Caching` a `ReadOnly`na .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Rozhraní příkazového řádku

Chcete-li použít dočasný disk pro nasazení virtuálního počítače `--ephemeral-os-disk` rozhraní CLI, nastavte `true` parametr `--os-disk-caching` v `ReadOnly` [az vm create](/cli/azure/vm#az-vm-create) to a parametr na .

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

Pro škálovací sady `--ephemeral-os-disk true` použijete stejný parametr pro vytvoření [az-vmss](/cli/azure/vmss#az-vmss-create) a nastavíte `--os-disk-caching` parametr na `ReadOnly`.

## <a name="portal"></a>Portál   

Na webu Azure Portal můžete při nasazování virtuálního počítače použít dočasné disky otevřením části **Upřesnit** na kartě **Disky.** V **části Použití dočasného disku operačního systému** vyberte **Ano**.

![Snímek obrazovky s přepínatým tlačítkem pro výběr dočasného disku operačního systému](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Pokud je možnost použití dočasného disku zašedlá, je možné, že jste vybrali velikost virtuálního počítače, která nemá velikost mezipaměti větší než bitová kopie operačního systému nebo která nepodporuje úložiště Premium. Vraťte se na stránku **Základy** a zkuste vybrat jinou velikost virtuálního počítače.

Pomocí portálu můžete také vytvářet škálovací sady s dočasnými disky operačního systému. Jen se ujistěte, že jste vybrali velikost virtuálního počítače s dostatečně velkou velikostí mezipaměti a pak v **části Použít dočasný disk operačního systému** vyberte **Ano**.

![Snímek obrazovky s přepínacím tlačítkem pro výběr dočasného disku operačního systému pro škálovací sadu](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Nasazení šablony škálovací sady  
Proces vytvoření škálovací sady, která používá dočasný disk operačního `diffDiskSettings` systému, `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` je přidání vlastnosti k typu prostředku v šabloně. Zásady ukládání do mezipaměti musí `ReadOnly` být také nastaveny na dočasný disk operačního systému. 


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
Virtuální modul můžete nasadit s dočasným diskem operačního systému pomocí šablony. Proces vytvoření virtuálního počítače, který používá dočasné disky operačního `diffDiskSettings` systému, je přidání vlastnosti do typu prostředku Microsoft.Compute/virtualMachines v šabloně. Zásady ukládání do mezipaměti musí `ReadOnly` být také nastaveny na dočasný disk operačního systému. 

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


## <a name="reimage-a-vm-using-rest"></a>Opětovné zobrazení virtuálního virtuálního virtuálního montovapomocí rest
Můžete reimage instance virtuálního počítače s dočasným diskem operačního systému pomocí rozhraní REST API, jak je popsáno níže a prostřednictvím portálu Azure portal tím, že přejdete do podokna Přehled virtuálního počítače. Pro škálovací sady je reimaging již k dispozici prostřednictvím prostředí Powershell, CLI a portálu.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jaká je velikost místních disků operačního systému?**

A: Podporujeme platformu a vlastní image, až do velikosti mezipaměti virtuálního počítače, kde všechny čtení a zápisy na disk operačního systému bude místní na stejném uzlu jako virtuální počítač. 

**Otázka: Lze velikost dočasného disku operačního systému?**

A: Ne, po zřízení dočasný disk operačního systému, disk operačního systému nelze velikost. 

**Otázka: Můžu připojit spravované disky k dočasnému virtuálnímu virtuálnímu počítače?**

Odpověď: Ano, můžete připojit spravovaný datový disk k virtuálnímu počítače, který používá dočasný disk operačního systému. 

**Otázka: Budou podporovány všechny velikosti virtuálních počítačů pro dočasné disky operačního systému?**

A: Ne, všechny velikosti virtuálních zařízení úložiště Premium jsou podporovány (DS, ES, FS, GS a M) s výjimkou velikosti řady B, Řady N a H.  
 
**Otázka: Lze dočasný disk operačního systému použít na existující virtuální počítače a škálovací sady?**

A: Ne, dočasný disk operačního systému lze použít pouze během vytvoření virtuálního počítače a škálovací sady. 

**Otázka: Můžete kombinovat dočasné a normální disky operačního systému v škálovací sadě?**

A: Ne, nemůžete mít kombinaci dočasných a trvalých instancí disku operačního systému ve stejné škálovací sadě. 

**Otázka: Lze dočasný disk operačního systému vytvořit pomocí prostředí Powershell nebo CLI?**

A: Ano, můžete vytvořit virtuální počítače s dočasným diskem operačního systému pomocí REST, šablony, PowerShell a CLI.

**Otázka: Jaké funkce nejsou podporovány s dočasným diskem operačního systému?**

A: Dočasné disky nepodporují:
- Zachycení obrazů virtuálních mitek
- Snímky disků 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Prohození disku operačního systému 
