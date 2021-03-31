---
title: Referenční architektury vykreslování Azure
description: Architektury pro používání Azure Batch a dalších služeb Azure k rozšíření místní farmy vykreslování s využitím shlukování do cloudu
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 784fd89c3dea88e25a2058713897c7a655c8a3af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83726515"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referenční architektury pro vykreslování Azure

Tento článek ukazuje diagramy architektury vysoké úrovně pro scénáře pro rozšíření nebo "shluky", místní farmu vykreslování do Azure. V příkladech se zobrazují různé možnosti pro výpočetní, síťové a úložné služby Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrid se systémem souborů NFS nebo CFS

Následující diagram znázorňuje hybridní scénář, který obsahuje následující služby Azure:

* **COMPUTE** -Azure Batch fond nebo sada škálování virtuálních počítačů.

* Místní **síť** : Azure EXPRESSROUTE nebo VPN. Azure: virtuální síť Azure.

* Vstupní a výstupní soubory **úložiště** : systém souborů NFS nebo CFS s využitím virtuálních počítačů Azure, které se synchronizují s místním úložištěm pomocí synchronizace souborů Azure nebo rsync. Alternativně: avere vFXT pro vstupní nebo výstupní soubory z místních zařízení NAS pomocí systému souborů NFS.

  ![Shlukování cloudu – Hybrid se systémem souborů NFS nebo CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybridní s Blobfuse

Následující diagram znázorňuje hybridní scénář, který obsahuje následující služby Azure:

* **COMPUTE** -Azure Batch fond nebo sada škálování virtuálních počítačů.

* Místní **síť** : Azure EXPRESSROUTE nebo VPN. Azure: virtuální síť Azure.

* Vstupní a výstupní soubory **úložiště** : BLOB Storage, připojené k výpočetním prostředkům prostřednictvím Azure Blobfuse.

  ![Shlukování cloudu – hybridní pomocí Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybridní výpočetní prostředky a úložiště

Následující diagram znázorňuje plně připojený hybridní scénář pro výpočetní prostředky i úložiště a zahrnuje tyto služby Azure:

* **COMPUTE** -Azure Batch fond nebo sada škálování virtuálních počítačů.

* Místní **síť** : Azure EXPRESSROUTE nebo VPN. Azure: virtuální síť Azure.

* **Úložiště** – mezi místy: avere vFXT. Volitelná archivace místních souborů prostřednictvím Azure Data Box do úložiště objektů BLOB nebo místní avere FXT pro akceleraci NAS.

  ![Shlukování cloudu – hybridní výpočetní prostředky a úložiště](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Další kroky

* Další informace o používání [správců vykreslování](batch-rendering-render-managers.md) pomocí Azure Batch.

* Přečtěte si další informace o možnostech [vykreslování v Azure](batch-rendering-service.md).