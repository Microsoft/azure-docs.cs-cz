---
title: Matice podpory pro zálohování sdílených složek Azure
description: Obsahuje souhrn nastavení podpory a omezení při zálohování sdílených složek Azure.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: 03190c6b83fceec88442583a57ff46756cfe6221
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195685"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matice podpory pro zálohování sdílených složek Azure

[Službu Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) můžete použít k zálohování sdílených složek Azure. Tento článek shrnuje nastavení podpory při zálohování sdílených složek Azure pomocí Azure Backup.

## <a name="supported-geos"></a>Podporované zeměpisných oblastech

Zálohování sdílených složek Azure je dostupné v následujících zeměpisných oblastech:

| Oblasti GA | Podporované oblasti, ale ne GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Austrálie – jihovýchod (pomocného), Kanada – střed (CNC), Středozápadní USA (WCUS), Západní USA 2 (WUS 2), Indie – jih (in), Střed USA – sever (NCUS), Japonsko – východ (JPE), Brazílie – jih (BRS), Jižní Východní Asie (moře)                                                     |Austrálie – východ (AE), Kanada – východ (CE), Východní Asie (EA), Východní USA (EUS), Východní USA 2 (EUS2), Japonsko – západ (JPW), Indie – střed (INC), Korea – střed (KRC), Korea – jih (KRS), Severní Evropa (NE), Střed USA – jih (SCUS), Velká Británie – jih (UKS), Velká Británie – západ (UKW), Západní Evropa (WE), západní USA (WUS), US Gov – Arizona (UGA), US gov – Texas (UGT), US gov – Virginie (UGV), Austrálie – střed (ACL) , Indie – západ (INW), Jižní Afrika – sever (SAN), Spojené arabské emiráty sever (MOČOVINa, Francie – střed), Německo – sever (GN), Německo – středozápad (GWC), Jižní Afrika – západ (), FRC (Spojené arabské emiráty), Norsko – východ (NDOPORUČUJEME), Norsko – západ (NWW), Švýcarsko – sever (SZN), Střed USA (kapacitní jednotky)           |

## <a name="supported-storage-accounts"></a>Podporované účty úložiště

| Podrobnosti účtu úložiště | Podpora                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Druh účtu            | Azure Backup podporuje sdílené složky Azure, které jsou k dispozici pro obecné účely V1, obecné účely v2 a účty úložiště typu sdílení souborů. |
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
