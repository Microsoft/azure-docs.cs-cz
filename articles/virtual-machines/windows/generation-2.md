---
title: Virtuální počítače generace 2 (preview) v Azure | Dokumentace Microsoftu
description: Přehled Azure 2. generace virtuálních počítačů
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 1dcc0d3a652ccbf365a18ce734a54dc78515b1a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388368"
---
# <a name="generation-2-vms-preview-on-azure"></a>Virtuální počítače generace 2 (preview) v Azure

> [!IMPORTANT]
> Virtuální počítače generace 2 jsou aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podpora pro 2. generace virtuálních počítačů (VM) je teď dostupná ve verzi public preview v Azure. Nelze změnit generaci virtuálního počítače po jeho vytvoření. Proto doporučujeme, abyste si na základě aspektů [tady](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) i informace na této stránce před výběrem generace.

2\. generace virtuálních počítačů podporu klíčových funkcí, které nejsou podporovány na virtuálních počítačích generace 1, jako například: zvýšili paměti, Intel® Software Guard rozšíření (SGX) a virtuální trvalé paměti (vPMEM). Virtuální počítače generace 2 mají také některé funkce, které zatím nejsou podporované v Azure. Další informace najdete v tématu [funkce a možnosti](#features-and-capabilities) oddílu.

Virtuální počítače generace 2 pomocí nové vs architektury založené na režimu UEFI spouštění systémem BIOS architektura používány virtuálními počítači 1. generace. Ve srovnání s 1. generace virtuálních počítačů, virtuální počítače generace 2 může mít lepší časů spuštění a instalace. Přehled virtuálních počítačů 2. generace a některé hlavní rozdíly mezi 1 a generace 2 najdete v tématu [bych si měl vytvořit virtuální počítač generace 1 nebo 2 v Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Velikosti virtuálních počítačů 2. generace

Virtuální počítače generace 1 podporuje všechny velikosti virtuálních počítačů v Azure. Azure nyní nabízí podporu 2. generace pro následující vybrané virtuální počítače řady ve verzi public preview:

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) a [Dsv3-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [Řady GS-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#gs-series)
* [Řada ls-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) a [Lsv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Image virtuálních počítačů generace 2 na webu Azure Marketplace

Virtuální počítače generace 2 podporují následující Image Azure Marketplace:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Vs místní virtuální počítače Azure generace 2

Azure aktuálně nepodporuje některé funkce, které on-premises Hyper-V podporuje pro virtuální počítače generace 2.

| Funkce 2. generace                | On-premises Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Zabezpečené spouštění                         | :heavy_check_mark:  | : x:.   |
| Chráněný virtuální počítač                         | :heavy_check_mark:  | : x:.   |
| vTPM                                | :heavy_check_mark:  | : x:.   |
| Zabezpečení na základě virtualizace (VBS) | :heavy_check_mark:  | : x:.   |
| Formát VHDX                         | :heavy_check_mark:  | : x:.   |

## <a name="features-and-capabilities"></a>Funkce a možnosti

### <a name="generation-1-vs-generation-2-features"></a>Funkce 2. generace 1. generace vs

| Funkce | 1\. generace | 2\. generace |
|---------|--------------|--------------|
| Spouštění             | PCAT                      | UEFI                               |
| Disk řadiče | IDE – integrované vývojové prostředí                       | SCSI                               |
| Velikosti virtuálních počítačů         | K dispozici na všech velikostí virtuálních počítačů | Storage úrovně Premium podporované pouze virtuální počítače |

### <a name="generation-1-vs-generation-2-capabilities"></a>Možnosti 2. generace 1. generace vs

| Schopnost | 1\. generace | 2\. generace |
|------------|--------------|--------------|
| Operační systém na disku > 2 TB                    | : x:.                        | :heavy_check_mark: |
| Vlastní/Image/prohození disků operačního systému         | :heavy_check_mark:         | :heavy_check_mark: |
| Škálovací sady virtuálních počítačů podpory | :heavy_check_mark:         | :heavy_check_mark: |
| Azure Site Recovery a Backup                        | :heavy_check_mark:         | : x:.                |
| Sdílená galerie obrázků              | :heavy_check_mark:         | : x:.                |
| Azure Disk Encryption             | :heavy_check_mark:         | : x:.                |

## <a name="creating-a-generation-2-vm"></a>Vytváření 2. generace virtuálních počítačů

### <a name="marketplace-image"></a>Image z Marketplace

Virtuální počítače generace 2 je vytvořit z image marketplace (která podporuje rozhraní UEFI) prostřednictvím webu Azure portal nebo rozhraní příkazového řádku Azure.

`windowsserver-gen2preview` Nabídka obsahuje jen pro Image 2. generace Windows. Tím se vyhnete záměně s ohledem na 1. generace a 2. generace imagí. Chcete-li vytvořit generace 2 virtuální počítače, vyberte **Imagí** z této nabídky a postupovat podle standardního procesu vytváření virtuálního počítače.

V současné době následující generace Windows jsou publikovány 2 imagí v Tržišti Azure Marketplace:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Jak jsme bude pokračovat v přidávání dalších imagí, které podporují 2. generace, naleznete v části Možnosti seznam podporovaných Tržiště imagí.

### <a name="managed-image-or-managed-disk"></a>Spravované image nebo spravovaný disk

2 virtuální počítače můžete vytvořit ze spravované image nebo spravovaný disk stejným způsobem, jako byste vytvořili generace 1. generace virtuálních počítačů.

### <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Generace 2 virtuální počítače můžete vytvořit také pomocí škálovací sady virtuálních počítačů. Můžete vytvářet generaci 2 virtuálních počítačů pomocí škálovacích sad virtuálních počítačů Azure pomocí Azure CLI.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* **Jsou generace 2 virtuální počítače k dispozici ve všech oblastech Azure?**  
    Ano. ale ne všechny [velikosti virtuálních počítačů 2. generace](#generation-2-vm-sizes) jsou k dispozici v každé oblasti. Dostupnost generace 2 virtuální počítače je závislý na dostupnosti velikost virtuálního počítače.

* **Je nějaký cena rozdíl mezi 1 a generace 2 virtuální počítače?**  
    Není žádný rozdíl v cenách mezi 1. generace a virtuální počítače generace 2.

* **Jak můžu zvýšit velikost disku operačního systému?**  
  Disky s operačním systémem větší než 2 TB se 2. generace virtuálních počítačů. Ve výchozím nastavení většina disků operačního systému jsou menší než 2 TB pro virtuální počítače generace 2, ale je možné zvýšit velikost disku na doporučenou maximálně 4 TB. Můžete zvýšit velikost disku operačního systému přes rozhraní příkazového řádku Azure nebo na webu Azure portal. Další informace o prostřednictvím kódu programu se zvětšující disky najdete v tématu [Změna velikosti disku](expand-os-disk.md).

  Chcete-li zvýšit velikost disku operačního systému pomocí webu Azure portal:

  * Přejděte na stránku vlastností virtuálního počítače na portálu Azure portal.

  * Vypnout a uvolnit pomocí virtuálních počítačů **Zastavit** tlačítko na webu Azure portal.

  * V **disky** vyberte disk s operačním systémem, který chcete zvýšit.

  * Vyberte **konfigurace** v **disky** oddílu a aktualizace **velikost** na požadovanou hodnotu.

  * Přejděte zpět na stránku vlastností virtuálního počítače a **Start** virtuálního počítače.
  
  Může se zobrazit upozornění pro disky s operačním systémem větší než 2 TB. Upozornění se nevztahuje na virtuální počítače generace 2; velikost disku operačního systému větší než 4 TB ale **nedoporučuje.**

* **Virtuální počítače generace 2 podporují Akcelerovanými síťovými službami?**  
    Ano, podpora virtuálních počítačů 2. generace [Akcelerovanými síťovými službami](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Vhdx vyslané generace 2?**  
    Ne, se podporuje jenom VHD na virtuálních počítačích 2. generace.

* **Virtuální počítače generace 2 podporují Ultra Solid stav jednotky SSD (Solid-State Drive)?**  
    Ano, virtuální počítače generace 2 podporují Ultra SSD.

* **Můžete migrovat z 1. generace do 2. generace virtuálních počítačů?**  
    Ne, nemůžete změnit generaci virtuálního počítače po jeho vytvoření. Pokud je potřeba přepnout mezi generací virtuálních počítačů, musíte vytvořit nový virtuální počítač z jiné generace.

## <a name="next-steps"></a>Další postup

* Další informace o [2. generace virtuálních počítačů Hyper-v](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Zjistěte, jak [Příprava virtuálního pevného disku](prepare-for-upload-vhd-image.md) nahrát z místního do Azure.
