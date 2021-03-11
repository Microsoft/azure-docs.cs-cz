---
title: Podpora Azure pro virtuální počítače 2. generace
description: Přehled podpory Azure pro virtuální počítače 2. generace
author: ju-shim
ms.service: virtual-machines
ms.subservice: generation-2-vm
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/26/2021
ms.author: jushiman
ms.openlocfilehash: a9c832d9a5e90cb2c9ffcbe8a26cdb08ebcddfcc
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553115"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Podpora virtuálních počítačů 2. generace v Azure

V Azure je teď dostupná podpora pro virtuální počítače 2. generace (VM). Generaci virtuálního počítače po jeho vytvoření nemůžete změnit, proto si přečtěte pokyny na této stránce před výběrem generace.

Virtuální počítače 2. generace podporují klíčové funkce, které se na virtuálních počítačích 1. generace nepodporují. Mezi tyto funkce patří zvýšené množství paměti, rozšíření Intel software Guard (Intel SGX) a virtualizovaná trvalá paměť (vPMEM). Virtuální počítače generace 2 s místním prostředím obsahují některé funkce, které ještě nejsou v Azure podporované. Další informace najdete v části [funkce a možnosti](#features-and-capabilities) .

Virtuální počítače generace 2 používají novou architekturu na bázi rozhraní UEFI namísto architektury založené na systému BIOS používané virtuálními počítači 1. generace. V porovnání s virtuálními počítači 1. generace můžou být virtuální počítače generace 2 vylepšené spouštění a časy instalace. Přehled virtuálních počítačů generace 2 a některých rozdílů mezi generace 1 a generace 2 najdete v tématu [Vytvoření virtuálního počítače generace 1 nebo 2 v technologii Hyper-V?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Velikosti virtuálních počítačů 2. generace

Virtuální počítače 1. generace jsou podporovány všemi velikostmi virtuálních počítačů v Azure (s výjimkou virtuálních počítačů s Mv2-Series). Azure teď nabízí podporu generace 2 pro následující vybranou řadu virtuálních počítačů:


* [Řady B-Series](sizes-b-series-burstable.md)
* [DCsv2-Series](dcv2-series.md)
* [DSv2-series](dv2-dsv2-series.md)
* [Dsv3-series](dv3-dsv3-series.md)
* [Dsv4-Series](dv4-dsv4-series.md)
* [Dasv4-Series](dav4-dasv4-series.md)
* [Ddsv4-Series](ddv4-ddsv4-series.md)
* [Esv3-Series](ev3-esv3-series.md)
* [Esv4-Series](ev4-esv4-series.md)
* [Easv4-Series](eav4-easv4-series.md)
* [Edsv4-Series](edv4-edsv4-series.md)
* [Řada Fsv2](fsv2-series.md)
* [Řady GS](sizes-previous-gen.md#gs-series)
* [Řada HB](hb-series.md)
* [Řada HC](hc-series.md)
* [Řada ls-series](sizes-previous-gen.md#ls-series) 
* [Řada Lsv2](lsv2-series.md)
* [Řada M](m-series.md)
* [Mv2-Series](mv2-series.md)<sup>1</sup>
* [Řada NCv2](ncv2-series.md) 
* [Řada NCv3](ncv3-series.md)
* [Řada ND](nd-series.md)
* [Řada NVv3](nvv3-series.md)
* [Řada NVv4](nvv4-series.md)
* [Řada NCasT4_v3](nct4-v3-series.md)

<sup>1</sup> Mv2-Series nepodporuje image virtuálních počítačů 1. generace a podporují jenom podmnožinu imagí 2. generace. Podrobnosti najdete v [dokumentaci k Mv2-Series](mv2-series.md) .


## <a name="generation-2-vm-images-in-azure-marketplace"></a>Image virtuálních počítačů 2. generace v Azure Marketplace

Virtuální počítače generace 2 podporují následující image na webu Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 pro, Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16,04, 18,04, 19,04, 19,10 
* RHEL 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 7,0
* Cent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4
* Oracle Linux 7,7, 7,7-CI

> [!NOTE]
> Konkrétní velikosti virtuálního počítače, jako je například Mv2-Series, mohou podporovat pouze podmnožinu těchto imagí. podrobné informace najdete v příslušné dokumentaci k virtuálnímu počítači.

## <a name="on-premises-vs-azure-generation-2-vms"></a>Místní a Azure generace 2 – virtuální počítače

Azure v současné době nepodporuje některé funkce, které místní technologie Hyper-V podporuje pro virtuální počítače 2. generace.

| Funkce generace 2                | Místní Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Zabezpečené spuštění                         | :heavy_check_mark:  | Pomocí důvěryhodného spuštění (Preview)   |
| Chráněný virtuální počítač                         | :heavy_check_mark:  | znak   |
| vTPM                                | :heavy_check_mark:  | Pomocí důvěryhodného spuštění (Preview)  |
| Zabezpečení založené na virtualizaci (VBS) | :heavy_check_mark:  | Pomocí důvěryhodného spuštění (Preview)   |
| Formát VHDX                         | :heavy_check_mark:  | znak   |

Další informace najdete v tématu [důvěryhodné spuštění (Preview)](trusted-launch.md).

## <a name="features-and-capabilities"></a>Funkce a možnosti

### <a name="generation-1-vs-generation-2-features"></a>Generace 1 vs. generace 2 – funkce

| Funkce | 1. generace | 2. generace |
|---------|--------------|--------------|
| Spouštění             | PCAT                      | UEFI                               |
| Řadiče disku | IDE – integrované vývojové prostředí                       | SCSI                               |
| Velikost virtuálních počítačů         | Všechny velikosti virtuálních počítačů | [Zobrazit dostupné velikosti](#generation-2-vm-sizes) |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generace 1 vs. generace 2 – možnosti

| Schopnost | 1. generace | 2. generace |
|------------|--------------|--------------|
| Disk s operačním systémem > 2 TB                    | znak                | :heavy_check_mark: |
| Vlastní disk/image/prohození operačního systému         | :heavy_check_mark: | :heavy_check_mark: |
| Podpora sady škálování virtuálních počítačů | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Zálohování a obnovení                    | :heavy_check_mark: | :heavy_check_mark: |
| Galerie sdílených imagí              | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)             | :heavy_check_mark: | :heavy_check_mark:                |
| [Šifrování na straně serveru](disk-encryption.md)            | :heavy_check_mark: | :heavy_check_mark: |

## <a name="creating-a-generation-2-vm"></a>Vytvoření virtuálního počítače 2. generace

### <a name="marketplace-image"></a>Image z Marketplace

V Azure Portal nebo Azure CLI můžete vytvořit virtuální počítače 2. generace z image Marketplace, která podporuje spouštění pomocí UEFI.

#### <a name="azure-portal"></a>portál Azure

Níže jsou uvedené kroky k vytvoření virtuálního počítače generace 2 (Gen2) v Azure Portal.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Vyberte **Vytvořit prostředek**.
1. Na levé straně klikněte na **Zobrazit vše** z Azure Marketplace.
1. Vyberte bitovou kopii, která podporuje Gen2.
1. Klikněte na **Vytvořit**.
1. Na kartě **Upřesnit** v části **generování virtuálního počítače** vyberte možnost **Obecné 2** .
1. Na kartě **základy** klikněte v části **Podrobnosti instance** na **Velikost** a otevřete okno **Vybrat velikost virtuálního počítače** .
1. Vyberte [podporovaný virtuální počítač 2. generace](#generation-2-vm-sizes).
1. Projděte si zbývající stránky a dokončete vytváření virtuálního počítače.

![Vyberte virtuální počítač 1. generace 2 nebo 2. generace.](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

PowerShell můžete také použít k vytvoření virtuálního počítače přímo odkazující na generaci SKU 1 nebo 2. generace.

Pomocí následující rutiny prostředí PowerShell můžete například získat seznam SKU v `WindowsServer` nabídce.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Pokud vytváříte virtuální počítač s Windows Serverem 2012 jako operačním systémem, vyberete buď SKLADOVOU položku virtuálního počítače 1. generace (BIOS) nebo generace 2 (UEFI), což bude vypadat takto:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

V části [funkce a možnosti](#features-and-capabilities) najdete aktuální seznam podporovaných imagí na webu Marketplace.

#### <a name="azure-cli"></a>Azure CLI

Alternativně můžete pomocí Azure CLI Zobrazit všechny dostupné image generace 2 uvedené **vydavatelem**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>Spravovaná Image nebo spravovaný disk

Virtuální počítač 2. generace můžete vytvořit ze spravované bitové kopie nebo spravovaného disku stejným způsobem, jako byste vytvořili virtuální počítač 1. generace.

### <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Virtuální počítače 2. generace můžete vytvořit také pomocí sady Virtual Machine Scale Sets. V Azure CLI použijte Azure Scale Sets k vytvoření virtuálních počítačů 2. generace.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* **Jsou virtuální počítače generace 2 dostupné ve všech oblastech Azure?**  
    Ano. Ale ne všechny [velikosti virtuálních počítačů 2. generace](#generation-2-vm-sizes) jsou dostupné v každé oblasti. Dostupnost virtuálního počítače 2. generace závisí na dostupnosti velikosti virtuálního počítače.

* **Existuje cenový rozdíl mezi virtuálními počítači generace 1 a generace 2?**  
   No.

* **Mám soubor. VHD z místního virtuálního počítače 2. generace. Můžu soubor. VHD použít k vytvoření virtuálního počítače generace 2 v Azure?**
  Ano, můžete přenést soubor. VHD generace 2 do Azure a použít ho k vytvoření virtuálního počítače 2. generace. K tomu použijte následující postup:
    1. Nahrajte soubor. VHD do účtu úložiště ve stejné oblasti, ve které chcete vytvořit virtuální počítač.
    1. Vytvořte spravovaný disk ze souboru. VHD. Nastavte vlastnost generace technologie Hyper-V na hodnotu v2. Následující příkazy PowerShellu při vytváření spravovaného disku nastavily vlastnost generace technologie Hyper-V.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Jakmile je disk k dispozici, vytvořte virtuální počítač připojením tohoto disku. Vytvořený virtuální počítač bude virtuální počítač 2. generace.
    Když se vytvoří virtuální počítač 2. generace, můžete pro něj volitelně generalizovat image tohoto virtuálního počítače. Pomocí generalizace image ji můžete použít k vytvoření více virtuálních počítačů.

* **Návody zvýšit velikost disku s operačním systémem?**  

  Disky s operačním systémem větší než 2 TiB jsou pro virtuální počítače 2. generace nové. Ve výchozím nastavení jsou disky s operačním systémem menší než 2 TiB pro virtuální počítače 2. generace. Velikost disku můžete zvětšit až na Doporučené maximum 4 TiB. Zvyšte velikost disku operačního systému pomocí Azure CLI nebo Azure Portal. Informace o tom, jak programově rozbalovat disky, najdete v tématu **Změna velikosti disku** pro [Windows](./windows/expand-os-disk.md) nebo [Linux](./linux/resize-os-disk-gpt-partition.md).

  Zvýšení velikosti disku operačního systému z Azure Portal:

  1. V Azure Portal přejdete na stránku vlastností virtuálního počítače.
  1. Pokud chcete virtuální počítač vypnout a zrušit jeho přidělení, vyberte tlačítko **zastavit** .
  1. V části **disky** vyberte disk s operačním systémem, který chcete zvětšit.
  1. V části **disky** vyberte **Konfigurace** a aktualizujte **Velikost** na požadovanou hodnotu.
  1. Vraťte se na stránku vlastností virtuálního počítače a **Spusťte** virtuální počítač.
  
  Může se zobrazit upozornění na disky s operačním systémem větší než 2 TiB. Upozornění se nevztahuje na virtuální počítače 2. generace. Velikosti disků s operačním systémem větší než 4 TiB se ale nepodporují.

* **Podporují virtuální počítače generace 2 urychlené síťové služby?**  
    Ano. Další informace najdete v tématu [Vytvoření virtuálního počítače s akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-cli.md)službami.

* **Podporují virtuální počítače generace 2 v Azure zabezpečené spouštění nebo vTPM?**
    VTPM i zabezpečené spouštění jsou funkce důvěryhodného spuštění (Preview) pro virtuální počítače 2. generace. Další informace najdete v tématu věnovaném [důvěryhodnému spuštění](trusted-launch.md).
    
* **Podporuje se VHDX na generaci 2?**  
    Ne, virtuální počítače 2. generace podporují jenom virtuální pevný disk.

* **Podporují virtuální počítače generace 2 Azure Ultra Disk Storage?**  
    Ano.

* **Můžu migrovat virtuální počítač z generace 1 na generaci 2?**  
    Ne, generaci virtuálního počítače po jeho vytvoření nemůžete změnit. Pokud potřebujete přepínat mezi generací virtuálních počítačů, vytvořte nový virtuální počítač jiné generace.

* **Proč se při pokusu o vytvoření virtuálního počítače s Gen2 nepovoluje velikost svého virtuálního počítače v selektoru velikosti?**

    To může být vyřešeno následujícím způsobem:

    1. Ověřte, že je vlastnost **generování virtuálního počítače** na kartě **Upřesnit** nastavená na hodnotu **Obecné 2** .
    1. Ověřte, že hledáte [Velikost virtuálního počítače, která podporuje virtuální počítače s Gen2](#generation-2-vm-sizes).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [důvěryhodném spuštění (Preview)](trusted-launch-portal.md) s virtuálními počítači 2. generace.

Seznamte [se s virtuálními počítači generace 2 v Hyper-V](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
