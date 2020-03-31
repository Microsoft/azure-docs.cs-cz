---
title: Podpora Azure pro virtuální počítače generace 2
description: Přehled podpory Azure pro virtuální počítače generace 2
author: ju-shim
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: b68179caed4df5efd3696d7d6a3739a1e07a3c80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267297"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Podpora pro virtuální počítače generace 2 v Azure

Podpora virtuálních počítačů generace 2 je teď dostupná v Azure. Po vytvoření nelze změnit generaci virtuálního počítače, proto si před výběrem generace přečtěte důležité informace na této stránce.

Virtuální virtuální aplikace generace 2 podporují klíčové funkce, které nejsou podporované ve virtuálních milionech generace 1. Mezi tyto funkce patří zvýšená paměť, rozšíření Intel Software Guard Extensions (Intel SGX) a virtualizovaná trvalá paměť (vPMEM). Virtuální počítače generace 2 spuštěné místně, mají některé funkce, které ještě nejsou podporované v Azure. Další informace naleznete v části [Funkce a možnosti.](#features-and-capabilities)

Virtuální virtuální zařízení generace 2 používají novou spouštěcí architekturu založenou na Rozhraní UEFI, nikoli architekturu založenou na systému BIOS používanou virtuálními zařízeními generace 1. Ve srovnání s virtuálními zařízeními generace 1 mohou virtuální aplikace generace 2 zlepšit dobu spouštění a instalace. Přehled virtuálních počítačů generace 2 a některých rozdílů mezi generací 1 a generací 2 najdete [v tématu Mám vytvořit virtuální počítač generace 1 nebo 2 v technologii Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="generation-2-vm-sizes"></a>Generace 2 velikosti virtuálních počítače

Virtuální počítače generace 1 jsou podporované všemi velikostmi virtuálních zařízení v Azure (s výjimkou virtuálních počítačích řady Mv2). Azure teď nabízí podporu generace 2 pro následující vybrané řady virtuálních počítače:

* [Řada B](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [Řada DC](../dcv2-series.md)
* [Řada DSv2](../dv2-dsv2-series.md) a [Dsv3](../dv3-dsv3-series.md)
* [Řada Esv3](../ev3-esv3-series.md)
* [Řada Fsv2](../fsv2-series.md)
* [Řada GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [Řada HB](../hb-series.md)
* [Řada HC](../hc-series.md)
* [Řada LS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) a [Řada LSV2](../lsv2-series.md)
* [Řada Mv2](../mv2-series.md)
* [Řada NCv2](../ncv2-series.md) a [Řada NCv3](../ncv3-series.md)
* [Řada ND](../nd-series.md)
* [Řada NVv3](../nvv3-series.md)

> [!NOTE]
> Použití image virtuálních počítače generace 2 pro virtuální počítače řady Mv2 je obecně dostupné, protože řada Mv2 pracuje výhradně s image mise 2 vs generace. Image virtuálních počítačů generace 1 nejsou podporované na virtuálních počítačích řady Mv2. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Image virtuálních virtuálních počítačů pro generování 2 na Azure Marketplace

Virtuální virtuální chod generace 2 podporují následující image Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 7,0
* Cent OS 8,0, 7,7, 7,6, 7,5, 7,4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>Místní virtuální počítače azure generace 2

Azure momentálně nepodporuje některé funkce, které místní Technologie Hyper-V podporuje pro virtuální počítače generace 2.

| Funkce generace 2                | Místní Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Zabezpečené spuštění                         | :heavy_check_mark:  | :x:   |
| Chráněný virtuální počítač                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Zabezpečení založené na virtualizaci (VBS) | :heavy_check_mark:  | :x:   |
| Formát VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funkce a možnosti

### <a name="generation-1-vs-generation-2-features"></a>Generace 1 vs generace 2 funkce

| Funkce | 1. generace | 2. generace |
|---------|--------------|--------------|
| Spouštěcí             | PCAT         | Uefi |
| Řadiče disků | IDE – integrované vývojové prostředí          | SCSI |
| Velikost virtuálních počítačů         | Všechny velikosti virtuálních počítače | Jenom virtuální zařízení, které podporují prémiové úložiště |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generace 1 vs generace 2 schopnosti

| Schopnost | 1. generace | 2. generace |
|------------|--------------|--------------|
| Disk operačního systému > 2 TB                    | :x:                | :heavy_check_mark: |
| Vlastní disk/obrázek/odkládací operační systém         | :heavy_check_mark: | :heavy_check_mark: |
| Podpora škálovací sady virtuálních strojů | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Zálohování/obnovení                    | :heavy_check_mark: | :heavy_check_mark: |
| Sdílená galerie obrázků              | :heavy_check_mark: | :heavy_check_mark: |
| Šifrování disku Azure             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Vytvoření generace 2 Virtuální ho sondy

### <a name="marketplace-image"></a>Obrázek tržiště

Na webu Azure Portal nebo Azure CLI můžete vytvořit virtuální počítače generace 2 z image Marketplace, která podporuje spouštění UEFI.

#### <a name="azure-portal"></a>portál Azure

Níže jsou kroky k vytvoření virtuálního počítače generace 2 (Gen2) na webu Azure Portal.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Vyberte **Vytvořit prostředek**.
1. Klikněte na **Zobrazit vše** na Azure Marketplace na levé straně.
1. Vyberte obrázek, který podporuje Gen2.
1. Klikněte na **Vytvořit**.
1. Na kartě **Upřesnit** vyberte v části **Generování virtuálního soudu** možnost **Gen 2.**
1. Na kartě **Základy** v části **Podrobnosti instance**přejděte na **Velikost** a otevřete okno **Vybrat velikost virtuálního** počítače.
1. Vyberte [podporovaný virtuální virtuální modul generace 2](#generation-2-vm-sizes).
1. Projděte [si tok vytváření portálu Azure](quick-create-portal.md) a dokončete vytváření virtuálního počítače.

![Vyberte virtuální virtuální vír Gen 1 nebo Gen 2.](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

PowerShell můžete také použít k vytvoření virtuálního virtuálního klíče přímým odkazem na výrobní jednotku SKU generace 1 nebo generace 2.

Pomocí následující rutiny prostředí PowerShell můžete například získat seznam skum v nabídce. `WindowsServer`

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Případně můžete pomocí rozhraní příkazového příkazového příkazu Azure zobrazit všechny dostupné image generace 2 uvedené podle **vydavatele**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Pokud vytváříte virtuální hosto s Windows Server 2012 jako operační systém, pak vyberete buď generace 1 (BIOS) nebo generace 2 (UEFI) SKU virtuálního zařízení, které vypadají takto:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Aktuální seznam podporovaných bitových kopií Marketplace najdete v části [Funkce a možnosti.](#features-and-capabilities)

### <a name="managed-image-or-managed-disk"></a>Spravovaná bitová kopie nebo spravovaný disk

Můžete vytvořit generace 2 Virtuální modul z spravované bitové kopie nebo spravovaného disku stejným způsobem, jako byste vytvořit generaci 1 virtuálního počítače.

### <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Můžete také vytvořit generace 2 virtuálních počítačů pomocí škálovací sady virtuálních strojů. V azure cli použijte škálovací sady Azure k vytvoření virtuálních počítačů generace 2.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* **Jsou virtuální počítače generace 2 dostupné ve všech oblastech Azure?**  
    Ano. Ale ne všechny [velikosti virtuálních počítače generace 2](#generation-2-vm-sizes) jsou k dispozici ve všech oblastech. Dostupnost virtuálního počítače generace 2 závisí na dostupnosti velikosti virtuálního počítače.

* **Existuje cenový rozdíl mezi virtuálními stránkami generace 1 a generace 2?**  
    Ne.

* **Mám soubor .vhd z místní generace 2 VM. Můžu použít tento soubor .vhd k vytvoření virtuálního počítače generace 2 v Azure?**
  Ano, do Azure můžete přenést soubor 2 .vhd generace a použít ho k vytvoření virtuálního počítače generace 2. K tomu postupujte takto:
    1. Nahrajte rozhraní .vhd do účtu úložiště ve stejné oblasti, kde chcete vytvořit virtuální počítač.
    1. Vytvořte spravovaný disk ze souboru VHD. Nastavte vlastnost Hyper-V Generation na V2. Následující příkazy prostředí PowerShell nastavují vlastnost Hyper-V Generation při vytváření spravovaného disku.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Jakmile je disk k dispozici, vytvořte virtuální počítače připojením tohoto disku. Vytvořený virtuální ms bude generace 2 Virtuální ho.
    Když se vytvoří virtuální ms generace 2, můžete volitelně zobecnit image tohoto virtuálního soudu. Generalizací obrázku ji můžete použít k vytvoření více virtuálních počítačů.

* **Jak zvětším velikost disku operačního systému?**  
  Disky operačního systému větší než 2 TB jsou nové pro virtuální počítače generace 2. Ve výchozím nastavení jsou disky operačního systému menší než 2 TB pro virtuální počítače generace 2. Velikost disku můžete zvětšit až na doporučenou maximálně 4 TB. Pomocí azure cli nebo portálazure zvětšit velikost disku operačního systému. Informace o programovém rozbalení disků naleznete [v tématu Změna velikosti disku](expand-disks.md).

  Zvětšení velikosti disku operačního systému z webu Azure:

  1. Na webu Azure Portal přejděte na stránku vlastností virtuálního počítače.
  1. Pokud chcete virtuální ho vypnout a navrátit, vyberte tlačítko **Zastavit.**
  1. V části **Disky** vyberte disk operačního systému, který chcete zvětšit.
  1. V části **Disky** vyberte **Konfigurace**a aktualizujte požadovanou hodnotu **Velikost.**
  1. Vraťte se na stránku vlastností virtuálního počítače a **spusťte** virtuální ho.

  Může se zobrazit upozornění pro disky operačního systému větší než 2 TB. Upozornění se nevztahuje na virtuální chod generace 2. Velikosti disků operačního systému větší než 4 TB se však *nedoporučuje.*

* **Podporují virtuální zařízení generace 2 zrychlené sítě?**  
    Ano. Další informace najdete [v tématu Vytvoření virtuálního virtuálního ms s akceleračně sítí](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Je VHDX podporován o generaci 2?**  
    Ne, virtuální chody generace 2 podporují jenom virtuální disk.

* **Podporují virtuální počítače Azure Ultra Disk Storage pro generaci 2?**  
    Ano.

* **Můžu migrovat virtuální hod z generace 1 na generaci 2?**  
    Ne, generování virtuálního soudu po jeho vytvoření nemůžete změnit. Pokud potřebujete přepínat mezi generacemi virtuálních virtuálních společností, vytvořte nový virtuální virtuální virtuální ms jiné generace.

* **Proč není velikost virtuálního počítače povolena ve voliči velikosti, když se snažím vytvořit virtuální virtuální počítače Gen2?**

    To lze vyřešit následujícím způsobem:

    1. Ověřte, zda je vlastnost **generování virtuálního zařízení** nastavená na **Gen 2** na kartě **Upřesnit.**
    1. Ověřte, zda hledáte [velikost virtuálního počítače, který podporuje virtuální počítače Gen2](#generation-2-vm-sizes).

## <a name="next-steps"></a>Další kroky

* Další informace o [virtuálních počítačích generace 2 v hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
