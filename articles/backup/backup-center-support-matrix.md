---
title: Matice podpory pro centrum zálohování
description: Tento článek shrnuje scénáře, které centrum zálohování podporuje pro jednotlivé typy úloh.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 672f0e127f173260f25978497198fd7b554aa390
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893588"
---
# <a name="support-matrix-for-backup-center"></a>Matice podpory pro centrum zálohování

Centrum zálohování poskytuje samostatné podokno skla pro podniky, které umožňuje [řídit, monitorovat, provozovat a analyzovat zálohy ve velkém měřítku](backup-center-overview.md). Tento článek shrnuje scénáře, které centrum zálohování podporuje pro jednotlivé typy úloh.

## <a name="supported-scenarios"></a>Podporované scénáře

| **Kategorie** | **Scénář**  | **Podporované úlohy**  | **Omezení** |
| -------------| ------------- | ----------------------- |------------|
| Sledování   | Zobrazit všechny úlohy | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Soubory Azure | <li> za 7 dní vyprší množství úloh, které jsou k dispozici v poli. <br> <li> Každý filtr nebo rozevírací seznam podporuje maximálně 1000 položek. Proto je možné pomocí centra zálohování monitorovat maximálně 1000 odběrů a 1000 trezorů napříč klienty. |
| Sledování | Zobrazit všechny instance zálohování | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files | Stejný jako výše uvedený |
| Sledování | Zobrazit všechny zásady zálohování | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files | Stejný jako výše uvedený |
| Sledování | Zobrazit všechny trezory | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files | Stejný jako výše uvedený |
| Akce | Konfigurace zálohování | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files | Další informace najdete v tématu Podpora matic pro [zálohování virtuálních počítačů Azure](./backup-support-matrix-iaas.md) a [Zálohování serveru Azure Database for PostgreSQL](backup-azure-database-postgresql.md#support-matrix) . |
| Akce | Obnovit instanci zálohy | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files | Další informace najdete v tématu Podpora matic pro [zálohování virtuálních počítačů Azure](./backup-support-matrix-iaas.md) a [Zálohování serveru Azure Database for PostgreSQL](backup-azure-database-postgresql.md#support-matrix) . |
| Akce | Vytvořit trezor | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files | Další informace o maticích pro [úložiště Recovery Services](./backup-support-matrix.md#vault-support) najdete v tématu Podpora. |
| Akce | Vytvořit zásady zálohování | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files | Další informace o maticích pro [úložiště Recovery Services](./backup-support-matrix.md#vault-support) najdete v tématu Podpora. |
| Akce | Spustit zálohování na vyžádání pro instanci zálohování | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files | Další informace najdete v tématu Podpora matic pro [zálohování virtuálních počítačů Azure](./backup-support-matrix-iaas.md) a [Zálohování serveru Azure Database for PostgreSQL](backup-azure-database-postgresql.md#support-matrix) . |
| Akce | Zastavit zálohování instance zálohy | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files | Další informace najdete v tématu Podpora matic pro [zálohování virtuálních počítačů Azure](./backup-support-matrix-iaas.md) a [Zálohování serveru Azure Database for PostgreSQL](backup-azure-database-postgresql.md#support-matrix) . |
| Insights | Zobrazení sestav zálohování | <li> Virtuální počítač Azure <br><br> <li> SQL na virtuálním počítači Azure <br><br> <li> SAP HANA na virtuálním počítači Azure <br><br> <li> Azure Files <br><br> <li> System Center Data Protection Manager <br><br> <li> Agent Azure Backup (MARS) <br><br> <li> Server Azure Backup (MABS) | Informace o [podporovaných scénářích pro sestavy zálohování](./configure-reports.md#supported-scenarios) |
| Zásady správného řízení | Zobrazte a přiřaďte předdefinované a vlastní zásady Azure v kategorii "zálohování". | N/A | N/A |
| Zásady správného řízení | Zobrazit zdroje dat, které nejsou nakonfigurované pro zálohování | <li> Virtuální počítač Azure <br><br> <li> Server Azure Database for PostgreSQL | – |

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

| **Kategorie** | **Scénář**  |
|--------------|---------------|
| Sledování | Zobrazit výstrahy ve velkém měřítku |
| Akce | Konfigurace nastavení trezoru ve velkém měřítku |
| Akce | Spustit úlohu obnovení mezi oblastmi ze služby Backup Center |

## <a name="next-steps"></a>Další kroky

* [Seznamte se s maticí podpory pro Azure Backup](./backup-support-matrix.md)
* [Seznamte se s maticí podpory pro zálohování virtuálních počítačů Azure.](./backup-support-matrix-iaas.md)
* [Seznamte se s maticí podpory pro zálohování serveru Azure Database for PostgreSQL](backup-azure-database-postgresql.md#support-matrix)