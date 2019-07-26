---
title: Podpora Azure pro virtuální počítače generace 2 (Preview) | Microsoft Docs
description: Přehled podpory Azure pro virtuální počítače 2. generace
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 7cd938369cdc4f4ca711a442d89cd6ef3d6d08e8
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477076"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Podpora virtuálních počítačů 2. generace (Preview) v Azure

> [!IMPORTANT]
> Podpora Azure pro virtuální počítače 2. generace je momentálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Podpora virtuálních počítačů 2. generace je teď dostupná ve verzi Preview v Azure. Generaci virtuálního počítače po jeho vytvoření nemůžete změnit, proto si přečtěte pokyny na této stránce před výběrem generace. 

Virtuální počítače 2. generace podporují klíčové funkce, které se na virtuálních počítačích 1. generace nepodporují. Mezi tyto funkce patří zvýšené množství paměti, rozšíření Intel software Guard (Intel SGX) a virtualizovaná trvalá paměť (vPMEM). Virtuální počítače generace 2 také obsahují některé funkce, které ještě nejsou podporované v Azure. Další informace najdete v části [funkce a možnosti](#features-and-capabilities) .

Virtuální počítače generace 2 používají novou architekturu na bázi rozhraní UEFI namísto architektury založené na systému BIOS používané virtuálními počítači 1. generace. V porovnání s virtuálními počítači 1. generace můžou být virtuální počítače generace 2 vylepšené spouštění a časy instalace. Přehled virtuálních počítačů generace 2 a některých rozdílů mezi generace 1 a generace 2 najdete v tématu [Vytvoření virtuálního počítače generace 1 nebo 2 v technologii Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Velikosti virtuálních počítačů 2. generace

Virtuální počítače 1. generace jsou podporovány všemi velikostmi virtuálních počítačů v Azure. Azure teď nabízí podporu generace 2 pro tuto vybranou řadu virtuálních počítačů:

* [Řady B-Series](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* Řady [Dsv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) a [Dsv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [Řady GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Ls-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) a [Lsv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* Řady [NCv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series) a [NCv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [Řada KS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Image virtuálních počítačů 2. generace v Azure Marketplace

Virtuální počítače generace 2 podporují následující image na webu Marketplace:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Místní vs. Virtuální počítače Azure generace 2

Azure v současné době nepodporuje některé funkce, které místní technologie Hyper-V podporuje pro virtuální počítače 2. generace.

| Funkce generace 2                | Místní technologie Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Zabezpečené spouštění                         | :heavy_check_mark:  | znak   |
| Stíněný virtuální počítač                         | :heavy_check_mark:  | znak   |
| vTPM                                | :heavy_check_mark:  | znak   |
| Zabezpečení založené na virtualizaci (VBS) | :heavy_check_mark:  | znak   |
| Formát VHDX                         | :heavy_check_mark:  | znak   |

## <a name="features-and-capabilities"></a>Funkce a možnosti

### <a name="generation-1-vs-generation-2-features"></a>Generace 1 vs. generace 2 – funkce

| Funkce | Generace 1 | Generace 2 |
|---------|--------------|--------------|
| Spouštění             | PCAT                      | UEFI                               |
| Řadiče disku | IDE – integrované vývojové prostředí                       | SCSI                               |
| Velikost virtuálních počítačů         | Všechny velikosti virtuálních počítačů | Jenom virtuální počítače, které podporují Premium Storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generace 1 vs. generace 2 – možnosti

| Funkce | Generace 1 | Generace 2 |
|------------|--------------|--------------|
| Disk s operačním systémem > 2 TB                    | znak                        | :heavy_check_mark: |
| Vlastní disk/image/prohození operačního systému         | :heavy_check_mark:         | :heavy_check_mark: |
| Podpora sady škálování virtuálních počítačů | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/záloha                        | :heavy_check_mark:         | znak                |
| Galerie sdílených imagí              | :heavy_check_mark:         | znak                |
| Azure Disk Encryption             | :heavy_check_mark:         | znak                |

## <a name="creating-a-generation-2-vm"></a>Vytvoření virtuálního počítače 2. generace

### <a name="marketplace-image"></a>Obrázek Marketplace

V Azure Portal nebo Azure CLI můžete vytvořit virtuální počítače 2. generace z image Marketplace, která podporuje spouštění pomocí UEFI.

`windowsserver-gen2preview` Nabídka obsahuje jenom image Windows generace 2. Toto balení zabraňuje nejasnostem mezi 1. generace a imagemi 2. generace. Pokud chcete vytvořit virtuální počítač 2. generace, vyberte z této nabídky **Image** a při vytváření virtuálního počítače použijte standardní proces.

V současné době Marketplace nabízí následující image Windows generace 2:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

V části [funkce a možnosti](#features-and-capabilities) najdete aktuální seznam podporovaných imagí na webu Marketplace.

### <a name="managed-image-or-managed-disk"></a>Spravovaná Image nebo spravovaný disk

Virtuální počítač 2. generace můžete vytvořit ze spravované bitové kopie nebo spravovaného disku stejným způsobem, jako byste vytvořili virtuální počítač 1. generace.

### <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Virtuální počítače 2. generace můžete vytvořit také pomocí sady Virtual Machine Scale Sets. V Azure CLI použijte Azure Scale Sets k vytvoření virtuálních počítačů 2. generace.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* **Jsou virtuální počítače generace 2 dostupné ve všech oblastech Azure?**  
    Ano. Ale ne všechny [velikosti virtuálních počítačů 2. generace](#generation-2-vm-sizes) jsou dostupné v každé oblasti. Dostupnost virtuálního počítače 2. generace závisí na dostupnosti velikosti virtuálního počítače.

* **Existuje cenový rozdíl mezi virtuálními počítači generace 1 a generace 2?**  
   Ne.

* **Návody zvýšit velikost disku s operačním systémem?**  
  Disky s operačním systémem větší než 2 TB jsou pro virtuální počítače 2. generace nové. Ve výchozím nastavení jsou disky s operačním systémem menší než 2 TB pro virtuální počítače 2. generace. Velikost disku můžete zvětšit na maximálně 4 TB. Zvyšte velikost disku operačního systému pomocí Azure CLI nebo Azure Portal. Informace o tom, jak programově rozbalovat disky, najdete v tématu [Změna velikosti disku](expand-os-disk.md).

  Zvýšení velikosti disku operačního systému z Azure Portal:

  1. V Azure Portal přejdete na stránku vlastností virtuálního počítače.
  1. Pokud chcete virtuální počítač vypnout a zrušit jeho přidělení, vyberte tlačítko **zastavit** .
  1. V části **disky** vyberte disk s operačním systémem, který chcete zvětšit.
  1. V části **disky** vyberte **Konfigurace**a aktualizujte **Velikost** na požadovanou hodnotu.
  1. Vraťte se na stránku vlastností virtuálního počítače a **Spusťte** virtuální počítač.
  
  Může se zobrazit upozornění na disky s operačním systémem větší než 2 TB. Upozornění se nevztahuje na virtuální počítače 2. generace. Velikosti disků s operačním systémem větší než 4 TB se ale nedoporučují *.*

* **Podporují virtuální počítače generace 2 urychlené síťové služby?**  
    Ano. Další informace najdete v tématu [Vytvoření virtuálního počítače s akcelerovanými síťovými](../../virtual-network/create-vm-accelerated-networking-cli.md)službami.

* **Podporuje se VHDX na generaci 2?**  
    Ne, virtuální počítače 2. generace podporují jenom virtuální pevný disk.

* **Podporují virtuální počítače generace 2 Azure Ultra Disk Storage?**  
    Ano.

* **Můžu migrovat virtuální počítač z generace 1 na generaci 2?**  
    Ne, generaci virtuálního počítače po jeho vytvoření nemůžete změnit. Pokud potřebujete přepínat mezi generací virtuálních počítačů, vytvořte nový virtuální počítač jiné generace.

## <a name="next-steps"></a>Další postup

* Seznamte [se s virtuálními počítači generace 2 v Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Naučte se, jak [připravit VHD](prepare-for-upload-vhd-image.md) pro nahrání z místních systémů do Azure.
