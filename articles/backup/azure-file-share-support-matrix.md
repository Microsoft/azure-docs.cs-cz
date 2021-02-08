---
title: Matice podpory pro zálohování sdílených složek Azure
description: Obsahuje souhrn nastavení podpory a omezení při zálohování sdílených složek Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 999cb4c764e9960c509d19faa61016b2522259ac
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819856"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matice podpory pro zálohování sdílených složek Azure

[Službu Azure Backup](./backup-overview.md) můžete použít k zálohování sdílených složek Azure. Tento článek shrnuje nastavení podpory při zálohování sdílených složek Azure pomocí Azure Backup.

## <a name="supported-regions"></a>Podporované oblasti

### <a name="ga-regions-for-azure-file-shares-backup"></a>Oblasti GA pro zálohování sdílených složek Azure

Zálohování sdílených složek Azure je k dispozici ve všech oblastech **s výjimkou** : Německo – střed (svrchovan), Německo – Severovýchod (svrchovan), Čína – východ Čína – východ 2 Čína – sever Čína – sever 2 US gov – Iowa

## <a name="supported-storage-accounts"></a>Podporované účty úložiště

| Podrobnosti účtu úložiště | Podpora                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Druh účtu            | Azure Backup podporuje sdílené složky Azure přítomné v účtech úložiště pro obecné účely V1, obecné verze V2 a souborové úložiště. |
| Výkon              | Azure Backup podporuje sdílení souborů v účtech Standard a Premium Storage. |
| Replikace              | Podpora sdílených složek Azure v účtech úložiště s jakýmkoli typem replikace je podporovaná. |
| Povolená brána firewall         | Sdílené složky Azure v účtech úložiště s pravidly brány firewall, které umožňují Microsoft Azure služby pro přístup k účtu úložiště, jsou podporované.|

## <a name="supported-file-shares"></a>Podporované sdílené složky

| Typ sdílené složky                                   | Podpora   |
| -------------------------------------------------- | --------- |
| Standard                                           | Podporováno |
| Velká                                              | Podporováno |
| Premium                                            | Podporováno |
| Sdílené složky připojené ke službě Azure File Sync | Podporováno |

## <a name="protection-limits"></a>Omezení ochrany

| Nastavení                                                      | Omezení |
| ------------------------------------------------------------ | ----- |
| Maximální počet sdílených složek, které je možné chránit na trezor za den| 200   |
| Maximální počet účtů úložiště, které se dají registrovat na trezor za den | 50    |
| Maximální počet sdílených složek, které se dají chránit na jeden trezor | 2000   |
| Maximální počet účtů úložiště, které se můžou registrovat na jeden trezor | 200   |

## <a name="backup-limits"></a>Omezení zálohování

| Nastavení                                      | Omezení |
| -------------------------------------------- | ----- |
| Maximální počet záloh na vyžádání za den | 10   |
| Maximální počet plánovaných záloh za den | 1     |

## <a name="restore-limits"></a>Omezení obnovení

| Nastavení                                                      | Omezení   |
| ------------------------------------------------------------ | ------- |
| Maximální počet obnovení za den                           | 10      |
| Maximální počet souborů na obnovení                         | 99      |
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
