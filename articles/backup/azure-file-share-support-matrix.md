---
title: Matice podpory pro zálohování sdílených složek Azure
description: Obsahuje souhrn nastavení podpory a omezení při zálohování sdílených složek Azure.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: b5bfae9454464bc8d11cef3bd346861e13822046
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838565"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matice podpory pro zálohování sdílených složek Azure

[Službu Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) můžete použít k zálohování sdílených složek Azure. Tento článek shrnuje nastavení podpory při zálohování sdílených složek Azure pomocí Azure Backup.

## <a name="supported-geos"></a>Podporované zeměpisných oblastech

Zálohování sdílených složek Azure je dostupné v následujících zeměpisných oblastech:

| Oblasti GA | Podporované oblasti (jako součást verze Preview), ale zatím ne GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Austrálie – jihovýchod (pomocného mechanismu), Kanada – střed (CNC), Středozápadní USA (WCUS), Západní USA 2 (WUS 2), Indie – jih (in), Střed USA – sever (NCUS), Japonsko – východ (JPE), Brazílie – jih (BRS), Jižní Východní Asie (moře), Švýcarsko – západ (SZW), Spojené arabské emiráty Central (UAC), Norsko – východ (nDoporučujeme), Indie – západ (INW), Austrálie – střed (ACL), Korea – střed (KRC), Japonsko – západ (JPW), Jižní Afrika Velká Británie – západ , Korea – jih (KRS), Německo – sever (GN), Norsko – západ (NWW), Jižní Afrika – západ (SZN), Německo – Středozápad (GWC) Švýcarsko – sever, Spojené arabské emiráty sever (MOČOVINa), Francie – střed (FRC), Indie – střed (INC), Kanada – východ (CNE), Východní Asie (EA), Austrálie – východ (AE), Střed USA (kapacitní jednotky), Západní USA (WUS)                                                  |  Východní USA (EUS), Východní USA 2 (EUS2), Severní Evropa (NE), Střed USA – jih (SCUS), Velká Británie – jih (UKS), Západní Evropa (My), US gov – Arizona (UGA), US Gov – Texas (UGT), US gov – Virginie (UGV)           |

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
| Dlouhodobé používání                                              | Podporuje se |
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
