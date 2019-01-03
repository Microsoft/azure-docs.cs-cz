---
title: Referenční architektury Azure vykreslování – Azure Batch
description: Architektury pro používání služby Azure Batch a dalšími službami Azure, rozšířit místní vykreslení farmy pomocí shlukování cloudu
services: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 08/13/2018
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: d5102ba94e2b7808a457df00a87b35ef7022c454
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543491"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referenční architektury pro vykreslování v Azure

Tento článek popisuje, diagramy architektury vysoké úrovně pro scénáře, které rozšiřují, nebo "sáhnout", místní vykreslení farmy do Azure. V příkladech jsou různé možnosti pro služby Azure compute, sítě a úložiště.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybridní řešení se systém souborů NFS nebo CFS

Následující diagram znázorňuje hybridní scénář, který zahrnuje následující služby Azure:

* **COMPUTE** -fondu Azure Batch nebo Škálovací sady virtuálních počítačů.

* **Síť** -On-premises: Azure ExpressRoute nebo VPN. Azure: Virtuální síť Azure.

* **Úložiště** – vstupní a výstupní soubory: Systém souborů NFS nebo používání virtuálních počítačů Azure a synchronizované s místní úložiště pomocí Azure File Sync nebo RSync CFS.

  ![Cloud bursting – hybridní řešení se systém souborů NFS nebo CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Hybridní řešení se Blobfuse

Následující diagram znázorňuje hybridní scénář, který zahrnuje následující služby Azure:

* **COMPUTE** -fondu Azure Batch nebo Škálovací sady virtuálních počítačů.

* **Síť** -On-premises: Azure ExpressRoute nebo VPN. Azure: Virtuální síť Azure.

* **Úložiště** – vstupní a výstupní soubory: Úložiště objektů BLOB, připojených k výpočetním prostředkům prostřednictvím Azure Blobfuse.

  ![Cloud bursting – hybridní řešení se Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybridní compute a storage

Následující diagram ukazuje plně propojené hybridní scénář pro výpočetní prostředky a úložiště a obsahuje následující služby Azure:

* **COMPUTE** -fondu Azure Batch nebo Škálovací sady virtuálních počítačů.

* **Síť** -On-premises: Azure ExpressRoute nebo VPN. Azure: Virtuální síť Azure.

* **Úložiště** – mezi různými místy: Avere vFXT. Volitelné archivaci místních souborů do úložiště objektů Blob pomocí Azure Data Box.

  ![Cloud bursting – hybridní compute a storage](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>Další postup

* Další informace o používání [vykreslení správci](batch-rendering-render-managers.md) pomocí služby Azure Batch.

* Další informace o možnostech [vykreslování v Azure](batch-rendering-service.md).