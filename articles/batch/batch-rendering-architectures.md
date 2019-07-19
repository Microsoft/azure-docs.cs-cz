---
title: Referenční architektury vykreslování Azure – Azure Batch
description: Architektury pro používání Azure Batch a dalších služeb Azure k rozšíření místní farmy vykreslování s využitím shlukování do cloudu
services: batch
ms.service: batch
author: davefellows
manager: gwallace
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: fa2d59b2a6d2dea72276ab38a5cb1ca7bfb579a4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323112"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referenční architektury pro vykreslování Azure

Tento článek ukazuje diagramy architektury vysoké úrovně pro scénáře pro rozšíření nebo "shluky", místní farmu vykreslování do Azure. V příkladech se zobrazují různé možnosti pro výpočetní, síťové a úložné služby Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrid se systémem souborů NFS nebo CFS

Následující diagram znázorňuje hybridní scénář, který obsahuje následující služby Azure:

* **COMPUTE** -Azure Batch fond nebo sada škálování virtuálních počítačů.

* Místní **síť** : Azure ExpressRoute nebo VPN. Azure: Virtuální síť Azure.

* Vstupní a výstupní soubory **úložiště** : NFS nebo CFS pomocí virtuálních počítačů Azure, které se synchronizují s místním úložištěm prostřednictvím Azure File Sync nebo RSync. Další možností: Avere vFXT na vstupní nebo výstupní soubory z místních zařízení NAS pomocí systému souborů NFS.

  ![Shlukování cloudu – Hybrid se systémem souborů NFS nebo CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybridní s Blobfuse

Následující diagram znázorňuje hybridní scénář, který obsahuje následující služby Azure:

* **COMPUTE** -Azure Batch fond nebo sada škálování virtuálních počítačů.

* Místní **síť** : Azure ExpressRoute nebo VPN. Azure: Virtuální síť Azure.

* Vstupní a výstupní soubory **úložiště** : Služba BLOB Storage, která je připojená k výpočetním prostředkům prostřednictvím Azure Blobfuse.

  ![Shlukování cloudu – hybridní pomocí Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybridní výpočetní prostředky a úložiště

Následující diagram znázorňuje plně připojený hybridní scénář pro výpočetní prostředky i úložiště a zahrnuje tyto služby Azure:

* **COMPUTE** -Azure Batch fond nebo sada škálování virtuálních počítačů.

* Místní **síť** : Azure ExpressRoute nebo VPN. Azure: Virtuální síť Azure.

* **Úložiště** – mezi místy: Avere vFXT. Volitelná archivace místních souborů prostřednictvím Azure Data Box do úložiště objektů BLOB nebo místní avere FXT pro akceleraci NAS.

  ![Shlukování cloudu – hybridní výpočetní prostředky a úložiště](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Další postup

* Další informace o používání [správců vykreslování](batch-rendering-render-managers.md) pomocí Azure Batch.

* Přečtěte si další informace o možnostech [vykreslování v Azure](batch-rendering-service.md).