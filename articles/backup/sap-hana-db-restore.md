---
title: Obnovení databází SAP HANA na virtuálních počítačích Azure
description: V tomto článku zjistíte, jak obnovit SAP HANA databáze, které běží na Azure Virtual Machines. K obnovení databází do sekundární oblasti můžete také použít obnovení mezi oblastmi.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c502b7741acd343baefe5e2bf8b95cfc02e46688
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021669"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Obnovení databází SAP HANA na virtuálních počítačích Azure

Tento článek popisuje, jak obnovit SAP HANA databáze běžící na virtuálním počítači Azure, který je službou Azure Backup zálohovaný do trezoru služby Recovery Services. Obnovení lze použít k vytvoření kopií dat pro scénáře vývoje a testování nebo k návratu do předchozího stavu.

Další informace o tom, jak zálohovat SAP HANA databáze, najdete v tématu [zálohování databází SAP HANA na virtuálních počítačích Azure](./backup-azure-sap-hana-database.md).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Obnovení k určitému bodu v čase nebo na bod obnovení

Azure Backup může obnovit databáze SAP HANA, které běží na virtuálních počítačích Azure, takto:

* Obnoví konkrétní datum nebo čas (za sekundu) pomocí záloh protokolu. Azure Backup automaticky určuje odpovídající úplné a rozdílové zálohy a řetěz záloh protokolů, které jsou nutné k obnovení na základě vybraného času.

* Obnovení do konkrétního úplného nebo rozdílového zálohování pro obnovení do konkrétního bodu obnovení.

## <a name="prerequisites"></a>Požadavky

Před obnovením databáze mějte na paměti následující:

* Databázi můžete obnovit pouze do instance SAP HANA, která je ve stejné oblasti.

* Cílová instance musí být zaregistrovaná ve stejném trezoru jako zdroj.

* Azure Backup nemůže na jednom virtuálním počítači identifikovat dvě různé instance SAP HANA. Proto není možné obnovit data z jedné instance do druhé na stejném virtuálním počítači.

* Pokud chcete mít jistotu, že je cílová instance SAP HANA připravená k obnovení, zkontrolujte její stav **připravenosti na zálohování** :

  1. Otevřete trezor, ve kterém je instance cílového SAP HANA zaregistrována.

  1. Na řídicím panelu trezoru v části **Začínáme** klikněte na **zálohovat**.

      ![Zálohování na řídicím panelu trezoru](media/sap-hana-db-restore/getting-started-backup.png)

  1. V části **zálohování** v části **co chcete zálohovat?** na **virtuálním počítači Azure vyberte SAP HANA**.

      ![Výběr SAP HANA na virtuálním počítači Azure](media/sap-hana-db-restore/sap-hana-backup.png)

  1. V části **zjišťování databáze na virtuálních počítačích** vyberte **Zobrazit podrobnosti**.

      ![Zobrazit podrobnosti](media/sap-hana-db-restore/view-details.png)

  1. Zkontrolujte **připravenost na zálohování** cílového virtuálního počítače.

      ![Chráněné servery](media/sap-hana-db-restore/protected-servers.png)

* Další informace o typech obnovení, které SAP HANA podporuje, najdete v SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="restore-a-database"></a>Obnovení databáze

K obnovení potřebujete následující oprávnění:

* Oprávnění **operátora zálohování** v trezoru, ve kterém provádíte obnovení.
* **Přispěvatel (Write)** přístup ke ZDROJOVÉmu virtuálnímu počítači, který se zálohuje.
* **Přispěvatel (zápis**) k CÍLOVÉmu virtuálnímu počítači:
  * Pokud provádíte obnovení do stejného virtuálního počítače, jedná se o zdrojový virtuální počítač.
  * Pokud provádíte obnovení do alternativního umístění, jedná se o nový cílový virtuální počítač.

1. Otevřete trezor, ve kterém se má zaregistrovat SAP HANA databáze, která se má obnovit.

1. Na řídicím panelu trezoru v části **chráněné položky** vyberte **zálohované položky** .

    ![Zálohované položky](media/sap-hana-db-restore/backup-items.png)

1. V části **zálohované položky** v části **typ správy zálohování** vyberte **SAP HANA na virtuálním počítači Azure** .

    ![Typ správy zálohování](media/sap-hana-db-restore/backup-management-type.png)

1. Vyberte databázi, která se má obnovit.

    ![Databáze k obnovení](media/sap-hana-db-restore/database-to-restore.png)

1. Projděte si nabídku databáze. Poskytuje informace o zálohování databáze, včetně:

    * Nejstarší a nejnovější body obnovení

    * Stav zálohování protokolu za posledních 24 a 72 hodin pro databázi

    ![Nabídka databáze](media/sap-hana-db-restore/database-menu.png)

1. Vybrat **obnovit databázi**

1. V části **obnovení konfigurace** určete, kam (nebo jak) se mají obnovit data:

    * **Alternativní umístění**: Obnovte databázi do alternativního umístění a zachovejte původní zdrojovou databázi.

    * **Přepsat databázi**: Obnovte data do stejné instance SAP HANA jako původní zdroj. Tato možnost přepíše původní databázi.

      ![Obnovit konfiguraci](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Obnovit do alternativního umístění

1. V nabídce **obnovit konfiguraci** v části **kde se má obnovit** vyberte **alternativní umístění**.

    ![Obnovit do alternativního umístění](media/sap-hana-db-restore/restore-alternate-location.png)

1. Vyberte SAP HANA název hostitele a název instance, pro které chcete obnovit databázi.
1. Ujistěte se, jestli je cílová instance SAP HANA připravená k obnovení, tím, že zajistíte její **připravenost na zálohování.** Další podrobnosti najdete v [části požadavky](#prerequisites) .
1. Do pole **Název obnovené** databáze zadejte název cílové databáze.

    > [!NOTE]
    > Obnovení kontejneru Izolovaná databáze (SDC) musí splňovat tyto [kontroly](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

1. Pokud je to možné, vyberte **přepsat, pokud databáze se stejným názvem už ve vybrané instanci Hana existuje**.
1. Vyberte **OK**.

    ![Obnovit konfiguraci – finální obrazovka](media/sap-hana-db-restore/restore-configuration-last.png)

1. V **části vybrat bod obnovení** vyberte **protokoly (časové** okamžiky), které chcete [obnovit do konkrétního bodu v čase](#restore-to-a-specific-point-in-time). Případně můžete vybrat možnost **úplného rozdílu &** pro [obnovení do konkrétního bodu obnovení](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Obnovit a přepsat

1. V nabídce **obnovit konfiguraci** v části **kde se má obnovení** vyberte **přepsat databázi**  >  **OK**.

    ![Přepsání databáze](media/sap-hana-db-restore/overwrite-db.png)

1. V **části vybrat bod obnovení** vyberte **protokoly (časové** okamžiky), které chcete [obnovit do konkrétního bodu v čase](#restore-to-a-specific-point-in-time). Případně můžete vybrat možnost **úplného rozdílu &** pro [obnovení do konkrétního bodu obnovení](#restore-to-a-specific-recovery-point).

### <a name="restore-as-files"></a>Obnovit jako soubory

Chcete-li obnovit data zálohy jako soubory místo databáze, vyberte možnost **Obnovit jako soubory**. Jakmile jsou soubory v zadané cestě, můžete tyto soubory převést na libovolný SAP HANA počítač, ve kterém je chcete obnovit jako databázi. Vzhledem k tomu, že můžete tyto soubory přesunout na libovolný počítač, teď můžete data obnovit v rámci předplatných a oblastí.

1. V nabídce **obnovit konfiguraci** v části **kde a postup obnovení** vyberte **Obnovit jako soubory**.
1. Vyberte název serveru **Host** /Hana, na který chcete záložní soubory obnovit.
1. V **cílové cestě na serveru** zadejte cestu ke složce na serveru, který jste vybrali v kroku 2. Toto je umístění, ve kterém bude služba vypsat všechny nezbytné soubory zálohy.

    Soubory, které jsou dumpingové, jsou:

    * Soubory zálohy databáze
    * Soubory katalogu
    * Soubory metadat JSON (pro každý záložní soubor, který je součástí)

    Cesta ke sdílené složce v síti, nebo cesta připojené sdílené složky Azure, pokud je zadaná jako cílová cesta, umožňuje snazší přístup k těmto souborům jiným počítačům ve stejné síti nebo se stejnou sdílenou složkou Azure, která je v nich namontovaná.

    >[!NOTE]
    >Pokud chcete obnovit záložní soubory databáze ve sdílené složce Azure připojené k cílovému zaregistrovanému virtuálnímu počítači, ujistěte se, že má kořenový účet oprávnění ke čtení a zápisu ve sdílené složce Azure.

    ![Zvolit cílovou cestu](media/sap-hana-db-restore/restore-as-files.png)

1. Vyberte **bod obnovení** odpovídající, který bude obnoven ze všech zálohovaných souborů a složek.

    ![Vybrat bod obnovení](media/sap-hana-db-restore/select-restore-point.png)

1. Všechny záložní soubory přidružené k vybranému bodu obnovení jsou dumpingové do cílové cesty.
1. V závislosti na typu vybraného bodu obnovení (**bodu v čase** nebo **úplném & rozdílu**) uvidíte v cílové cestě jednu nebo více složek. Jedna z složek s názvem `Data_<date and time of restore>` obsahuje úplné a rozdílové zálohy a další složka s názvem `Log` obsahuje zálohy protokolu.
1. Přesuňte obnovené soubory na Server SAP HANA, kde je chcete obnovit jako databázi.
1. Potom postupujte podle těchto kroků:
    1. Nastavte oprávnění pro složku nebo adresář, ve kterém se záložní soubory ukládají, pomocí následujícího příkazu:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. Spusťte další sadu příkazů jako `<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. Vygenerujte soubor katalogu pro obnovení. Extrahujte **BackupId** ze souboru metadat JSON pro úplnou zálohu, která bude později použita v rámci operace obnovení. Zajistěte, aby úplné zálohy a zápisy protokolů byly v různých složkách a odstranily soubory katalogu a soubory metadat JSON v těchto složkách.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        V příkazu výše:

        * `<DataFileDir>` – Složka obsahující úplné zálohy
        * `<LogFilesDir>` – Složka, která obsahuje zálohy protokolu
        * `<PathToPlaceCatalogFile>` – Složka, ve které se musí umístit vygenerovaný katalogový soubor

    1. Obnovte pomocí nově vygenerovaného souboru katalogu přes HANA Studio nebo spusťte dotaz HDBSQL Restore s tímto nově vygenerovaným katalogem. Dotazy na HDBSQL jsou uvedeny níže:

    * Obnovení k určitému bodu v čase:

        Pokud vytváříte novou obnovenou databázi, spusťte příkaz HDBSQL a vytvořte novou databázi `<DatabaseName>` a potom zastavte databázi pro obnovení. Pokud však obnovujete pouze existující databázi, zastavte databázi spuštěním příkazu HDBSQL.

        Pak spusťte následující příkaz pro obnovení databáze:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` – Název nové databáze nebo existující databáze, kterou chcete obnovit.
        * `<Timestamp>` – Přesné časové razítko obnovení bodu v čase
        * `<DatabaseName@HostName>` – Název databáze, jejíž zálohování se používá k obnovení, a název **hostitelského** /SAP HANA serveru, na kterém je umístěna Tato databáze. `USING SOURCE <DatabaseName@HostName>`Možnost určuje, že zálohování dat (používané pro obnovení) je databáze s jiným identifikátorem SID nebo názvem, než je cílový SAP HANA počítač. Takže není potřeba ho zadat pro obnovení na stejném serveru HANA, ze kterého se provádí zálohování.
        * `<PathToGeneratedCatalogInStep3>` -Cesta k souboru katalogu vygenerovanému v **kroku C**
        * `<DataFileDir>` – Složka obsahující úplné zálohy
        * `<LogFilesDir>` – Složka, která obsahuje zálohy protokolu
        * `<BackupIdFromJsonFile>` – **BackupId** extrahovaný v **kroku C**

    * Postup obnovení do konkrétního úplného nebo rozdílového zálohování:

        Pokud vytváříte novou obnovenou databázi, spusťte příkaz HDBSQL a vytvořte novou databázi `<DatabaseName>` a potom zastavte databázi pro obnovení. Pokud však obnovujete pouze existující databázi, zastavte databázi spuštěním příkazu HDBSQL:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` – název nové databáze nebo existující databáze, kterou chcete obnovit.
        * `<Timestamp>` – přesné časové razítko obnovení bodu v čase
        * `<DatabaseName@HostName>` – název databáze, jejíž zálohování se používá k obnovení, a název **hostitelského** /SAP HANA serveru, na kterém se nachází tato databáze. `USING SOURCE <DatabaseName@HostName>`Možnost určuje, že zálohování dat (používané pro obnovení) je databáze s jiným identifikátorem SID nebo názvem, než je cílový SAP HANA počítač. Proto není nutné zadávat pro obnovení na stejném serveru HANA, ze kterého se provádí zálohování.
        * `<PathToGeneratedCatalogInStep3>` – Cesta k souboru katalogu vygenerovanému v **kroku C**
        * `<DataFileDir>` – Složka obsahující úplné zálohy
        * `<LogFilesDir>` – Složka, která obsahuje zálohy protokolu
        * `<BackupIdFromJsonFile>` – **BackupId** extrahovaný v **kroku C**

### <a name="restore-to-a-specific-point-in-time"></a>Obnovení k určitému bodu v čase

Pokud jste jako typ obnovení vybrali možnost **protokoly (časový okamžik)** , udělejte toto:

1. Vyberte bod obnovení z grafu protokolu a výběrem možnosti **OK** zvolte bod obnovení.

    ![Bod obnovení](media/sap-hana-db-restore/restore-point.png)

1. V nabídce **obnovit** vyberte **obnovit** a spusťte úlohu obnovení.

    ![Výběr obnovení](media/sap-hana-db-restore/restore-restore.png)

1. Sledujte průběh obnovy v **oznamovací** oblasti nebo ji Sledujte výběrem možnosti **obnovit úlohy** v nabídce databáze.

    ![Obnovení se úspěšně aktivovalo.](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Obnovit na určitý bod obnovení

Pokud jste jako typ obnovení vybrali možnost **úplný & rozdíl** , udělejte toto:

1. Vyberte bod obnovení ze seznamu a vyberte možnost **OK** a zvolte bod obnovení.

    ![Obnovit určitý bod obnovení](media/sap-hana-db-restore/specific-recovery-point.png)

1. V nabídce **obnovit** vyberte **obnovit** a spusťte úlohu obnovení.

    ![Spustit úlohu obnovení](media/sap-hana-db-restore/restore-specific.png)

1. Sledujte průběh obnovy v **oznamovací** oblasti nebo ji Sledujte výběrem možnosti **obnovit úlohy** v nabídce databáze.

    ![Průběh obnovování](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > V rámci více databázových kontejnerů (MDC) se obnoví po obnovení systémové databáze do cílové instance jedna musí znovu spustit skript před registrací. Jenom potom se obnoví i následné obnovení databáze tenanta. Další informace najdete v tématu [řešení potíží – MDC Restore](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="cross-region-restore"></a>Obnovení mezi oblastmi

Jedna z možností obnovení (CRR) vám umožňuje obnovit SAP HANA databáze hostované na virtuálních počítačích Azure v sekundární oblasti, která je spárována se službou Azure.

Pokud chcete připojit funkci během verze Preview, přečtěte si [část než začnete](./backup-create-rs-vault.md#set-cross-region-restore).

Pokud chcete zjistit, jestli je CRR povolený, postupujte podle pokynů v tématu [Konfigurace obnovení mezi oblastmi](backup-create-rs-vault.md#configure-cross-region-restore) .

### <a name="view-backup-items-in-secondary-region"></a>Zobrazit zálohované položky v sekundární oblasti

Pokud je povolená možnost CRR, můžete zobrazit zálohované položky v sekundární oblasti.

1. Na portálu přejdete do části **Recovery Services trezoru**  >  **zálohované položky**.
1. Vyberte **Sekundární oblast** pro zobrazení položek v sekundární oblasti.

>[!NOTE]
>V seznamu se zobrazí pouze typy správy zálohování podporující funkci CRR. V současné době je povolena pouze podpora obnovení dat sekundární oblasti do sekundární oblasti.

![Zálohované položky v sekundární oblasti](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![Databáze v sekundární oblasti](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Obnovení v sekundární oblasti

Funkce obnovení koncového uživatele v sekundární oblasti bude podobná primární oblasti obnovení uživatelského prostředí. Při konfiguraci podrobností v podokně obnovit konfiguraci pro konfiguraci obnovení se zobrazí výzva k zadání pouze sekundárních parametrů oblasti.

![Kde a jak obnovit](./media/sap-hana-db-restore/restore-secondary-region.png)

>[!NOTE]
>Virtuální síť v sekundární oblasti musí být přiřazena jedinečně a nelze ji použít pro žádné jiné virtuální počítače v dané skupině prostředků.

![Oznámení o průběhu obnovení aktivační události](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>* Po aktivaci obnovení a ve fázi přenosu dat nelze úlohu obnovení zrušit.
>* Role Azure potřebné k obnovení v sekundární oblasti jsou stejné jako v primární oblasti.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorování úloh obnovení sekundární oblasti

1. Na portálu přejdete na   >  **úlohy zálohování** služby Recovery Services trezor.
1. Vyberte **Sekundární oblast** pro zobrazení položek v sekundární oblasti.

    ![Filtrované úlohy zálohování](./media/sap-hana-db-restore/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Další kroky

* [Naučte](sap-hana-db-manage.md) se spravovat SAP HANA databází zálohovaných pomocí Azure Backup
