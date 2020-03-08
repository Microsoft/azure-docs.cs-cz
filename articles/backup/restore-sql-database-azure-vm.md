---
title: Obnovení databází SQL Server na virtuálním počítači Azure
description: Tento článek popisuje, jak obnovit SQL Server databáze, které běží na virtuálním počítači Azure a které se zálohují s Azure Backup.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392829"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Obnovení databází SQL Server na virtuálních počítačích Azure

Tento článek popisuje, jak obnovit databázi SQL Server, která běží na virtuálním počítači Azure, který je službou [Azure Backup](backup-overview.md) zálohovaný do Azure Backup Recovery Services trezoru.

Tento článek popisuje, jak obnovit databáze SQL Server. Další informace najdete v tématu [zálohování SQL Server databází na virtuálních počítačích Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Obnovení na čas nebo bod obnovení

Azure Backup může obnovit databáze SQL Server, které běží na virtuálních počítačích Azure, takto:

- Obnovení na konkrétní datum nebo čas (do druhé) pomocí záloh protokolu transakcí. Azure Backup automaticky určí odpovídající úplné rozdílové zálohování a řetěz záloh protokolů, které jsou nutné k obnovení na základě vybraného času.
- Obnovení konkrétního úplného nebo rozdílového zálohování pro obnovení do konkrétního bodu obnovení.

## <a name="prerequisites"></a>Předpoklady

Před obnovením databáze mějte na paměti následující:

- Databázi můžete obnovit do instance SQL Server ve stejné oblasti Azure.
- Cílový server musí být zaregistrován ve stejném trezoru jako zdroj.
- Chcete-li obnovit TDE šifrovanou databázi do jiné SQL Server, je nutné nejprve [obnovit certifikát na cílový server](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Než obnovíte hlavní databázi, spusťte instanci SQL Server v jednouživatelském režimu pomocí možnosti Startup **-m AzureWorkloadBackup**.
  - Hodnota pro **-m** je název klienta.
  - Připojení může otevřít pouze zadaný název klienta.
- Pro všechny systémové databáze (model, hlavní počítač, msdb) zastavte službu agenta SQL Server před aktivací obnovení.
- Ukončete všechny aplikace, které se mohou pokusit připojit k některé z těchto databází.
- Pokud máte na serveru spuštěných víc instancí, měly by být všechny instance spuštěné a spuštěné, jinak se server neobjeví v seznamu cílových serverů, na který se má obnovit databáze.

## <a name="restore-a-database"></a>Obnovení databáze

K obnovení potřebujete následující oprávnění:

- Oprávnění **operátora zálohování** v trezoru, ve kterém provádíte obnovení.
- **Přispěvatel (Write)** přístup ke ZDROJOVÉmu virtuálnímu počítači, který se zálohuje.
- **Přispěvatel (zápis)** k CÍLOVÉmu virtuálnímu počítači:
  - Pokud provádíte obnovení do stejného virtuálního počítače, jedná se o zdrojový virtuální počítač.
  - Pokud provádíte obnovení do alternativního umístění, jedná se o nový cílový virtuální počítač.

Obnovte následujícím způsobem:

1. Otevřete trezor, ve kterém je virtuální počítač SQL Server zaregistrován.
2. Na řídicím panelu trezoru v části **využití**vyberte **zálohované položky**.
3. V části **zálohované položky**v části **typ správy zálohování**vyberte **SQL na virtuálním počítači Azure**.

    ![Výběr SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Vyberte databázi, kterou chcete obnovit.

    ![Vyberte databázi, kterou chcete obnovit.](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Projděte si nabídku databáze. Poskytuje informace o zálohování databáze, včetně:

    - Nejstarší a nejnovější body obnovení.
    - Stav zálohování protokolu za posledních 24 hodin pro databáze, které jsou v režimu úplného a hromadně protokolovaného obnovení a které jsou nakonfigurovány pro transakční zálohy protokolů.

6. Vyberte **obnovit**.

    ![Výběr obnovení](./media/backup-azure-sql-database/restore-db.png)

7. V části **obnovení konfigurace**určete, kam (nebo jak) se mají obnovit data:
   - **Alternativní umístění**: Obnovte databázi do alternativního umístění a zachovejte původní zdrojovou databázi.
   - **Přepsat databázi**: Obnovte data do stejné instance SQL Server jako původní zdroj. Tato možnost přepíše původní databázi.

    > [!IMPORTANT]
    > Pokud vybraná databáze patří do skupiny dostupnosti Always On, SQL Server neumožní přepsání databáze. K dispozici je pouze **alternativní umístění** .
    >
   - **Obnovit jako soubory**: místo obnovení jako databáze obnovte záložní soubory, které je možné obnovit jako databázi později v jakémkoli počítači, kde jsou soubory k dispozici, pomocí SQL Server Management Studio.
     ](./media/backup-azure-sql-database/restore-configuration.png) nabídce konfigurace ![obnovit

### <a name="restore-to-an-alternate-location"></a>Obnovit do alternativního umístění

1. V nabídce **obnovit konfiguraci** v části **kde se má obnovit**vyberte **alternativní umístění**.
2. Vyberte SQL Server název a instanci, do které chcete obnovit databázi.
3. Do pole **Název obnovené** databáze zadejte název cílové databáze.
4. Pokud je to možné, vyberte **přepsat, pokud databáze se stejným názvem už ve vybrané instanci SQL existuje**.
5. Vyberte **OK**.

    ![Zadejte hodnoty pro nabídku obnovit konfiguraci.](./media/backup-azure-sql-database/restore-configuration.png)

6. V **části vybrat bod obnovení**vyberte, jestli se má [obnovit k určitému bodu v čase](#restore-to-a-specific-point-in-time) , nebo jestli se má [obnovit na určitý bod obnovení](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Obnovení k bodu v čase je k dispozici pouze pro zálohy protokolu pro databáze, které jsou v režimu úplného a hromadně protokolovaného obnovení.

### <a name="restore-and-overwrite"></a>Obnovit a přepsat

1. V nabídce **obnovit konfiguraci** v části **kde se má obnovení**vyberte **přepsat databázi** > **OK**.

    ![Vybrat přepsat databázi](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. V **části vybrat bod obnovení**vyberte **protokoly (časové** okamžiky), které chcete [obnovit do konkrétního bodu v čase](#restore-to-a-specific-point-in-time). Případně můžete vybrat možnost **úplného rozdílu &** pro obnovení do [konkrétního bodu obnovení](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Obnovení k bodu v čase je k dispozici pouze pro zálohy protokolu pro databáze, které jsou v režimu úplného a hromadně protokolovaného obnovení.

### <a name="restore-as-files"></a>Obnovit jako soubory

Chcete-li obnovit data zálohy jako soubory. bak místo databáze, vyberte možnost **Obnovit jako soubory**. Jakmile jsou soubory v zadané cestě, můžete tyto soubory přenést do libovolného počítače, kde je chcete obnovit jako databázi. Díky možnosti přesouvat tyto soubory do libovolného počítače teď můžete data obnovit v rámci předplatných a oblastí.

1. V nabídce **obnovit konfiguraci** v části **kde se má obnovení**vybrat **Obnovit jako soubory**.
2. Vyberte název SQL Server, pro který chcete obnovit záložní soubory.
3. V **cílové cestě na serveru** zadejte cestu ke složce na serveru vybrané v kroku 2. Toto je umístění, ve kterém bude služba vypsat všechny nezbytné soubory zálohy. Cesta ke sdílené složce v síti, nebo cesta připojené sdílené složky Azure, pokud je zadaná jako cílová cesta, umožňuje snazší přístup k těmto souborům jiným počítačům ve stejné síti nebo se stejnou sdílenou složkou Azure, která je v nich namontovaná.<BR>

    >Pokud chcete obnovit záložní soubory databáze ve sdílené složce Azure připojené k cílovému registrovanému virtuálnímu počítači, ujistěte se, že NT AUTHORITY\SYSTEM má přístup ke sdílené složce. Pomocí níže uvedených kroků můžete udělit oprávnění ke čtení a zápisu pro službu AFS připojenou k virtuálnímu počítači:
    >
    >- Spuštění `PsExec -s cmd` pro zadání NT AUTHORITY\SYSTEM Shell
    >   - Spusťte příkaz `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`.
    >   - Ověřit přístup pomocí `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Zahájí obnovení souborů z trezoru služby Backup, aby se `\\<storageacct>.file.core.windows.net\<filesharename>` jako cesta.<BR>
    PsExec můžete stáhnout přes <https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Vyberte **OK**.

    ![Vybrat obnovit jako soubory](./media/backup-azure-sql-database/restore-as-files.png)

5. Vyberte **bod obnovení** odpovídající, pro který budou obnoveny všechny dostupné soubory. bak.

    ![Vybrat bod obnovení](./media/backup-azure-sql-database/restore-point.png)

6. Všechny záložní soubory přidružené k vybranému bodu obnovení jsou dumpingové do cílové cesty. Soubory můžete obnovit jako databázi na jakémkoli počítači, na kterém se nachází, pomocí SQL Server Management Studio.

    ![Obnovené záložní soubory v cílové cestě](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Obnovení k určitému bodu v čase

Pokud jste jako typ obnovení vybrali možnost **protokoly (časový okamžik)** , udělejte toto:

1. V části **Datum/čas obnovení**otevřete kalendář. V kalendáři se data, která mají body obnovení, zobrazují tučně a aktuální datum je zvýrazněno.
1. Vyberte datum, které obsahuje body obnovení. Nemůžete vybrat data, která nemají žádné body obnovení.

    ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Po výběru data se v grafu časové osy zobrazí dostupné body obnovení v souvislém rozsahu.
1. Zadejte čas pro obnovení na grafu časové osy nebo vyberte čas. Pak vyberte **OK**.

    ![Vyberte čas obnovení.](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. Pokud chcete po obnovení zachovat databázi neprovozované v nabídce **Upřesnit konfiguraci** , povolte **obnovení pomocí NORECOVERY**.
1. Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadejte novou cílovou cestu.
1. Vyberte **OK**.

    ![Nabídka Upřesnit konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. V nabídce **obnovit** vyberte **obnovit** a spusťte úlohu obnovení.
1. Sledujte průběh obnovy v oblasti **oznámení** nebo ho Sledujte výběrem možnosti **obnovit úlohy** v nabídce databáze.

    ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Obnovit na určitý bod obnovení

Pokud jste jako typ obnovení vybrali možnost **úplný & rozdíl** , udělejte toto:

1. Vyberte bod obnovení ze seznamu a kliknutím na **tlačítko OK** dokončete postup bodu obnovení.

    ![Zvolit úplný bod obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > Ve výchozím nastavení se zobrazí body obnovení za posledních 30 dní. Kliknutím na tlačítko **filtrovat** a vybrat vlastní rozsah můžete zobrazit body obnovení starší než 30 dní.

1. Pokud chcete po obnovení zachovat databázi neprovozované v nabídce **Upřesnit konfiguraci** , povolte **obnovení pomocí NORECOVERY**.
1. Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadejte novou cílovou cestu.
1. Vyberte **OK**.

    ![Nabídka Upřesnit konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. V nabídce **obnovit** vyberte **obnovit** a spusťte úlohu obnovení.
1. Sledujte průběh obnovy v oblasti **oznámení** nebo ho Sledujte výběrem možnosti **obnovit úlohy** v nabídce databáze.

    ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Obnovení databází s velkým počtem souborů

Pokud je celková velikost řetězce souborů v databázi větší než [určitý limit](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), Azure Backup ukládá seznam databázových souborů v různých součástech, takže během operace obnovení nebude možné nastavit cílovou cestu pro obnovení. Místo toho se soubory obnoví na výchozí cestu SQL.

  ![Obnovení databáze s velkým souborem](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Další kroky

[Správa a monitorování](manage-monitor-sql-database-backup.md) SQL Server databáze zálohované pomocí Azure Backup.
