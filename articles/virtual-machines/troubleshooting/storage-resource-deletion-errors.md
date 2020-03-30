---
title: Poradce při potížích s chybami odstranění prostředků úložiště na virtuálních počítačích SIP v Azure | Dokumenty společnosti Microsoft
description: Jak řešit problémy při odstraňování prostředků úložiště obsahujících připojené virtuální počítače.
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 50ab4b0f1e676ffcba0ce69ab6aa957e4c77ab88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058159"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Poradce při potížích s chybami odstranění prostředků úložiště

V některých případech může dojít k jedné z následujících chyb při pokusu o odstranění účtu úložiště Azure, kontejneru nebo objektu blob v nasazení Azure Resource Manager:

> **Odstranění účtu úložiště "StorageAccountName" se nezdařilo. Chyba: Účet úložiště nelze odstranit z důvodu jeho artefakty jsou používány.**
> 
> **Nepodařilo se odstranit # out<br>of # kontejner (y): vhds: Aktuálně je zapůjčení na kontejneru a žádné ID zapůjčení bylo zadáno v požadavku.**
> 
> **Nepodařilo se odstranit # out<br>of # objekty BLOB: BlobName.vhd: Aktuálně existuje zapůjčení objektu blob a v požadavku nebylo zadáno žádné ID zapůjčení.**

Virtuální pevné disky používané ve virtuálních počítačích Azure jsou .vhd soubory uložené jako objekty BLOB stránky ve standardním nebo prémiovém účtu úložiště v Azure. Další informace o discích Azure najdete v našem [úvodu ke spravovaným diskům](../linux/managed-disks-overview.md).

Azure zabraňuje odstranění disku, který je připojen k virtuálnímu počítači, aby se zabránilo poškození. Také zabraňuje odstranění kontejnerů a účtů úložiště, které mají objekt blob stránky, který je připojený k virtuálnímu virtuálnímu počítačům. 

Proces odstranění účtu úložiště, kontejneru nebo objektu blob při příjmu jedné z těchto chyb je: 
1. Identifikace objektů BLOB připojených k virtuálnímu virtuálnímu mísu
2. [Odstranění virtuálních počítačů s připojeným **diskem operačního systému**](#step-2-delete-vm-to-detach-os-disk)
3. [Odpojení všech **datových disků** od zbývajících virtuálních počítačů](#step-3-detach-data-disk-from-the-vm)

Opakujte odstranění účtu úložiště, kontejneru nebo objektu blob po dokončení těchto kroků.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Krok 1: Identifikace objektu blob připojeného k virtuálnímu virtuálnímu mísu

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scénář 1: Odstranění objektu blob – identifikace připojeného virtuálního počítače
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce centra vyberte **Všechny prostředky**. Přejděte na účet úložiště, v části **Služba objektů blob vyberte** **kontejnery**a přejděte na objekt blob, který chcete odstranit.
3. Pokud **je** **stav zapůjčení objektu** blob , klepněte pravým tlačítkem myši a vyberte **upravit metadata** a otevřete podokno metadat objektů Blob. 

    ![Snímek obrazovky portálu se zvýrazněnými objekty BLOB účtu úložiště a pravým tlačítkem myši > "Upravit metadata"](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. V podokně metadat objektů Blob zkontrolujte a zaznamenejte hodnotu **MicrosoftAzureCompute_VMName**. Tato hodnota je název virtuálního virtuálního serveru, ke kterému je virtuální pevný disk připojen. (Viz **důležité,** pokud toto pole neexistuje)
5. V podokně metadat objektů Blob zkontrolujte a zaznamenejte hodnotu **MicrosoftAzureCompute_DiskType**. Tato hodnota určuje, zda je připojený disk os nebo datový disk (Viz **důležité,** pokud toto pole neexistuje). 

     ![Snímek obrazovky portálu s otevřeným podoknem Úložiště metadat objektů blob](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Pokud je typ disku objektu blob **OSDisk,** postupujte [podle kroku 2: Odstranění virtuálního počítače a odpojení disku operačního systému](#step-2-delete-vm-to-detach-os-disk). V opačném případě pokud je typ disku objektu blob **DataDisk** postupujte podle kroků v [kroku 3: Odpojit datový disk od virtuálního počítače](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Pokud **MicrosoftAzureCompute_VMName** a **MicrosoftAzureCompute_DiskType** nezobrazí v metadatech objektu blob, znamená to, že objekt blob je explicitně zapůjčena a není připojen k virtuálnímu virtuálnímu soudu. Zapůjčené objekty BLOB nelze odstranit, aniž by byla nejprve rozdělena zapůjčení. Chcete-li přerušit zapůjčení, klikněte pravým tlačítkem myši na objekt blob a vyberte **možnost Přerušit zapůjčení**. Pronajaté objekty BLOB, které nejsou připojeny k virtuálnímu virtuálnímu připojení zabránit odstranění objektu blob, ale nebrání odstranění kontejneru nebo účtu úložiště.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scénář 2: Odstranění kontejneru – identifikujte všechny objekty blob v kontejneru, které jsou připojené k virtuálním počítačem
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce centra vyberte **Všechny prostředky**. Přejděte na účet úložiště v části **Služba objektů blob vyberte** **kontejnery**a najděte kontejner, který má být odstraněn.
3. Klepnutím otevřete kontejner a zobrazí se seznam objektů BLOB uvnitř. Identifikujte všechny objekty BLOB s objektem BLOB Type = **Objekt blob stránky** a Stav zapůjčení **z** tohoto seznamu. Postupujte podle scénáře 1 k identifikaci virtuálního počítače přidruženého ke každému z těchto objektů BLOB.

    ![Snímek obrazovky portálu s objekty BLOB účtu úložiště a "Stav zapůjčení" se zvýrazněným "Pronajaté"](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Odstranění virtuálních počítačů pomocí **nástroje OSDisk** a odpojení **datadisku**pomocí kroku 2 a 3 postupujte podle [kroků 2](#step-2-delete-vm-to-detach-os-disk) a [3.](#step-3-detach-data-disk-from-the-vm) 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scénář 3: Odstranění účtu úložiště – identifikujte všechny objekty blob v rámci účtu úložiště, které jsou připojené k virtuálním počítačem
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce centra vyberte **Všechny prostředky**. Přejděte na účet úložiště v části **Služba objektů blob** vyberte **Objekty blob**.
3. V **podokně Kontejnery** identifikujte všechny kontejnery, kde je **pronajatý stav zapůjčený,** a postupujte podle [scénáře 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) pro každý **zapůjčený** kontejner. **Lease State**
4. Odstranění virtuálních počítačů pomocí **nástroje OSDisk** a odpojení **datadisku**pomocí kroku 2 a 3 postupujte podle [kroků 2](#step-2-delete-vm-to-detach-os-disk) a [3.](#step-3-detach-data-disk-from-the-vm) 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Krok 2: Odstranění virtuálního počítače k odpojení disku operačního systému
Pokud je virtuální pevný disk disk operačního systému, musíte virtuální ho odstranit, aby bylo možné odstranit připojený virtuální pevný disk. Žádná další akce bude vyžadována pro datové disky připojené ke stejnému virtuálnímu počítače po dokončení těchto kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce Hub vyberte **Virtuální počítače**.
3. Vyberte virtuální ms, ke kterému je virtuální pevný disk připojený.
4. Ujistěte se, že nic není aktivně používat virtuální počítač a že už nepotřebujete virtuální počítač.
5. V horní části podokna **podrobností virtuálního počítače** vyberte **Odstranit**a potvrďte to klepnutím na **tlačítko Ano.**
6. Virtuální ho virtuálního mísu by měl být odstraněn, ale virtuální pevný disk lze zachovat. Virtuální pevný disk by však již neměl být připojen k virtuálnímu počítači nebo na něm mít zapůjčení. Uvolnění zapůjčení může trvat několik minut. Chcete-li ověřit, zda je zapůjčení uvolněno, přejděte do umístění objektu blob a v podokně **vlastností objektu Blob,** **stav zapůjčení** by měl být **k dispozici**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Krok 3: Odpojení datového disku od virtuálního počítače
Pokud je virtuální pevný disk datový disk, odpojte virtuální disk od virtuálního počítače a odeberte zapůjčení:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce Hub vyberte **Virtuální počítače**.
3. Vyberte virtuální ms, ke kterému je virtuální pevný disk připojený.
4. V podokně **podrobností virtuálního počítače** vyberte **Disky.**
5. Vyberte datový disk, ke kterému má být virtuální pevný disk připojen. Objekt blob, který je připojen k disku, můžete určit kontrolou adresy URL virtuálního pevného disku.
6. Umístění objektu blob můžete ověřit kliknutím na disk a zkontrolovat cestu v poli **IDENTIFIKÁTOR URI virtuálního pevného disku.**
7. V horní části podokna **Disky** vyberte **Upravit.**
8. Klepněte na **ikonu odpojení** datového disku, který má být odstraněn.

     ![Snímek obrazovky portálu s otevřeným podoknem Úložiště metadat objektů blob](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Vyberte **Uložit**. Disk je teď odpojen od virtuálního počítače a virtuální pevný disk už není pronajatý. Uvolnění zapůjčení může trvat několik minut. Chcete-li ověřit, zda byla zapůjčení uvolněna, přejděte do umístění objektu blob a v podokně **vlastností objektu Blob,** hodnota **Stav zapůjčení** by měla být **odemčena** nebo **k dispozici**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

