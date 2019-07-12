---
title: Podpora Azure pro virtuální počítače generace 2 (preview) | Dokumentace Microsoftu
description: Přehled podpory Azure pro virtuální počítače generace 2
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
ms.openlocfilehash: 7abc1b7cd3be775af03aeaf558f631060cc6f81c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722728"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Podpora generace 2 virtuální počítače (preview) v Azure

> [!IMPORTANT]
> Podpora Azure pro virtuální počítače generace 2 je aktuálně ve verzi preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb a se nedoporučuje pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [dodatečnými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podpora pro 2. generace virtuálních počítačů (VM) je teď dostupná ve verzi preview v Azure. Nelze změnit generaci virtuálního počítače po jeho vytvoření, takže prostudujte si důležité informace na této stránce, než se rozhodnete generace. 

Virtuální počítače generace 2 podporují klíčové funkce, které nejsou podporované ve virtuálních počítačích generace 1. Tyto funkce patří větší paměť, rozšíření Guard Software Intel (Intel SGX) a virtualizované trvalé paměti (vPMEM). Virtuální počítače generace 2 mají také některé funkce, které zatím nejsou podporované v Azure. Další informace najdete v tématu [funkce a možnosti](#features-and-capabilities) oddílu.

Místo architektury založené na systému BIOS používané generace 1 virtuální počítače, použijte virtuální počítače generace 2 nové architektuře spouštěcí rozhraní UEFI. Porovnání s virtuálními počítači 1. generace, virtuální počítače generace 2 může mít lepší časů spuštění a instalace. Přehled virtuálních počítačů 2. generace a některé rozdíly mezi 1 a generace 2 najdete v tématu [bych si měl vytvořit virtuální počítač generace 1 nebo 2 v Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Velikosti virtuálních počítačů 2. generace

Virtuální počítače generace 1 podporuje všechny velikosti virtuálních počítačů v Azure. Azure nyní nabízí ve verzi preview 2. generace podporu pro následující vybrané virtuální počítače řady:

* [Dsv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) a [Dsv3-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [Řady GS-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Řada ls-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) a [Lsv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Image virtuálních počítačů generace 2 na webu Azure Marketplace

Virtuální počítače generace 2 podporují následující Image Marketplace:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Místní vs. Virtuální počítače Azure generace 2

Azure v současné době nepodporuje některé funkce, které on-premises Hyper-V podporuje pro virtuální počítače generace 2.

| Funkce 2. generace                | On-premises Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Zabezpečené spouštění                         | :heavy_check_mark:  | : x:.   |
| Chráněný virtuální počítač                         | :heavy_check_mark:  | : x:.   |
| vTPM                                | :heavy_check_mark:  | : x:.   |
| Zabezpečení na základě virtualizace (VBS) | :heavy_check_mark:  | : x:.   |
| Formát VHDX                         | :heavy_check_mark:  | : x:.   |

## <a name="features-and-capabilities"></a>Funkce a možnosti

### <a name="generation-1-vs-generation-2-features"></a>Generace 1 a generace 2 funkce

| Funkce | 1\. generace | 2\. generace |
|---------|--------------|--------------|
| Spouštění             | PCAT                      | UEFI                               |
| Disk řadiče | IDE – integrované vývojové prostředí                       | SCSI                               |
| Velikost virtuálních počítačů         | Všechny velikosti virtuálních počítačů | Pouze virtuální počítače, které podporují službu premium storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generace 1 a generace 2 možnosti

| Funkce | 1\. generace | 2\. generace |
|------------|--------------|--------------|
| Operační systém na disku > 2 TB                    | : x:.                        | :heavy_check_mark: |
| Vlastní/image/prohození disků operačního systému         | :heavy_check_mark:         | :heavy_check_mark: |
| Škálovací sady virtuálních počítačů podpory | :heavy_check_mark:         | :heavy_check_mark: |
| Azure Site Recovery a backup                        | :heavy_check_mark:         | : x:.                |
| Galerie sdílené bitové kopie              | :heavy_check_mark:         | : x:.                |
| Azure disk encryption             | :heavy_check_mark:         | : x:.                |

## <a name="creating-a-generation-2-vm"></a>Vytváření 2. generace virtuálních počítačů

### <a name="marketplace-image"></a>Image z Marketplace

Webu Azure portal nebo rozhraní příkazového řádku Azure můžete vytvořit generace 2 virtuální počítače z image Marketplace, který podporuje rozhraní UEFI.

`windowsserver-gen2preview` Nabídka obsahuje jen pro Image 2. generace Windows. Tento balení se vyhnete nejasnostem mezi generací 1 a generace 2 imagí. K vytvoření 2. generace virtuálních počítačů, vyberte **Imagí** z této nabídky a postupovat podle standardního procesu k vytvoření virtuálního počítače.

V současné době Marketplace nabízí následující Windows generace 2 imagí:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Zobrazit [funkce a možnosti](#features-and-capabilities) najdete aktuální seznam podporovaných imagí Marketplace.

### <a name="managed-image-or-managed-disk"></a>Spravované image nebo spravovaný disk

Můžete vytvořit 2. generace virtuálního počítače ze spravované image nebo spravovaný disk stejným způsobem, jako byste vytvořili 1. generace virtuálních počítačů.

### <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Můžete také vytvořit generace 2 virtuální počítače pomocí škálovací sady virtuálních počítačů. V Azure CLI použijte škálování Azure nastaví vytvořit generace 2 virtuální počítače.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* **Jsou generace 2 virtuální počítače k dispozici ve všech oblastech Azure?**  
    Ano. Ale ne všechny [velikosti virtuálních počítačů 2. generace](#generation-2-vm-sizes) jsou k dispozici v každé oblasti. Dostupnost generace 2 virtuální počítač, na kterém závisí na dostupnost velikost virtuálního počítače.

* **Je nějaký cena rozdíl mezi 1 a generace 2 virtuální počítače?**  
   Ne.

* **Jak můžu zvýšit velikost disku operačního systému?**  
  Disky s operačním systémem větší než 2 TB se 2. generace virtuálních počítačů. Ve výchozím nastavení disky s operačním systémem byly menší než 2 TB pro virtuální počítače generace 2. Můžete zvýšit velikost disku, doporučená až 4 TB. Zvýšit velikost disku operačního systému pomocí rozhraní příkazového řádku Azure nebo na webu Azure portal. Informace o tom, jak prostřednictvím kódu programu rozbalte disky najdete v tématu [Změna velikosti disku](expand-os-disk.md).

  Chcete-li zvýšit velikost disku operačního systému z webu Azure portal:

  1. Na webu Azure Portal přejděte na stránku vlastností virtuálního počítače.
  1. Chcete-li vypnout a uvolnit virtuálního počítače, vyberte **Zastavit** tlačítko.
  1. V **disky** vyberte disk s operačním systémem chcete zvětšit.
  1. V **disky** vyberte **konfigurace**a aktualizovat **velikost** na hodnotu, kterou chcete.
  1. Přejděte zpět na stránku vlastností virtuálního počítače a **Start** virtuálního počítače.
  
  Může se zobrazit upozornění pro disky s operačním systémem větší než 2 TB. Upozornění se nevztahuje na virtuální počítače generace 2. Velikost disku operačního systému větší než 4 TB ale *nedoporučuje.*

* **Generace 2 virtuální počítače podporují akcelerované síťové služby nabízí?**  
    Ano. Další informace najdete v tématu [vytvoření virtuálního počítače s akcelerovanými síťovými službami](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Je VHDX podporovány na generace 2?**  
    Ne, virtuální počítače generace 2 podporují jenom virtuální pevný disk.

* **Ultra diskové úložiště Azure podporují virtuální počítače generace 2?**  
    Ano.

* **Můžu migrovat virtuální počítač z 1. generace do 2. generace?**  
    Ne, nelze změnit generaci virtuálního počítače po jeho vytvoření. Pokud je potřeba přepnout mezi generací virtuálních počítačů, vytvořte nový virtuální počítač z jiné generace.

## <a name="next-steps"></a>Další postup

* Další informace o [2. generace virtuálních počítačů Hyper-v](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Zjistěte, jak [Příprava virtuálního pevného disku](prepare-for-upload-vhd-image.md) k odesílání z místních systémů do Azure.
