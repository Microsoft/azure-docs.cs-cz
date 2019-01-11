---
title: Možnosti pro data přenést do Azure s využitím zařízení | Dokumentace Microsoftu
description: Zjistěte, jak vybrat správné zařízení pro přenos dat do Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 11/12/2018
ms.author: alkohli
ms.openlocfilehash: b5ced2814aff51317304cd44c41b04ab9a954844
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213191"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Porovnat s Azure File Sync a okraj pole Data možnosti přenosu dat StorSimple 
 
Tento dokument obsahuje přehled možností pro přenos místních dat do Azure, porovnání: Data Box Edge vs. Azure File Sync vs. StorSimple řady 8000.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)**  – okraj pole dat je zařízení místní sítě, který přesouvá data do a z Azure a má povolené AI hraniční výpočetní předběžně zpracovat data při nahrávání. Jsme představili na konferenci Ignite 2018 a je ve verzi public preview. Brána pole dat je virtuální verzi zařízení pomocí stejné funkce přenosu dat.
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)**  – Azure File Sync umožňuje centralizovat sdílené složky organizace ve službě soubory Azure, při zachování flexibility, výkonu a kompatibility s místními souborového serveru. Azure File Sync transformuje serveru systému Windows na rychlou mezipaměť sdílené složky Azure. Obecná dostupnost služby Azure File Sync jsme představili dříve v roce 2018.
- **[StorSimple](/azure/storsimple/storsimple-overview)**  – StorSimple je hybridní zařízení, která pomáhá podnikům, že se úzce integruje konsolidovat svou infrastrukturu úložiště pro primární úložiště, ochranu dat, archivaci a zotavení po havárii v jediném řešení Úložiště Azure. Životní cyklus produktu pro StorSimple najdete [tady](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Souhrn porovnání

|                           |StorSimple 8000   |Synchronizace souborů Azure   |Data Box Edge (preview)           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Přehled         |Vrstvené úložiště hybridní a archivace|Obecné sdílené úložiště serveru se synchronizací více webů a vrstvení cloudu.  |Řešení úložiště pro předzpracování dat a jejich odesílání přes síť do Azure.        |
|Scénáře        |Souborový server, archivace, záložní cíl |Souborový server, archivace (Multi-Site)   |Přenos dat, data předběžného zpracování včetně ML odvozování, IoT, archivace    |
|Výpočetní funkce Edge     |Není k dispozici. |Není k dispozici. |Podporuje spouštění kontejnerů pomocí Azure IoT Edge    |
|Uspořádání formuláře      |Fyzické zařízení   |Agent nainstalovaný v systému Windows Server |Fyzické zařízení   |
|Hardware         |Fyzické zařízení od Microsoftu k dispozici jako součást služby | Zajišťované zákazníkem |Fyzické zařízení od Microsoftu k dispozici jako součást služby  |
|Formát dat      |Vlastní formát   |Soubory         |Objekty BLOB nebo souborů    |
|Podpora protokolu |iSCSI          |PROTOKOL SMB, NFS    | SMB nebo NFS      |
|Ceny          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Další postup

- Další informace o [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) a [Azure Data Box brány](/azure/databox-online/data-box-gateway-overview)
- Další informace o [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)
