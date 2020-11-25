---
title: Řešení chyb při odstraňování prostředků úložiště u virtuálních počítačů se systémem Linux v Azure | Microsoft Docs
description: Naučte se řešit problémy s virtuálními počítači se systémem Linux při odstraňování prostředků úložiště, které obsahují připojené virtuální pevné disky.
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
ms.openlocfilehash: 8d727bc8bdc8f015504baa57f9596b3bacac9712
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022866"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Řešení potíží s chybami odstraňování prostředků úložiště

V některých scénářích se může při pokusu o odstranění účtu, kontejneru nebo objektu BLOB služby Azure Storage v nasazení Azure Resource Manager narazit k jedné z následujících chyb:

> **Nepovedlo se odstranit účet úložiště ' StorageAccountName '. Chyba: účet úložiště nejde odstranit, protože se jeho artefakty používají.**
> 
> **Nepovedlo se odstranit počet kontejnerů # mimo #: <br> VHD: v tomto kontejneru je momentálně zapůjčení a v požadavku není zadané žádné ID zapůjčení.**
> 
> **Nepovedlo se odstranit # z # objektů BLOB: <br> BLOB. VHD: v tomto objektu BLOB je momentálně zapůjčení a v žádosti se nezadalo žádné ID zapůjčení.**

Virtuální pevné disky používané ve virtuálních počítačích Azure jsou soubory. VHD uložené jako objekty blob stránky v účtu úložiště Standard nebo Premium v Azure. Další informace o discích Azure najdete v našem [úvodu ke spravovaným diskům](../managed-disks-overview.md).

Azure zabraňuje odstranění disku připojeného k virtuálnímu počítači, aby nedocházelo k poškození. Zabrání taky odstranění kontejnerů a účtů úložiště, které mají objekt blob stránky, který je připojený k virtuálnímu počítači. 

Proces odstranění účtu úložiště, kontejneru nebo objektu BLOB při příjmu jedné z těchto chyb: 
1. Identifikace objektů BLOB připojených k virtuálnímu počítači
2. [Odstranění virtuálních počítačů s připojeným **diskem s operačním systémem**](#step-2-delete-vm-to-detach-os-disk)
3. [Odpojit všechny **datové disky** od zbývajících virtuálních počítačů](#step-3-detach-data-disk-from-the-vm)

Po dokončení těchto kroků zkuste znovu odstranit účet úložiště, kontejner nebo objekt BLOB.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Krok 1: Identifikace objektu BLOB připojeného k virtuálnímu počítači

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scénář 1: odstranění objektu BLOB – identifikace připojeného virtuálního počítače
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **Všechny prostředky**. Přejděte do účtu úložiště, v části **BLOB Service** vyberte **kontejnery** a přejděte k objektu blob, který chcete odstranit.
3. Pokud je **stav zapůjčení** objektu BLOB **zapůjčení**, klikněte pravým tlačítkem a vyberte **Upravit metadata** a otevřete tak podokno metadata objektu BLOB. 

    ![Snímek obrazovky portálu s objekty blob účtu úložiště a pravým tlačítkem myši > zvýrazněná možnost Upravit metadata](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. V podokně metadata objektu BLOB vyhledejte a zaznamenejte hodnotu pro **MicrosoftAzureCompute_VMName**. Tato hodnota je název virtuálního počítače, ke kterému je připojený virtuální pevný disk. (Viz **důležité** , pokud toto pole neexistuje)
5. V podokně metadata objektu BLOB vyhledejte a zaznamenejte hodnotu **MicrosoftAzureCompute_DiskType**. Tato hodnota označuje, zda se jedná o disk s operačním systémem nebo datovým diskem (viz **důležité** , pokud toto pole neexistuje). 

     ![Snímek obrazovky portálu s otevřeným panelem metadata úložiště objektů BLOB](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Pokud je typ disku objektu BLOB **OSDisk** podle [kroku 2: odstranění virtuálního počítače pro odpojení disku s operačním systémem](#step-2-delete-vm-to-detach-os-disk). V opačném případě, pokud je typ disku objektu BLOB datovým **diskem** , postupujte podle kroků v části [Krok 3: odpojení datového disku od virtuálního počítače](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Pokud se **MicrosoftAzureCompute_VMName** a **MicrosoftAzureCompute_DiskType** se v metadatech objektů BLOB nezobrazí, znamená to, že je objekt BLOB explicitně zapůjčení a není připojený k virtuálnímu počítači. Pronajaté objekty BLOB se nedají odstranit, aniž byste nejdřív přerušili zapůjčení. Pokud chcete přerušit zapůjčení, klikněte pravým tlačítkem na objekt BLOB a vyberte **Zrušit zapůjčení**. Pronajaté objekty blob, které nejsou připojené k virtuálnímu počítači, zabraňují odstranění objektu blob, ale nebrání odstranění kontejneru nebo účtu úložiště.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scénář 2: odstranění kontejneru – identifikace všech objektů BLOB v kontejneru, které jsou připojené k virtuálním počítačům
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **Všechny prostředky**. Do účtu úložiště vyberte v části **BLOB Service** možnost **kontejnery** a najděte kontejner, který chcete odstranit.
3. Kliknutím otevřete kontejner a zobrazí se seznam objektů blob, které se v něm nacházejí. Identifikujte všechny objekty BLOB s typem objektu BLOB = **objekt blob stránky** a stav zapůjčení = **zapůjčené** z tohoto seznamu. Použijte scénář 1 a Identifikujte virtuální počítač spojený s každým z těchto objektů BLOB.

    ![Snímek obrazovky portálu s objekty blob účtu úložiště a "stav zapůjčení" se zvýrazněným "zapůjčeným"](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Postupujte podle [kroků 2](#step-2-delete-vm-to-detach-os-disk) a [3](#step-3-detach-data-disk-from-the-vm) pro odstranění virtuálních počítačů pomocí **OSDisk** a odpojení **datadisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scénář 3: odstranění účtu úložiště – identifikace všech objektů BLOB v rámci účtu úložiště, které jsou připojené k virtuálním počítačům
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **Všechny prostředky**. Do účtu úložiště vyberte v části **BLOB Service** možnost **objekty blob**.
3. V podokně **kontejnery** identifikujte všechny kontejnery, ve kterých je zapůjčený **stav** **zapůjčení** , a v případě každého **pronajatého** kontejneru použijte [scénář 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) .
4. Postupujte podle [kroků 2](#step-2-delete-vm-to-detach-os-disk) a [3](#step-3-detach-data-disk-from-the-vm) pro odstranění virtuálních počítačů pomocí **OSDisk** a odpojení **datadisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Krok 2: odstranění virtuálního počítače pro odpojení disku s operačním systémem
Pokud se jedná o disk s operačním systémem, musíte virtuální počítač odstranit, aby se mohl odstranit připojený virtuální pevný disk (VHD). Po dokončení těchto kroků nebude potřeba žádná další akce pro datové disky připojené ke stejnému virtuálnímu počítači:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **Virtual Machines**.
3. Vyberte virtuální počítač, ke kterému je připojený virtuální pevný disk.
4. Ujistěte se, že virtuální počítač aktivně nepoužíváte a že ho už nepotřebujete.
5. V horní části podokna **podrobností o virtuálním počítači** vyberte **Odstranit** a potvrďte to kliknutím na **Ano** .
6. Virtuální počítač by se měl odstranit, ale virtuální pevný disk se dá zachovat. Virtuální pevný disk se ale už nesmí připojit k virtuálnímu počítači nebo mít zapůjčení. Vydání zapůjčení může trvat několik minut. Chcete-li ověřit, zda je zapůjčení uvolněno, přejděte do umístění objektu BLOB a v podokně **vlastnosti objektu BLOB** , měl by být **dostupný** **stav zapůjčení** .

## <a name="step-3-detach-data-disk-from-the-vm"></a>Krok 3: odpojení datového disku od virtuálního počítače
Pokud je virtuální pevný disk datového disku, odpojte virtuální pevný disk od virtuálního počítače a odeberte zapůjčení:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **Virtual Machines**.
3. Vyberte virtuální počítač, ke kterému je připojený virtuální pevný disk.
4. V podokně **podrobností virtuálního počítače** vyberte **disky** .
5. Vyberte datový disk, ke kterému se má odstranit virtuální pevný disk, ke kterému se má připojit. To, který objekt BLOB je připojený na disku, můžete zjistit kontrolou adresy URL virtuálního pevného disku.
6. Umístění objektu blob můžete ověřit tak, že kliknete na disk a zkontrolujete cestu v poli **identifikátor URI virtuálního pevného disku** .
7. V podokně horní části **disku** vyberte **Upravit** .
8. Klikněte na **Odpojit ikona** datového disku, který se má odstranit.

     ![Snímek obrazovky portálu s otevřeným oknem úložiště BLOB metadata a zvýrazní ikonu odpojení datového disku, který se má odstranit.](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Vyberte **Uložit**. Disk je teď odpojený od virtuálního počítače a virtuální pevný disk už není zapůjčení. Vydání zapůjčení může trvat několik minut. Chcete-li ověřit, zda byla zapůjčení uvolněna, přejděte do umístění objektu BLOB a v podokně **vlastností objektu BLOB** , hodnota **stavu zapůjčení** by měla být **odemčena** nebo **k dispozici**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

