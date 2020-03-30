---
title: Možnosti přenosu dat do Azure pomocí zařízení | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vybrat správné zařízení pro přenos dat do Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9456d975ffb6d7726166f9badbcb8fe0ccbe5256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965360"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Porovnání StorSimple s možnostmi přenosu dat pomocí Synchronizace souborů Azure a Data Boxu Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Tento dokument poskytuje přehled možností pro místní přenos dat do Azure, porovnání: Data Box Edge vs. Azure File Sync vs. StorSimple 8000 series.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – Data Box Edge je místní síťové zařízení, které přesouvá data do a z Azure a má edge s podporou UI pro předběžné zpracování dat během nahrávání. Brána datové schránky je virtuální verze zařízení se stejnými možnostmi přenosu dat.
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)** – Azure File Sync se dá použít k centralizaci sdílených složek vaší organizace v souborech Azure a při zachování flexibility, výkonu a kompatibility místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. Obecná dostupnost Azure File Sync byla oznámena dříve v 2018.
- **[StorSimple](/azure/storsimple/storsimple-overview)** – StorSimple je hybridní zařízení, které pomáhá podnikům konsolidovat infrastrukturu úložiště pro primární úložiště, ochranu dat, archivaci a zotavení po havárii na jediném řešení tím, že úzce integruje s úložištěm Azure. Životní cyklus produktu pro StorSimple naleznete [zde](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Souhrn porovnání

|                           |StorJednoduché 8000   |Synchronizace souborů Azure   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Přehled         |Vrstvené hybridní úložiště a archivace|Obecné úložiště souborového serveru s vrstvením cloudu a synchronizací ve více lokalitech.  |Řešení úložiště pro předběžné zpracování dat a jejich odeslání přes síť do Azure.        |
|Scénáře        |Souborový server, archivní, záložní cíl |Souborový server, archivní (více míst)   |Přenos dat, předběžné zpracování dat včetně ml inferencingu, IoT, archivní    |
|Výpočetní funkce Edge     |Není k dispozici. |Není k dispozici. |Podporuje spuštěné kontejnery pomocí Azure IoT Edge    |
|Tvarový faktor      |Fyzické zařízení   |Agent nainstalovaný v systému Windows Server |Fyzické zařízení   |
|Hardware         |Fyzické zařízení poskytované společností Microsoft jako součást služby | Zákazník za předpokladu, |Fyzické zařízení poskytované společností Microsoft jako součást služby  |
|Formát dat      |Vlastní formát   |Soubory         |Objekty BLOB nebo soubory    |
|Podpora protokolu |iSCSI          |SMB, NFS    | SMB nebo NFS      |
|Ceny          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Synchronizace souborů Azure](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Další kroky

- Informace o [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) a Azure Data Box [Gateway](/azure/databox-online/data-box-gateway-overview)
- Další informace o [synchronizaci souborů Azure](/azure/storage/files/storage-sync-files-deployment-guide)
