---
title: Řešení chyb odstranění prostředků úložiště v Azure Resource Manager nasazení na virtuální počítače s Linuxem | Microsoft Docs
description: Řešení potíží při odstraňování prostředků úložiště obsahující připojit virtuální pevné disky.
keywords: SSH připojení odmítnuto, ssh chyby, azure ssh, připojení SSH se nezdařilo
services: virtual-machines-linux
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 823c85f620dcd7c96d0005d39b1a12a7a9fba938
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363821"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Řešení chyb odstranění prostředků úložiště

V některých případech se můžete setkat jednu z následujících dojít k chybám, když se pokoušíte odstranit účet úložiště Azure, kontejneru nebo objektů blob v nasazení Azure Resource Manager:

>**Odstranění účtu úložiště 'StorageAccountName' se nezdařilo. Chyba: Účet úložiště nelze odstranit, protože některé jeho artefakty se používají.**

>**Nepodařilo se odstranit # mimo kontejnery #:<br>virtuální pevné disky: v současné době není zapůjčení na kontejneru a žádné ID zapůjčení zadaná v žádosti.**

>**Nepodařilo se odstranit # mimo # objekty BLOB:<br>BlobName.vhd: u objektu blob je momentálně zapůjčení a žádné ID zapůjčení zadaná v žádosti.**

Použité ve virtuálních počítačích Azure virtuální pevné disky jsou soubory VHD uložené jako objekty BLOB stránky v účtu úložiště standard nebo premium v Azure. Další informace o Azure disky najdete v tématu [o nespravované a spravované úložiště disku pro virtuální počítače Microsoft Azure Linux](about-disks-and-vhds.md). 

Azure zabraňuje odstranění disku, který je připojen k virtuálnímu počítači zabránit poškození. Rovněž zamezí odstranění kontejnerů a účty úložiště, které mají objekt blob stránky, který je připojen k virtuálnímu počítači. 

Je proces odstranit účet úložiště, kontejner nebo objekt blob při přijímání některé z těchto chyb: 
1. [Identifikovat objekty BLOB, které jsou připojené k virtuálnímu počítači](#step-1-identify-blobs-attached-to-a-vm)
2. [Připojené odstranění virtuálních počítačů s **disk operačního systému**](#step-2-delete-vm-to-detach-os-disk)
3. [Odpojte všechny **datové disky** z zbývající virtuální počítače](#step-3-detach-data-disk-from-the-vm)

Opakujte pokus o odstranění účtu úložiště, kontejner nebo objekt blob po dokončení těchto kroků.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Krok 1: Identifikace objektů blob, které jsou připojené k virtuálnímu počítači

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scénář 1: Odstranění objektu blob – identifikovat připojených virtuálních počítačů
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **všechny prostředky**. Přejděte na účet úložiště, v části **služby objektů Blob** vyberte **kontejnery**a přejděte do objektu blob se odstranit.
3. Pokud objekt blob **stavu zapůjčení** je **pronajatých**, klikněte pravým tlačítkem a vyberte **upravit Metadata** otevřete podokno metadata objektu Blob. 

    ![Snímek obrazovky portálu, s úložištěm objektů BLOB účtu a klikněte pravým tlačítkem na > "Upravit Metadata" zvýrazněná](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. V podokně metadata objektu Blob, zkontrolujte a hodnoty pro tuto hodnotu zaznamenejte **MicrosoftAzureCompute_VMName**. Tato hodnota je název virtuálního počítače, virtuální pevný disk připojený k. (Viz **důležité** Pokud toto pole neexistuje.)
5. V podokně metadata objektu Blob, zkontrolujte a zaznamenejte hodnotu **MicrosoftAzureCompute_DiskType**. Tato hodnota označuje, jestli je připojený disk operačního systému nebo data (viz **důležité** Pokud toto pole neexistuje). 

     ![Snímek obrazovky portálu open podokně "Metadata objektu Blob" úložiště](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Pokud je typ objektu blob disku **OSDisk** podle [krok 2: odstranění virtuálního počítače odpojit disk s operačním systémem](#step-2-delete-vm-to-detach-os-disk). Jinak, pokud je typ objektu blob disku **DataDisk** postupujte podle kroků v [krok 3: odpojit datový disk z virtuálního počítače](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Pokud **MicrosoftAzureCompute_VMName** a **MicrosoftAzureCompute_DiskType** se nezobrazí v metadata objektu blob, označuje, že objektu blob je explicitně pronajatý a není připojen k virtuálnímu počítači. Zapůjčených objekty BLOB nelze odstranit, aniž by vás první zapůjčení. Rozdělit zapůjčení, klikněte pravým tlačítkem na objekt blob a vyberte **zalomení zapůjčení**. Zapůjčených objekty BLOB, které nejsou připojené k virtuálnímu počítači zabránit odstranění objektu blob, ale nebudou bránit odstranění účtu kontejneru nebo úložiště.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scénář 2: Odstranění kontejner - identifikovat všechny blob(s) v rámci kontejneru, které jsou připojené k virtuálním počítačům
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **všechny prostředky**. Přejděte na účet úložiště, v části **služby objektů Blob** vyberte **kontejnery**a najít kontejner, aby ji odstranit.
3. Kliknutím otevřete kontejner a zobrazí se seznam objektů blob je uvnitř. Identifikujte všechny objekty BLOB s typem objektu Blob = **objekt blob stránky** a stavu zapůjčení = **pronajatých** z tohoto seznamu. Postupujte podle [scénáři 1](#step-1-identify-blobs-attached-to-a-vm) k identifikaci virtuálních počítačů spojené s každou z těchto objektů BLOB.

    ![Snímek obrazovky portálu s objekty BLOB účtu úložiště a "Zapůjčení stav" s "Pronajatých" zvýrazněná](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Postupujte podle [kroku 2](#step-2-delete-vm-to-detach-os-disk) a [krok 3](#step-3-detach-data-disk-from-the-vm) odstranit virtuální počítače s **OSDisk** a odpojit **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scénář 3: Odstranění úložiště účet – identifikace blob(s) v rámci účtu úložiště, které jsou připojené k virtuálním počítačům
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **všechny prostředky**. Přejděte na účet úložiště, v části **služby objektů Blob** vyberte **kontejnery**.

    ![Snímek obrazovky portálu s kontejnery účtu úložiště a "Zapůjčení stavu" s "Pronajatých" zvýrazněná](./media/troubleshoot-vhds/utd-containers-sm.png)

3. V **kontejnery** podokně identifikovat všechny kontejnery kde **stavu zapůjčení** je **pronajatých** a postupujte podle pokynů [scénáři 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) pro každou  **Pronajaté** kontejneru.
4. Postupujte podle [kroku 2](#step-2-delete-vm-to-detach-os-disk) a [krok 3](#step-3-detach-data-disk-from-the-vm) odstranit virtuální počítače s **OSDisk** a odpojit **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Krok 2: Odstranění virtuálního počítače odpojit disk operačního systému
Pokud virtuální pevný disk, disk operačního systému, je nutné odstranit virtuální počítač, než mohl být odstraněn připojený soubor VHD. Žádná další akce nutné pro datové disky připojené na stejný virtuální počítač po dokončení těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **virtuální počítače**.
3. Vyberte virtuální počítač, virtuální pevný disk připojený k.
4. Ujistěte se, že nic aktivně používá virtuální počítač a virtuální počítač už nepotřebujete.
5. V horní části **podrobnosti virtuálního počítače** podokně, vyberte **odstranit**a potom klikněte na **Ano** k potvrzení.
6. Virtuální počítač by měl být odstraněn, ale uchovávání může být virtuální pevný disk. Virtuální pevný disk však by už připojený k virtuálnímu počítači nebo mít zapůjčení na jeho. To může trvat několik minut, než zapůjčení k uvolnění. Pokud chcete ověřit, že zapůjčení vydání, přejděte do umístění objektu blob a v **Blob vlastnosti** podokně **zapůjčení stav** by měla být **dostupné**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Krok 3: Odpojte datový disk z virtuálního počítače
Pokud virtuální pevný disk je datový disk, odpojte virtuální pevný disk z virtuálního počítače odebrat zapůjčení:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **virtuální počítače**.
3. Vyberte virtuální počítač, virtuální pevný disk připojený k.
4. Vyberte **disky** na **podrobnosti virtuálního počítače** podokně.
5. Vyberte datový disk k odstranění virtuální pevný disk je připojen k. Můžete určit, které objekt blob je připojen na disku kontrolou adresu URL virtuálního pevného disku.
6. Umístění objektu blob můžete ověřit kliknutím na disk, který Zkontrolujte cestu **URI virtuálního pevného disku** pole.
7. Vyberte **upravit** na **disky** podokně.
8. Klikněte na tlačítko **odpojit ikonu** z datový disk k odstranění.

     ![Snímek obrazovky portálu open podokně "Metadata objektu Blob" úložiště](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Vyberte **Uložit**. Disk je teď odpojený od virtuálního počítače, a už je pronajatý virtuální pevný disk. To může trvat několik minut, než zapůjčení k uvolnění. Pokud chcete ověřit, že byla vydána zapůjčení, přejděte do umístění objektu blob a v **Blob vlastnosti** podokně **zapůjčení stav** hodnota by měla být **Odemknutý** nebo **k dispozici**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

