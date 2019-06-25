---
title: Obnovit zálohování databází systému SQL Server na Virtuálním počítači Azure pomocí Azure Backup | Dokumentace Microsoftu
description: Tento článek popisuje, jak obnovit databáze systému SQL Server, který běží na Virtuálním počítači Azure a službou Azure Backup, která jsou zálohované.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: d8ade598e4f1b6331367e8bd04ad59951ef5de8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242379"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Obnovení databází serveru SQL Server na virtuálních počítačích Azure

Tento článek popisuje, jak obnovit databáze SQL serveru, na kterém běží na virtuálním počítači Azure (VM), který [Azure Backup](backup-overview.md) služby zálohoval do trezoru služby Azure Backup Recovery Services.

Tento článek popisuje, jak obnovit databáze systému SQL Server. Další informace najdete v tématu [zálohování databází systému SQL Server na virtuálních počítačích Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Obnovení a dobu nebo bod obnovení

Azure Backup můžete obnovit databáze systému SQL Server, na kterých běží na virtuálních počítačích Azure následujícím způsobem:

- Obnovte na konkrétní datum nebo čas (sekundy) pomocí zálohování transakčního protokolu. Azure Backup automaticky určuje odpovídající úplné rozdílové zálohy a řetězec zálohy protokolu, které jsou nutné k obnovení na základě vybraného času.
- Obnovte konkrétní úplné nebo rozdílové zálohy obnovit na konkrétní bod obnovení.


## <a name="prerequisites"></a>Požadavky

Před obnovením databáze, vezměte na vědomí následující:

- Databázi můžete obnovit do instance systému SQL Server ve stejné oblasti Azure.
- Cílový server musí být zaregistrovaný do stejného trezoru jako zdroj.
- Obnovení databáze šifrovaná transparentní šifrování dat na jiný Server SQL, musíte si nejdřív [obnovení certifikátu na cílový server](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Před obnovením databáze "master", spusťte instanci systému SQL Server v jednouživatelském režimu s použitím možnosti spuštění **-m AzureWorkloadBackup**.
    - Hodnota pro **-m** je název klienta.
    - Pouze název zadaného klienta můžete otevřít připojení.
- Všechny systémové databáze (model, hlavní, msdb) zastavte službu agenta systému SQL Server před aktivaci obnovení.
- Zavřete všechny aplikace, které se můžou pokusit převzít připojení ke kterékoli z těchto databází.
- Pokud máte více instancí běží na serveru, všechny instance by měl být nahoru a jinak spuštěný server se nezobrazilo v seznamu cílových serverů pro obnovení databáze.

## <a name="restore-a-database"></a>Obnovení databáze

Pokud chcete obnovit, potřebujete následující oprávnění:

* **Operátor zálohování** oprávnění ve službě vault, kde provádíte obnovení.
* **Přispěvatel (zápis)** přístup k tomuto zdroji virtuální počítač, který se zálohuje.
* **Přispěvatel (zápis)** přístup k cílovému virtuálnímu počítači:
    - Pokud se obnovení do stejného virtuálního počítače, je zdrojový virtuální počítač.
    - Pokud se obnovení do alternativního umístění, je to nový cílový virtuální počítač.

Obnovte takto:
1. Otevřete trezor, ve které je zaregistrovaný virtuální počítač s SQL serverem.
2. Na řídicím panelu trezoru v části **využití**vyberte **zálohování položek**.
3. V **zálohování položek**v části **typu správy zálohování**vyberte **SQL na virtuálním počítači Azure**.

    ![Vyberte SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Vyberte databáze, kterou chcete obnovit.

    ![Vyberte databáze, kterou chcete obnovit](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Projděte si v nabídce databáze. Poskytuje informace o zálohování databáze, včetně:

    * Nejstarší a nejnovější body obnovení.
    * Stav zálohování protokolů za posledních 24 hodin pro databáze, které jsou v režimu úplný a hromadně protokolovaného obnovení, které jsou nakonfigurovány pro zálohy transakčního protokolu.

6. Vyberte **Restore DB**.

    ![Vyberte obnovit databázi](./media/backup-azure-sql-database/restore-db-button.png)

7. V **obnovit konfiguraci**, zadejte, kam chcete data obnovit:
   - **Alternativní umístění**: Obnovení databáze do alternativního umístění a zachovat původní zdrojové databáze.
   - **Přepsat databázi**: Obnovení dat na stejnou instanci systému SQL Server jako původní zdroj. Tato možnost přepíše původní databáze.

     > [!Important]
     > Pokud vybraná databáze patří do skupiny dostupnosti Always On, SQL Server nepovoluje databáze, kterou chcete přepsat. Pouze **alternativního umístění** je k dispozici.
     >

     ![Obnovení nabídky konfigurace](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Obnovení do alternativního umístění

1. V **obnovit konfiguraci** nabídky v části **kam obnovit**vyberte **alternativního umístění**.
2. Vyberte název serveru SQL Server a instance, do kterých chcete databázi obnovit.
3. V **název databáze obnovit** zadejte název cílové databáze.
4. Pokud je k dispozici, vyberte **přepsat, pokud ve vybrané instanci SQL existuje databáze se stejným názvem**.
5. Vyberte **OK**.

    ![Zadejte hodnoty pro konfiguraci obnovení nabídky](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. V **vyberte bod obnovení**vyberte, jestli se má [obnovení k určitému bodu v čase](#restore-to-a-specific-point-in-time) nebo [obnovit na konkrétní bod obnovení](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Obnovení bodu v čase je k dispozici pouze pro zálohy protokolů pro databáze, které jsou v režimu úplný a hromadně protokolovaného obnovení.

### <a name="restore-and-overwrite"></a>Obnovení a přepsání

1. V **obnovit konfiguraci** nabídky v části **kam obnovit**vyberte **přepsat DB** > **OK**.

    ![Vyberte možnost přepsat databázi](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. V **vyberte bod obnovení**vyberte **protokoly (časový okamžik)** k [obnovení k určitému bodu v čase](#restore-to-a-specific-point-in-time). Nebo vyberte **úplné a rozdílové** obnovíte [konkrétní bod obnovení](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Obnovení bodu v čase je k dispozici pouze pro zálohy protokolů pro databáze, které jsou v režimu úplný a hromadně protokolovaného obnovení.

### <a name="restore-to-a-specific-point-in-time"></a>Obnovení k určitému bodu v čase

Pokud jste vybrali **protokoly (časový okamžik)** jako typ obnovení, postupujte takto:

1.  V části **obnovit data a času**, otevřete v kalendáři. V kalendáři data, která mají body obnovení jsou zobrazeny tučným písmem a zvýrazní se aktuální datum.
1. Vyberte datum, které se body obnovení. Nelze vybrat data, která mají žádné body obnovení.

    ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Vyberte datum a časové osy grafu zobrazuje dostupné body obnovení v průběžné rozsahu.
1. Zadejte čas pro obnovení do časové osy grafu nebo vyberte čas. Pak vyberte **OK**.

    ![Vyberte dobu obnovení](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. Na **Upřesnit konfiguraci** Povolit nabídku, pokud chcete zachovat databázi po obnovení, nefunkčními **obnovit s NORECOVERY**.
1. Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadejte novou cílovou cestu.
1. Vyberte **OK**.

    ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Na **obnovení** nabídce vyberte možnost **obnovení** spustit úlohu obnovení.
1. Sledovat průběh na obnovení **oznámení** oblasti, nebo sledovat tak, že vyberete **obnovení úlohy** v nabídce databáze.

    ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Obnovení do bodu obnovení konkrétní

Pokud jste vybrali **úplné a rozdílové** jako typ obnovení, postupujte takto:

1. Vyberte bod obnovení ze seznamu a vyberte **OK** postup bodu obnovení.

    ![Vyberte úplný bod obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Na **Upřesnit konfiguraci** Povolit nabídku, pokud chcete zachovat databázi po obnovení, nefunkčními **obnovit s NORECOVERY**.
1. Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadejte novou cílovou cestu.
1. Vyberte **OK**.

    ![Nabídka Upřesnit konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Na **obnovení** nabídce vyberte možnost **obnovení** spustit úlohu obnovení.
1. Sledovat průběh na obnovení **oznámení** oblasti, nebo sledovat tak, že vyberete **obnovení úlohy** v nabídce databáze.

    ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Obnovení databáze s velkým počtem souborů

Pokud je větší než velikost řetězce celkový počet souborů v databázi [určitého limitu](backup-sql-server-azure-troubleshoot.md#files-size-limit-beyond-which-restore-happens-to-default-path), Azure Backup ukládá seznam souborů databáze v různých pit součásti tak, nebudete moci nastavit cílovou cestu obnovení během obnovení operace. Soubory se obnoví na výchozí cestu SQL místo.

  ![Obnovte databázi pomocí velkých souborů](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>Další postup

[Spravovat a monitorovat](manage-monitor-sql-database-backup.md) databáze systému SQL Server, které se zálohují v Azure Backup.
