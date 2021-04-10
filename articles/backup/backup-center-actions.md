---
title: Provádění akcí pomocí centra zálohování
description: Tento článek vysvětluje, jak provádět akce pomocí centra zálohování.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8c21475e5a52cdce7e38bbeb9d00df3c3ac3a752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506443"
---
# <a name="perform-actions-using-backup-center"></a>Provádění akcí pomocí centra zálohování

Centrum zálohování umožňuje provádět akce související se zálohováním klíče z centrálního rozhraní, aniž by bylo nutné přecházet do individuálního trezoru. Některé akce, které můžete provést ze služby Backup Center, jsou:

* Konfigurace zálohování pro zdroje dat
* Obnovení instance zálohy
* Vytvoření nového trezoru
* Vytvoření nové zásady zálohování
* Aktivace zálohy na vyžádání pro instanci zálohování
* Zastavit zálohování instance zálohy

## <a name="supported-scenarios"></a>Podporované scénáře

* Centrum zálohování se v současné době podporuje pro zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure, SAP HANA v zálohování virtuálních počítačů Azure, zálohování souborů Azure a zálohování serveru Azure Database for PostgreSQL.
* Podrobný seznam podporovaných a nepodporovaných scénářů najdete v tématu věnovaném [matrici podpory](backup-center-support-matrix.md) .

## <a name="configure-backup"></a>Konfigurace zálohování

Pokud zálohujete virtuální počítače Azure, SQL ve virtuálních počítačích Azure, SAP HANA ve virtuálních počítačích Azure nebo ve službě soubory Azure, měli byste použít trezor Recovery Services. Pokud zálohujete databáze Azure pro server PostgreSQL, měli byste použít úložiště záloh. 

V závislosti na typu zdroje dat, který chcete zálohovat, postupujte podle příslušných pokynů, jak je popsáno níže.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Konfigurace zálohování do trezoru Recovery Services

1. Přejděte do centra zálohování a v horní části karty **Přehled** vyberte **+ záloha** .

    ![Přehled služby Backup Center](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Vyberte typ zdroje dat, který chcete zálohovat.

    ![Vyberte zdroj dat pro konfiguraci zálohování virtuálního počítače.](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Zvolte trezor Recovery Services a vyberte **pokračovat**. To vás vede k prostředí Konfigurace zálohování, které je stejné jako dostupné z trezoru Recovery Services. [Přečtěte si další informace o tom, jak nakonfigurovat zálohování virtuálních počítačů Azure pomocí trezoru Recovery Services](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>Konfigurace zálohování do trezoru záloh

1. Přejděte do centra zálohování a v horní části karty **Přehled** vyberte **+ záloha** .
2. Vyberte typ zdroje dat, který chcete zálohovat (Azure Database for PostgreSQL Server v tomto případě).

    ![Vyberte zdroj dat pro konfiguraci zálohování serveru Azure Database for PostgreSQL.](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Vyberte **pokračovat**. To vás vede na možnosti konfigurace zálohování, které se shodují s tím, co je dosažitelné z úložiště záloh. [Přečtěte si další informace o tom, jak nakonfigurovat zálohování pro Azure Database for PostgreSQL Server s úložištěm záloh](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Obnovení instance zálohy

V závislosti na typu zdroje dat, který chcete obnovit, postupujte podle příslušných pokynů, jak je popsáno níže.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Pokud obnovujete z Recovery Servicesového trezoru

1. Přejděte do centra záloh a v horní části karty **Přehled** vyberte **obnovit** .

    ![Přehled služby Backup pro obnovení virtuálního počítače](./media/backup-center-actions/backup-center-overview-restore.png)

2. Vyberte typ zdroje dat, který chcete obnovit.

    ![Výběr zdroje dat pro obnovení virtuálního počítače](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Zvolte instanci zálohování a vyberte **pokračovat**. To vede k nastavení obnovení, které je stejné jako dostupné z trezoru Recovery Services. [Přečtěte si další informace o tom, jak obnovit virtuální počítač Azure pomocí trezoru Recovery Services](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>Pokud obnovujete z trezoru služby Backup

1. Přejděte do centra záloh a v horní části karty **Přehled** vyberte **obnovit** .
2. Vyberte typ zdroje dat, který chcete obnovit (Azure Database for PostgreSQL Server v tomto případě).

    ![Výběr zdroje dat pro obnovení serveru Azure Database for PostgreSQL](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Zvolte instanci zálohování a vyberte **pokračovat**. To vede k nastavení obnovení, které je stejné jako dostupné z trezoru Recovery Services. [Přečtěte si další informace o tom, jak obnovit Azure Database for PostgreSQL Server pomocí trezoru záloh](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Vytvoření nového trezoru

Nový trezor můžete vytvořit tak, že přejdete do centra zálohování a v horní části karty **Přehled** vyberete **+ trezor** .

![Vytvořit trezor](./media/backup-center-actions/backup-center-create-vault.png)

* [Další informace o vytvoření trezoru služby Recovery Services](backup-create-rs-vault.md)
* [Další informace o vytvoření trezoru záloh](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Vytvoření nové zásady zálohování

V závislosti na typu zdroje dat, který chcete zálohovat, postupujte podle příslušných pokynů popsaných níže.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Pokud zálohujete na Recovery Services trezor

1. Přejděte do centra zálohování a v horní části karty **Přehled** vyberte **+ zásada** .

    ![Přehled centra záloh pro zásady zálohování](./media/backup-center-actions/backup-center-overview-policy.png)

2. Vyberte typ zdroje dat, který chcete zálohovat.

    ![Vyberte zdroj dat pro zásady zálohování virtuálního počítače](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Zvolte trezor služby Recovery Services a vyberte **pokračovat**. To vás vede k prostředí pro vytváření zásad, které je stejné jako dostupné z trezoru Recovery Services. [Přečtěte si další informace o tom, jak vytvořit nové zásady zálohování pro virtuální počítač Azure pomocí trezoru služby Recovery Services](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>Pokud zálohujete do trezoru služby Backup

1. Přejděte do centra zálohování a v horní části karty **Přehled** vyberte **+ zásada** .
2. Vyberte typ zdroje dat, který chcete zálohovat (Azure Database for PostgreSQL Server v tomto případě).

    ![Vyberte zdroj dat pro zásady pro zálohování serveru Azure Database for PostgreSQL.](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Vyberte **pokračovat**. To vede k prostředí pro vytváření zásad, které je stejné jako dosažitelné z úložiště záloh. [Přečtěte si další informace o tom, jak vytvořit nové zásady zálohování pomocí trezoru záloh](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Spustit zálohování na vyžádání pro instanci zálohování

Služba Backup Center umožňuje vyhledávat instance služby Backup v celé nemovitosti a provádět operace zálohování na vyžádání.

Pokud chcete aktivovat zálohování na vyžádání, přejděte do centra zálohování a vyberte položku nabídky **instance zálohování** . Výběrem této možnosti můžete zobrazit podrobnosti o všech instancích zálohy, ke kterým máte přístup. Můžete vyhledat instanci zálohování, kterou chcete zálohovat. Kliknutím pravým tlačítkem myši na položku v mřížce se zobrazí seznam dostupných akcí. Pokud chcete spustit zálohování na vyžádání, vyberte možnost **Zálohovat nyní** .

![Zálohování na vyžádání](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Přečtěte si další informace o provádění zálohování na vyžádání pro Azure Virtual Machines](backup-azure-manage-vms.md#run-an-on-demand-backup).

[Přečtěte si další informace o provádění zálohování na vyžádání pro Azure Database for PostgreSQL Server](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Zastavit zálohování instance zálohy

K dispozici jsou scénáře, kdy můžete chtít zastavit zálohování instance zálohy, například pokud již zálohovaný základní prostředek neexistuje.

Pokud chcete aktivovat zálohování na vyžádání, přejděte do centra zálohování a vyberte položku nabídky **instance zálohování** . Tato možnost umožňuje zobrazit podrobnosti o všech instancích zálohy, ke kterým máte přístup. Můžete vyhledat instanci zálohování, kterou chcete zálohovat. Kliknutím pravým tlačítkem myši na položku v mřížce se zobrazí seznam dostupných akcí. Vyberte možnost **Zastavit zálohování** a zastavte zálohování instance zálohování.

![Zastavení ochrany](./media/backup-center-actions/backup-center-stop-protection.png)

[Přečtěte si další informace o zastavení zálohování pro Azure Virtual Machines](backup-azure-manage-vms.md#stop-protecting-a-vm).

[Další informace o zastavení zálohování pro Azure Database for PostgreSQL Server](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>Další kroky

* [Monitorování a provozování záloh](backup-center-monitor-operate.md)
* [Řízení vaší záložní nemovitosti](backup-center-govern-environment.md)
* [Získejte přehled o vašich zálohách](backup-center-obtain-insights.md)
