---
title: Řešení chyb při odstraňování prostředků úložiště na virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Řešení potíží při odstraňování prostředků úložiště s připojenými virtuálními pevnými disky.
keywords: ''
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 1de70b3ddea84fc0067a0e20ec613f01024f0ed4
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748030"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Řešení chyb při odstraňování prostředků úložiště

V některých scénářích, může dojít k jedné z následujících dojít k chybám, když se pokoušíte odstranit objektů blob v nasazení Azure Resource Manageru, kontejneru nebo účtu úložiště Azure:

>**Nepovedlo se odstranit účet úložiště "StorageAccountName". Chyba: Účet úložiště nejde odstranit, protože některé jeho artefakty používá.**

>**Nepovedlo se odstranit # mimo # kontejnerů:<br>virtuální pevné disky: v kontejneru je aktuálně zapůjčení a v požadavku bylo zadáno žádné ID zapůjčení.**

>**Nepovedlo se odstranit # mimo # objektů blob:<br>BlobName.vhd: u objektu blob je aktuálně zapůjčení a v požadavku bylo zadáno žádné ID zapůjčení.**

Virtuální pevné disky používané ve virtuálních počítačích Azure jsou soubory .vhd uložené jako objekty BLOB stránky v účtu úložiště úrovně standard nebo premium v Azure. Další informace o disků v Azure najdete v tématu [o nespravované a spravované diskové úložiště pro virtuální počítače Microsoft Azure Linux](../linux/about-disks-and-vhds.md). 

Azure zabraňuje odstranění disku, který je připojen k virtuálnímu počítači zabránit v poškození. Zabrání také odstranění kontejnerů a účty úložiště, které mají objekt blob stránky, který je připojen k virtuálnímu počítači. 

Proces můžete odstranit účet úložiště, kontejner nebo objekt blob, když je přijetím jedné z následujících chyb: 
1. [Identifikace objektů BLOB, které jsou připojené k virtuálnímu počítači](#step-1-identify-blobs-attached-to-a-vm)
2. [Odstranit virtuální počítače s připojené **disk s operačním systémem**](#step-2-delete-vm-to-detach-os-disk)
3. [Odpojit vše **datové disky** ze zbývajících virtuálních počítačů](#step-3-detach-data-disk-from-the-vm)

Opakovat pokus o odstranění účtu úložiště, kontejner nebo objekt blob po dokončení těchto kroků.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Krok 1: Identifikace objektů blob, které jsou připojené k virtuálnímu počítači

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scénář 1: Odstranění objektů blob – identifikovat připojených virtuálních počítačů
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **všechny prostředky**. Přejděte do účtu úložiště v části **služby Blob Service** vyberte **kontejnery**a přejděte na objekt blob k odstranění.
3. Pokud se objekt blob **stav zapůjčení** je **zapůjčený**, klikněte pravým tlačítkem a vyberte **upravit Metadata** a otevřete podokno metadata objektu Blob. 

    ![Snímek obrazovky portálu, s úložištěm objektů BLOB účtu a klikněte pravým tlačítkem myši klikněte na > "Upravit Metadata" zvýrazněnou](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. V podokně metadata objektu Blob, zkontrolujte a poznamenejte hodnotu **MicrosoftAzureCompute_VMName**. Tato hodnota je název, který virtuální pevný disk je připojený k virtuálnímu počítači. (Viz **důležité** Pokud toto pole neexistuje.)
5. V podokně metadata objektu Blob, zkontrolujte a zaznamenaná hodnota **MicrosoftAzureCompute_DiskType**. Tato hodnota určuje, zda je připojeném disku s operačním systémem nebo datový disk (viz **důležité** Pokud toto pole neexistuje). 

     ![Snímek obrazovky portálu s otevřeným podoknem "Metadata objektu Blob na" úložiště](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Pokud je typ objektu blob disku **OSDisk** postupujte podle [krok 2: odstranění virtuálního počítače se odpojit disk s operačním systémem](#step-2-delete-vm-to-detach-os-disk). Jinak, pokud je typ objektu blob disku **DataDisk** postupujte podle kroků v [krok 3: odpojení datového disku z virtuálního počítače](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Pokud **MicrosoftAzureCompute_VMName** a **MicrosoftAzureCompute_DiskType** se nezobrazí v metadata objektu blob, znamená to, že objekt blob je explicitně pronajatý a není připojen k virtuálnímu počítači. Pronajatých objekty BLOB není možné odstranit bez přerušení zapůjčení první. Přerušit zapůjčení, klikněte pravým tlačítkem na objekt blob a vyberte **přerušení zapůjčení**. Pronajatých objekty BLOB, které nejsou připojené k virtuálnímu počítači znemožňují, aby se objektu blob, ale nezabrání odstranění kontejneru nebo účtu úložiště.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scénář 2: Odstranění kontejneru – identifikovat všechny se objekty BLOB v kontejneru, které jsou připojené k virtuálním počítačům
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **všechny prostředky**. Přejděte do účtu úložiště v části **služby Blob Service** vyberte **kontejnery**a najít kontejner, která se má odstranit.
3. Kliknutím otevřete kontejner a zobrazí se seznam objektů BLOB dovnitř. Identifikujte všechny objekty BLOB s typem objektu Blob = **objektů blob stránky** a stav zapůjčení = **zapůjčený** z tohoto seznamu. Postupujte podle [scénář 1](#step-1-identify-blobs-attached-to-a-vm) pro identifikaci virtuálních počítačů spojené s každou z těchto objektů BLOB.

    ![Snímek obrazovky portálu se objektech blob na účtu úložiště a "Zapůjčení stav" s "Zapůjčený" zvýrazněnou](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Postupujte podle [kroku 2](#step-2-delete-vm-to-detach-os-disk) a [kroku 3](#step-3-detach-data-disk-from-the-vm) odstranit virtuální počítač s **OSDisk** a odpojit **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scénář 3: Odstranění úložiště účet - identifikaci všech objektů blob: {0} v rámci účtu úložiště, které jsou připojené k virtuálním počítačům
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **všechny prostředky**. Přejděte do účtu úložiště v části **služby Blob Service** vyberte **objekty BLOB**.
3. V **kontejnery** podokně identifikovat všechny kontejnery kde **zapůjčení** je **zapůjčený** a postupujte podle pokynů [scénář 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) pro každou  **Zapůjčení** kontejneru.
4. Postupujte podle [kroku 2](#step-2-delete-vm-to-detach-os-disk) a [kroku 3](#step-3-detach-data-disk-from-the-vm) odstranit virtuální počítač s **OSDisk** a odpojit **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Krok 2: Odstranění virtuálního počítače se odpojit disk s operačním systémem
Pokud virtuální pevný disk je disk s operačním systémem, musíte odstranit virtuální počítač před odstraněním připojený virtuální pevný disk. Nemusíte dělat nic dalšího se bude vyžadovat pro datové disky připojené k stejný virtuální počítač po dokončení těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **virtuálních počítačů**.
3. Vyberte virtuální pevný disk připojený k virtuálnímu počítači.
4. Ujistěte se, že nic aktivně používá virtuální počítač a virtuální počítač už nepotřebujete.
5. V horní části **podrobnosti virtuálního počítače** vyberte **odstranit**a potom klikněte na tlačítko **Ano** potvrďte.
6. Měl by být odstraněn virtuální počítač, ale můžou uchovávat virtuální pevný disk. Však virtuální pevný disk by měl již připojen k virtuálnímu počítači nebo mít zapůjčení na něj. Může trvat několik minut, než zapůjčení se uvolní. Pokud chcete ověřit, že zapůjčení se uvolní, přejděte do umístění objektu blob a v **vlastnosti objektu Blob** podokně **stav zapůjčení** by měl být **dostupné**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Krok 3: Odpojení datového disku z virtuálního počítače
Pokud virtuální pevný disk datového disku, odpojte virtuální pevný disk z virtuálního počítače Toto označení odebrat:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **virtuálních počítačů**.
3. Vyberte virtuální pevný disk připojený k virtuálnímu počítači.
4. Vyberte **disky** na **podrobnosti virtuálního počítače** podokně.
5. Vyberte datový disk má být odstraněn virtuální pevný disk je připojený k. Můžete určit, která objekt blob je připojen na disku tak, že zkontrolujete adresu URL virtuálního pevného disku.
6. Umístění objektu blob můžete ověřit kliknutím na disku se změnami cestu **URI virtuálního pevného disku** pole.
7. Vyberte **upravit** v horní **disky** podokně.
8. Klikněte na tlačítko **odpojit ikonu** z datového disku, která se má odstranit.

     ![Snímek obrazovky portálu s otevřeným podoknem "Metadata objektu Blob na" úložiště](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Vyberte **Uložit**. Disk teď odpojen od virtuálního počítače a virtuálního pevného disku je již zapůjčení. Může trvat několik minut, než zapůjčení se uvolní. K ověření, že zapůjčení se uvolní, přejděte do umístění objektu blob a v **vlastnosti objektu Blob** podokně **stav zapůjčení** hodnota by měla být **Odemknutý** nebo **Dostupné**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

