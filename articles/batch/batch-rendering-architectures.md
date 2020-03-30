---
title: Referenční architektury Azure – Azure Batch
description: Architektury pro použití Azure Batch a dalších služeb Azure k rozšíření místní vykreslovací farmy prasknutím do cloudu
services: batch
ms.service: batch
author: davefellows
manager: evansma
ms.author: labrenne
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: 20442a6618ca9357bb3be95879b68bffca45a40d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022949"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referenční architektury pro vykreslování Azure

Tento článek ukazuje diagramy architektury vysoké úrovně pro scénáře rozšířit nebo "burst", místní vykreslovací farmy do Azure. Příklady ukazují různé možnosti pro výpočetní, síťové a úložné služby Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybridní s NFS nebo CFS

Následující diagram znázorňuje hybridní scénář, který zahrnuje následující služby Azure:

* **Výpočetní –** fond dávek Azure nebo škálovací sada virtuálních strojů.

* **Síť** – místní: Azure ExpressRoute nebo VPN. Azure: Virtuální síť Azure.

* **Úložiště** – vstupní a výstupní soubory: NFS nebo CFS pomocí virtuálních počítačích Azure, synchronizované s místním úložištěm přes Azure File Sync nebo RSync. Alternativně: Avere vFXT pro vstup nebo výstup souborů z místních zařízení NAS pomocí systému souborů NFS.

  ![Prasknutí cloudu - Hybridní s NFS nebo CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybridní s Blobfuse

Následující diagram znázorňuje hybridní scénář, který zahrnuje následující služby Azure:

* **Výpočetní –** fond dávek Azure nebo škálovací sada virtuálních strojů.

* **Síť** – místní: Azure ExpressRoute nebo VPN. Azure: Virtuální síť Azure.

* **Úložiště** – vstupní a výstupní soubory: Úložiště objektů blob, připojené k výpočetním prostředkům přes Azure Blobfuse.

  ![Prasknutí mraků - Hybrid s Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybridní výpočetní výkon a úložiště

Následující diagram znázorňuje plně připojený hybridní scénář pro výpočetní prostředky i úložiště a zahrnuje následující služby Azure:

* **Výpočetní –** fond dávek Azure nebo škálovací sada virtuálních strojů.

* **Síť** – místní: Azure ExpressRoute nebo VPN. Azure: Virtuální síť Azure.

* **Skladování** - Meziprostory: Avere vFXT. Volitelná archivace místních souborů přes Azure Data Box do úložiště objektů Blob nebo místní Avere FXT pro akceleraci NAS.

  ![Prasknutí cloudu – hybridní výpočetní výkon a úložiště](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o používání [správců vykreslení](batch-rendering-render-managers.md) s Azure Batch.

* Další informace o možnostech [vykreslování v Azure](batch-rendering-service.md).