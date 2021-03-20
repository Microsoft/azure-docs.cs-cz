---
title: Možnosti přenosu dat do Azure pomocí zařízení | Microsoft Docs
description: Naučte se, jak zvolit správné zařízení pro místní přenos dat do Azure mezi Data Box Edge, Synchronizace souborů Azure a StorSimple řady 8000.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4fc92146d6f076287fe37f64734bb07e8250792b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882208"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Porovnání StorSimple s možnostmi přenosu dat pomocí Synchronizace souborů Azure a Data Boxu Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Tento dokument poskytuje přehled možností místního přenosu dat do Azure, porovnání: Data Box Edge vs. Synchronizace souborů Azure vs. StorSimple 8000 series.

- **[Data box Edge](../databox-online/azure-stack-edge-overview.md)** – data box Edge je místní síťové zařízení, které přesouvá data do a z Azure a má k předběžnému zpracování dat s povoleným AI. Data Box Gateway je virtuální verze tohoto zařízení, ale se stejnými funkcemi pro přenos dat.
- **[Synchronizace souborů Azure](../storage/files/storage-sync-files-deployment-guide.md)** – synchronizace souborů Azure můžete použít k centralizaci sdílených složek ve vaší organizaci ve službě soubory Azure a zároveň udržet flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. Obecná dostupnost Synchronizace souborů Azure byla oznámena dříve v 2018.
- **[StorSimple](./storsimple-overview.md)** – StorSimple je hybridní zařízení, které pomáhá podnikům konsolidovat svou infrastrukturu úložiště pro primární úložiště, ochranu dat, archivaci a zotavení po havárii v jednom řešení, a to těsně integrací se službou Azure Storage. Životní cyklus produktu pro StorSimple najdete [tady](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Souhrn porovnání

|                           |StorSimple 8000   |Synchronizace souborů Azure   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Přehled**     |Vrstvené hybridní úložiště a archivace|Obecné úložiště souborového serveru s využitím vrstev cloudu a synchronizace více lokalit.  |Řešení úložiště pro předběžné zpracování dat a jejich odesílání přes síť do Azure        |
|**Scénáře**    |Souborový server, archivace, cíl zálohování |Souborový server, archivace (více lokalit)   |Přenos dat, předběžné zpracování dat, včetně ML Inferencing, IoT, archivace    |
|**Výpočet hran** |Není k dispozici |Není k dispozici |Podporuje spuštěné kontejnery pomocí Azure IoT Edge    |
|**Formulářový faktor**  |Fyzické zařízení   |Agent nainstalovaný na Windows serveru |Fyzické zařízení   |
|**Hardware**     |Fyzické zařízení poskytované od Microsoftu jako součást služby | Poskytnutý zákazník |Fyzické zařízení poskytované od Microsoftu jako součást služby  |
|**Formát dat**  |Vlastní formát   |Soubory         |Objekty blob nebo soubory    |
|**Podpora protokolu** |iSCSI          |SMB, NFS    | SMB nebo NFS      |
|**Ceny**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Synchronizace souborů Azure](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Další kroky

- Informace o [Azure Data box Edge](../databox-online/azure-stack-edge-overview.md) a [Azure Data box Gateway](../databox-gateway/data-box-gateway-overview.md)
- Informace o [synchronizace souborů Azure](../storage/files/storage-sync-files-deployment-guide.md)