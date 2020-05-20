---
title: Matice podpory pro zálohování sdílených složek Azure
description: Obsahuje souhrn nastavení podpory a omezení při zálohování sdílených složek Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.openlocfilehash: 42578cc83ef193801fa700ec7d136385411e5f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684621"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matice podpory pro zálohování sdílených složek Azure

[Službu Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) můžete použít k zálohování sdílených složek Azure. Tento článek shrnuje nastavení podpory při zálohování sdílených složek Azure pomocí Azure Backup.

## <a name="supported-geos"></a>Podporované zeměpisných oblastech

Zálohování sdílených složek Azure je dostupné v následujících zeměpisných oblastech:

**Oblasti GA**:<br>
Austrálie – jihovýchod (pomocného), Kanada – střed (CNC), Středozápadní USA (WCUS), Střed USA – jih (SCUS), Západní USA 2 (WUS 2), Indie – jih (INS), Střed USA – sever (NCUS), Japonsko – východ (JPE), Brazílie – jih (BRS), Jižní Východní Asie (moře), Švýcarsko – západ (SZW), Spojené arabské emiráty Central (řízení uživatelských účtů), Norsko – východ (nDoporučujeme), Indie – západ (INW), Austrálie – střed (ACL), Jižní Afrika (SAN); Severní Korea , Velká Británie – jih (UKS), Velká Británie – západ (UKW), Korea – jih (KRS), Severní Evropa (NE), Německo – sever (GN), Norsko – západ (NWW), Jižní Afrika – západ (nevidělo), Švýcarsko – sever (SZN), Německo – středozápad (GWC), Spojené arabské emiráty sever (MOČOVINa, Francie – střed), Indie – střed (INC), Kanada – východ (FRC), Východní Asie (EA), Austrálie – východ (AE), Střed USA (CNE), Západní USA (kapacitní jednotky), US gov – Arizona (WUS) US gov – Texas , US Gov – Virginie (UGV), US DoD – střed (UDC), US DoD – východ (OUČIT)

**Podporované oblasti (jako součást verze Preview), ale zatím ne GA**:<br>
Východní USA (EUS), Východní USA 2 (EUS2), Západní Evropa (My)

## <a name="supported-storage-accounts"></a>Podporované účty úložiště

| Podrobnosti účtu úložiště | Podpora                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Druh účtu            | Azure Backup podporuje sdílené složky Azure, které jsou k dispozici v obecných účelech V1, pro obecné účely v2 a na typech úložiště souborů. |
| Výkon              | Azure Backup podporuje sdílení souborů v účtech Standard a Premium Storage. |
| Replikace              | Sdílené složky Azure Files v účtech úložiště s libovolným typem replikace jsou podporované. |

## <a name="supported-file-shares"></a>Podporované sdílené složky

| Typ sdílené složky                                   | Podpora   |
| -------------------------------------------------- | --------- |
| Standard                                           | Podporuje se |
| Velká                                              | Podporuje se |
| Premium                                            | Podporuje se |
| Sdílené složky připojené ke službě Azure File Sync | Podporuje se |

## <a name="protection-limits"></a>Omezení ochrany

| Nastavení                                                      | Omezení |
| ------------------------------------------------------------ | ----- |
| Maximální počet sdílených složek, které je možné chránit za den na jeden trezor | 200   |
| Maximální počet účtů úložiště, které se dají registrovat na trezor za den | 50    |

## <a name="backup-limits"></a>Omezení zálohování

| Nastavení                                      | Omezení |
| -------------------------------------------- | ----- |
| Maximální počet záloh na vyžádání za den | 4     |
| Maximální počet plánovaných záloh za den | 1     |

## <a name="restore-limits"></a>Omezení obnovení

| Nastavení                                                      | Omezení   |
| ------------------------------------------------------------ | ------- |
| Maximální počet obnovení za den                           | 10      |
| Maximální počet souborů na obnovení                         | 10      |
| Maximální doporučená velikost obnovení na obnovení pro velké sdílené složky | 15 TiB |

## <a name="retention-limits"></a>Omezení uchování

| Nastavení                                                      | Omezení    |
| ------------------------------------------------------------ | -------- |
| Maximální celkový počet bodů obnovení na sdílenou složku v jakémkoli časovém okamžiku | 200      |
| Maximální doba uchování bodu obnovení vytvořeného zálohováním na vyžádání | 10 let |
| Maximální uchovávání denních bodů obnovení (snímků) na sdílení souborů| 200 dní |
| Maximální doba uchování týdenních bodů obnovení (snímků) na sdílení souborů | 200 týdnů |
| Maximální uchovávání měsíčních bodů obnovení (snímků) na sdílení souborů | 120 měsíců |
| Maximální doba uchování ročních bodů obnovení (snímků) na sdílení souborů | 10 let |

## <a name="supported-restore-methods"></a>Podporované metody obnovení

| Metoda Restore     | Podrobnosti                                                      |
| ------------------ | ------------------------------------------------------------ |
| Úplné obnovení sdílené složky | Úplnou sdílenou složku můžete obnovit do původního nebo alternativního umístění. |
| Obnovení na úrovni položek | Jednotlivé soubory a složky můžete obnovit do původního nebo alternativního umístění. |

## <a name="next-steps"></a>Další kroky

* Informace o [Zálohování sdílených složek Azure](backup-afs.md)
* Přečtěte si, jak [obnovit sdílené složky Azure](restore-afs.md) .
* Naučte se [Spravovat zálohy sdílených složek Azure](manage-afs-backup.md)
