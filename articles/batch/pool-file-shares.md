---
title: Sdílená složka Azure pro fondy Azure Batch
description: Jak připojit sdílenou složku služby soubory Azure z výpočetních uzlů ve fondu Linux nebo Windows ve službě Azure Batch.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: e5682e7ba853973592c3a650a06ce72615cec7b6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735490"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Použití sdílené složky Azure s fondem služby Batch

[Soubory Azure](../storage/files/storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím protokolu SMB (Server Message Block). Tento článek poskytuje informace a příklady kódu pro připojení a používání sdílené složky Azure ve fondech výpočetních uzlů.

## <a name="considerations-for-use-with-batch"></a>Pokyny pro použití s Batch

* Pokud máte fondy, které spouštějí relativně nízký počet paralelních úloh, pokud používáte soubory Azure, které nejsou Premium, zvažte použití sdílené složky Azure. Zkontrolujte [cíle výkonu a škálování](../storage/files/storage-files-scale-targets.md) , abyste zjistili, jestli by se měly používat soubory Azure (které používají účet Azure Storage) s ohledem na očekávanou velikost fondu a počet souborů prostředků. 

* Sdílené složky Azure jsou [nákladově efektivní](https://azure.microsoft.com/pricing/details/storage/files/) a je možné je nakonfigurovat pomocí replikace dat do jiné oblasti, takže jsou globálně redundantní. 

* Sdílenou složku Azure můžete připojit souběžně z místního počítače. Ujistěte se však, že rozumíte [dopadům souběžnosti](../storage/blobs/concurrency-manage.md) , zejména při použití rozhraní REST API.

* Viz také obecné [požadavky plánování](../storage/files/storage-files-planning.md) pro sdílené složky Azure.


## <a name="create-a-file-share"></a>Vytvoření sdílené složky

[Vytvořte sdílenou složku](../storage/files/storage-how-to-create-file-share.md) v účtu úložiště, který je propojený s vaším účtem Batch, nebo v samostatném účtu úložiště.

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Připojení sdílené složky Azure ve fondu služby Batch

Informace o tom, jak [připojit virtuální systém souborů ve fondu Batch](virtual-file-mount.md), najdete v dokumentaci.

## <a name="next-steps"></a>Další kroky

* Další možnosti čtení a zápisu dat do služby Batch najdete v tématu [zachování úlohy a výstupu úlohy](batch-task-output.md).
* Viz také sada [Batch pro dávkové](https://github.com/Azure/batch-shipyard) zpracování, která zahrnuje [recepty v loděnicích](https://github.com/Azure/batch-shipyard/tree/master/recipes) pro nasazení systémů souborů pro úlohy kontejnerů služby Batch.