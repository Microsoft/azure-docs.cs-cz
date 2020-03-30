---
title: Obnovení databází SQL Serveru na virtuálním počítači Azure
description: Tento článek popisuje, jak obnovit databáze SQL Serveru, které běží na virtuálním počítači Azure a které jsou zálohovány pomocí Azure Backup.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252451"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Obnovení databází SQL Serveru na virtuálních počítačích Azure

Tento článek popisuje, jak obnovit databázi SQL Serveru, která běží na virtuálním počítači Azure (VM), který služba [Azure Backup](backup-overview.md) zálohovala do trezoru služby Obnovení zálohování Azure.

Tento článek popisuje, jak obnovit databáze serveru SQL Server. Další informace najdete [v tématu Zálohování databází serveru SQL Server na virtuálních počítačích Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Obnovení času nebo bodu obnovení

Azure Backup můžete obnovit SQL Server databází, které běží na virtuálních počítačích Azure takto:

- Obnovení na určité datum nebo čas (na druhý) pomocí záloh protokolu transakcí. Azure Backup automaticky určuje příslušné úplné rozdílové zálohy a řetězce záloh protokolu, které jsou nutné k obnovení na základě vybraného času.
- Obnovení konkrétní úplné nebo rozdílové zálohy obnovit do určitého bodu obnovení.

## <a name="prerequisites"></a>Požadavky

Před obnovením databáze si všimněte následujícího:

- Databázi můžete obnovit do instance serveru SQL Server ve stejné oblasti Azure.
- Cílový server musí být zaregistrován do stejného úložiště jako zdroj.
- Chcete-li obnovit databázi šifrovanou tde na jiný server SQL Server, je třeba nejprve [obnovit certifikát na cílovém serveru](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Před obnovením "hlavní" databáze spusťte instanci serveru SQL Server v režimu jednoho uživatele pomocí možnosti spuštění **-m AzureWorkloadBackup**.
  - Hodnota pro **-m** je název klienta.
  - Připojení může otevřít pouze zadaný název klienta.
- Pro všechny systémové databáze (model, hlavní, msdb) zastavte službu SQL Server Agent před aktivací obnovení.
- Zavřete všechny aplikace, které se mohou pokusit převzít připojení k některé z těchto databází.
- Pokud máte na serveru spuštěno více instancí, měly by být všechny instance spuštěny, jinak by se server neobjevil v seznamu cílových serverů, na které byste mohli obnovit databázi.

## <a name="restore-a-database"></a>Obnovení databáze

Chcete-li obnovit, potřebujete následující oprávnění:

- **Oprávnění operátora zálohování** v úschovně, kde provádíte obnovení.
- **Přispěvatel (zápis)** přístup ke zdrojovému virtuálnímu virtuálnímu mněmu, který je zálohován.
- **Přispěvatel (zápis)** přístup k cílovému virtuálnímu virtuálnímu virtuálnímu mněmu:
  - Pokud obnovujete stejný virtuální virtuální ms, tohle je zdrojový virtuální virtuální.
  - Pokud obnovujete do alternativního umístění, jedná se o nový cílový virtuální virtuální virtuální ms.

Obnovení následujícím způsobem:

1. Otevřete úschovnu, ve které je virtuální modul SQL Server registrován.
2. Na řídicím panelu úschovny vyberte v části **Využití** **položku Zálohování položek**.
3. V **části Položky zálohování**vyberte v části Typ správy **zálohování**sql **ve virtuálním počítači Azure**.

    ![Výběr SQL ve virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Vyberte databázi, kterou chcete obnovit.

    ![Vyberte databázi, kterou chcete obnovit.](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Zkontrolujte nabídku databáze. Poskytuje informace o zálohování databáze, včetně:

    - Nejstarší a nejnovější body obnovení.
    - Stav zálohování protokolu za posledních 24 hodin pro databáze, které jsou v režimu úplného a zaznamenáného hromadného protokolu a které jsou nakonfigurovány pro zálohy transakčních protokolů.

6. Vyberte **obnovit**.

    ![Vybrat obnovit](./media/backup-azure-sql-database/restore-db.png)

7. V **části Konfigurace obnovení**určete, kde (nebo jak) chcete data obnovit:
   - **Alternativní umístění**: Obnovení databáze do alternativního umístění a zachovat původní zdrojovou databázi.
   - **Přepsat DB**: Obnovte data do stejné instance serveru SQL Server jako původní zdroj. Tato možnost přepíše původní databázi.

    > [!IMPORTANT]
    > Pokud vybraná databáze patří do skupiny dostupnosti vždy na, SQL Server neumožňuje přepsání databáze. K dispozici je pouze **alternativní umístění.**
    >
   - **Obnovení jako soubory**: Namísto obnovení jako databáze, obnovit záložní soubory, které lze obnovit jako databáze později na libovolném počítači, kde jsou k dispozici soubory pomocí SQL Server Management Studio.
     ![Nabídka Obnovit konfiguraci](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Obnovení do alternativního umístění

1. V nabídce **Obnovit konfiguraci** vyberte v části **Kde obnovit** **položku Alternativní umístění**.
2. Vyberte název a instanci serveru SQL Server, do kterých chcete databázi obnovit.
3. Do pole **Obnovený název db** zadejte název cílové databáze.
4. Pokud je to možné, vyberte **Přepsat, pokud db se stejným názvem již existuje na vybrané instanci SQL**.
5. Vyberte **OK**.

    ![Zadání hodnot pro nabídku Obnovit konfiguraci](./media/backup-azure-sql-database/restore-configuration.png)

6. V **povolte v posuzovacím bodu Výběr,** zda chcete [obnovit určitý bod v čase](#restore-to-a-specific-point-in-time) nebo obnovit určitý bod [obnovení](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Obnovení bodu v čase je k dispozici pouze pro zálohy protokolů pro databáze, které jsou v režimu úplného a zaznamenáného hromadného obnovení.

### <a name="restore-and-overwrite"></a>Obnovení a přepsání

1. V nabídce **Obnovit konfiguraci** vyberte v části **Kde obnovit**možnost **Přepsat DB** > **OK**.

    ![Vybrat přepsat DB](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. V **povolte Select Restore point** **vyberte Protokoly (Bod v čase),** chcete-li [obnovit určitý bod v čase](#restore-to-a-specific-point-in-time). Nebo vyberte **možnost Plný & Rozdíl,** chcete-li obnovit [určitý bod obnovení](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Obnovení bodu v čase je k dispozici pouze pro zálohy protokolů pro databáze, které jsou v režimu úplného a zaznamenáného hromadného obnovení.

### <a name="restore-as-files"></a>Obnovit jako soubory

Chcete-li obnovit záložní data jako soubory .bak místo databáze, zvolte **Obnovit jako soubory**. Jakmile jsou soubory vypovězeny do zadané cesty, můžete tyto soubory převést do libovolného počítače, kde je chcete obnovit jako databázi. Díky možnosti přesunout tyto soubory do libovolného počítače, nyní můžete obnovit data napříč odběry a oblastmi.

1. V nabídce **Obnovit konfiguraci** vyberte v části **Kde obnovit**možnost Obnovit **jako soubory**.
2. Vyberte název serveru SQL Server, ke kterému chcete obnovit záložní soubory.
3. V **cílové cestě na serveru** zadejte cestu ke složce na serveru vybraném v kroku 2. Toto je umístění, kde služba vypíše všechny potřebné záložní soubory. Cesta ke sdílené složce sítě nebo cesta připojené sdílené složky Azure, pokud je zadána jako cílová cesta, obvykle umožňuje snadnější přístup k těmto souborům jinými počítači ve stejné síti nebo se stejnou sdílenou složkou Azure, která je na nich připojena.<BR>

    >Chcete-li obnovit záložní soubory databáze na sdílené složce Azure připojené k cílovému registrovanému virtuálnímu počítači, ujistěte se, že NT AUTHORITY\SYSTEM má přístup ke sdílené složce. Můžete provést kroky uvedené níže udělit oprávnění pro čtení a zápis afs připojené na virtuálním počítači:
    >
    >- Spustit `PsExec -s cmd` pro vstup do prostředí NT AUTHORITY\SYSTEM
    >   - Spusťte příkaz `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`.
    >   - Ověření přístupu pomocí`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Nastartovat obnovení jako soubory z `\\<storageacct>.file.core.windows.net\<filesharename>` úložiště zálohování jako cesta<BR>
    Zde si můžete stáhnout Psexec přes<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Vyberte **OK**.

    ![Vybrat obnovit jako soubory](./media/backup-azure-sql-database/restore-as-files.png)

5. Vyberte bod obnovení odpovídající **bodu obnovení,** do kterého budou obnoveny všechny dostupné soubory .bak.

    ![Výběr bodu obnovení](./media/backup-azure-sql-database/restore-point.png)

6. Všechny záložní soubory přidružené k vybranému bodu obnovení jsou uloženy do cílové cesty. Soubory můžete obnovit jako databázi v libovolném počítači, ve které jsou přítomny pomocí aplikace SQL Server Management Studio.

    ![Obnovené záložní soubory v cílové cestě](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Obnovení do určitého bodu v čase

Pokud jste jako typ obnovení vybrali **protokoly (Bod v čase),** postupujte takto:

1. V části **Obnovit datum a čas**otevřete kalendář. V kalendáři jsou data, která mají body obnovení, zobrazena tučným písmem a je zvýrazněno aktuální datum.
1. Vyberte datum, které má body obnovení. Nelze vybrat data, která nemají žádné body obnovení.

    ![Otevření kalendáře](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Po výběru data se v grafu časové osy zobrazí dostupné body obnovení v souvislém rozsahu.
1. Zadejte čas pro obnovení v grafu časové osy nebo vyberte čas. Pak vyberte **OK**.

    ![Výběr času obnovení](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. V nabídce **Rozšířená konfigurace,** pokud chcete zachovat databázi nefunkční po obnovení, povolte **obnovit pomocí NORECOVERY**.
1. Pokud chcete změnit umístění obnovení na cílovém serveru, zadejte novou cílovou cestu.
1. Vyberte **OK**.

    ![Rozšířená konfigurační nabídka](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. V nabídce **Restore** vyberte **Obnovit,** chcete-li spustit úlohu obnovení.
1. Sledujte průběh obnovení v oblasti **Oznámení** nebo jej sledujte výběrem **možnosti Obnovit úlohy** v nabídce databáze.

    ![Obnovit průběh úlohy](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Obnovení do určitého bodu obnovení

Pokud jste jako typ obnovení vybrali **možnost Full & Differential,** postupujte takto:

1. Vyberte bod obnovení ze seznamu a vyberte **OK,** abyste dokončili postup bodu obnovení.

    ![Zvolte úplný bod obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > Ve výchozím nastavení se zobrazí body obnovení za posledních 30 dní. Body obnovení starší než 30 dní můžete zobrazit kliknutím na **filtr** a výběrem vlastního rozsahu.

1. V nabídce **Rozšířená konfigurace,** pokud chcete zachovat databázi nefunkční po obnovení, povolte **obnovit pomocí NORECOVERY**.
1. Pokud chcete změnit umístění obnovení na cílovém serveru, zadejte novou cílovou cestu.
1. Vyberte **OK**.

    ![Nabídka Rozšířená konfigurace](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. V nabídce **Restore** vyberte **Obnovit,** chcete-li spustit úlohu obnovení.
1. Sledujte průběh obnovení v oblasti **Oznámení** nebo jej sledujte výběrem **možnosti Obnovit úlohy** v nabídce databáze.

    ![Obnovit průběh úlohy](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Obnovení databází s velkým počtem souborů

Pokud je celková velikost řetězce souborů v databázi větší než [určitý limit](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), azure backup ukládá seznam databázových souborů v jiné součásti jámy tak, že během operace obnovení nebudete moci nastavit cílovou cestu obnovení. Soubory budou místo toho obnoveny na výchozí cestu SQL.

  ![Obnovit databázi s velkým souborem](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Další kroky

[Správa a monitorování](manage-monitor-sql-database-backup.md) Databáze SQL Serveru, které jsou zálohovány službou Azure Backup.
