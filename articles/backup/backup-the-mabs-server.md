---
title: Zálohování serveru MABS
description: Naučte se zálohovat Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: de62f0f57273ad7bd77df917d909627819165adb
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946707"
---
# <a name="back-up-the-mabs-server"></a>Zálohování serveru MABS

Pokud chcete zajistit, aby se data obnovila, když Microsoft Azure Backup Server (MABS) selžou, budete potřebovat strategii pro zálohování serveru MABS. Pokud není zálohovaný, budete ho muset po selhání znovu sestavit ručně a body obnovení na disku nebudou obnovitelné. Servery MABS můžete zálohovat zálohováním databáze MABS.

## <a name="back-up-the-mabs-database"></a>Zálohování databáze MABS

Jako součást strategie zálohování MABS budete muset zálohovat databázi MABS. Databáze MABS má název DPMDB. Tato databáze obsahuje konfiguraci MABS spolu s daty o zálohách MABS. Pokud dojde k havárii, můžete znovu sestavit většinu funkcí serveru MABS pomocí poslední zálohy databáze. Za předpokladu, že je možné databázi obnovit, jsou k dispozici zálohy založené na pásce a udržují se všechna nastavení skupiny ochrany a plány zálohování. Pokud nedošlo k ovlivnění disků fondu úložiště MABS, jsou po opětovném sestavení použitelné i zálohy založené na disku. Databázi můžete zálohovat několika různými způsoby.

|Metoda zálohování databáze|Výhody|Nevýhody|
|--------------------------|--------------|-----------------|
|[Zálohování do Azure](#back-up-to-azure)|<li>Snadno nakonfigurovaná a monitorovaná v MABS.<li>Více umístění souborů zálohy databáze.<li>Úložiště v cloudu poskytuje robustní řešení pro zotavení po havárii.<li>Silně zabezpečené úložiště pro databázi.<li>Podporuje 120 online bodů obnovení.|<li>Vyžaduje účet Azure a další konfiguraci MABS. Způsobuje náklady pro úložiště Azure.<li> K získání přístupu k MABS zálohám uloženým v trezoru záloh Azure vyžaduje podporovaná verze systému Windows Server s agentem Azure. Nemůže to být jiný MABS Server.<li>Nelze ho použít, je-li databáze hostována místně a chcete povolit sekundární ochranu. <li>Započítány jsou některé další přípravy a čas na zotavení.|
|[Zálohování databáze zálohováním fondu úložiště MABS](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Jednoduchá konfigurace a sledování.<li>Záloha se uchovává na discích fondu úložiště MABS a je snadno přístupná místně.<li>MABS naplánované zálohy podporují 512 expresní úplné zálohování. Pokud zálohujete každou hodinu, budete mít plnou ochranu za 21 dnů.|<li>Není vhodný pro zotavení po havárii. Je online a obnovení nemusí fungovat podle očekávání, pokud server MABS nebo disk fondu úložiště selžou.<li>Nelze ho použít, je-li databáze hostována místně a chcete povolit sekundární ochranu. <li>K získání přístupu k bodům obnovení se vyžadují některé přípravy a speciální kroky, pokud služba MABS nebo konzola neběží nebo nepracují.|
|[Zálohování pomocí nativního zálohování serveru SQL Server na místní disk](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Integrováno do systému SQL Server.<li>Záloha se uchovává na místním disku, který je snadno přístupný.<li>Spuštěn můžete naplánovat tak často, jak se vám líbí.<li>Zcela nezávisle na MABS.<li>Můžete naplánovat vyčištění záložního souboru.|<li>Nepředstavuje dobrou možnost zotavení po havárii, nekopírují-li se zálohy do vzdáleného umístění.<li>Vyžaduje místní úložiště pro zálohování, které může omezit dobu uchovávání a četnosti.|
|[Zálohování pomocí nativního zálohování SQL a ochrany MABS do sdíleného úložiště chráněného MABS](#back-up-to-a-share-protected-by-mabs)|<li>Dá se snadno monitorovat v MABS.<li>Více umístění souborů zálohy databáze.<li>Snadno dostupné v libovolném počítači se systémem Windows v síti.<li>Potenciálně nejrychlejší způsob obnovení.|<li>Podporuje pouze 64 bodů obnovení.<li>Není vhodný pro zotavení pracovišť po havárii. MABS Server nebo MABS selhání disku fondu úložiště může bránit v úsilí o obnovení.<li>Není možnost, pokud je databáze MABS hostována místně a chcete povolit sekundární ochranu. <li>Pro konfiguraci a testování jsou nutné další přípravy.<li>Je potřeba určit nějakou dodatečnou přípravu a dobu obnovení, pokud je samotný server MABS mimo provoz, ale disky fondu úložiště MABS jsou přesné.|

- Pokud zálohujete pomocí skupiny ochrany MABS, doporučujeme, abyste pro databázi použili jedinečnou skupinu ochrany.

    > [!NOTE]
    > Pro účely obnovení musí být instalace MABS, kterou chcete obnovit, v databázi MABS shodná s verzí databáze MABS.  Pokud například databáze, kterou chcete obnovit, pochází z MABS v3 s instalací kumulativní aktualizace 1, musí mít server MABS spuštěnou stejnou verzi s kumulativní aktualizací 1. To znamená, že před obnovením databáze může být nutné odinstalovat a znovu nainstalovat MABS s kompatibilní verzí.  Pokud chcete zkontrolovat verzi databáze, může být potřeba ji ručně připojit k názvu dočasné databáze a potom spuštěním příkazu jazyka SQL v databázi zkontrolovat poslední nainstalované kumulativní aktualizace na základě hlavní verze a podverze.

- Pokud chcete zjistit verzi databáze MABS, postupujte podle těchto kroků:

    1. Pokud chcete spustit dotaz, otevřete SQL Management Studio a pak se připojte k instanci SQL, na které běží databáze MABS.

    2. Vyberte databázi MABS a potom spusťte nový dotaz.

    3. Následující dotaz SQL vložte do podokna dotazu a spusťte ho:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Pokud se ve výsledcích dotazu nic nevrátí, nebo pokud se server MABS Upgradoval z předchozích verzí, ale od té doby se nenainstalovala žádná nová kumulativní aktualizace, pro základní instalaci MABS se neobjeví žádná položka pro hlavní, vedlejší. Pokud chcete ověřit verze MABS přidružené k kumulativním aktualizacím, přečtěte si [seznam čísel buildů pro MABS](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx).

### <a name="back-up-to-azure"></a>Zálohování do Azure

1. Než začnete, budete muset spustit skript, který načte cestu k přípojnému bodu svazku repliky MABS, abyste věděli, který bod obnovení obsahuje zálohu MABS. Proveďte to po počáteční replikaci provedené pomocí služby Azure Backup. Ve skriptu nahraďte `dplsqlservername%` názvem SQL Server instance, která je hostitelem databáze MABS.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Ujistěte se, že máte heslo, které jste zadali při instalaci agenta Azure Recovery Services a server MABS byl zaregistrován v trezoru Azure Backup. Heslo budete potřebovat k obnovení zálohy.

2. Vytvořte trezor Azure Backup Vault a stáhněte si instalační soubor agenta Azure Backup a přihlašovací údaje trezoru. Spuštěním instalačního souboru nainstalujte agenta na server MABS a použijte přihlašovací údaje trezoru k registraci serveru MABS v trezoru. [Další informace](backup-azure-microsoft-azure-backup.md).

3. Po nakonfigurování trezoru nastavte skupinu ochrany MABS, která obsahuje databázi MABS. Tuto možnost vyberte, pokud se má zálohovat na disk a do Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>Obnovení databáze MABS z Azure

Databázi můžete obnovit z Azure pomocí libovolného serveru MABS, který je registrovaný v úložišti Azure Backup, a to následujícím způsobem:

1. V konzole MABS vyberte **obnovení**  >  **Přidat externí MABS**.

2. Zadejte přihlašovací údaje trezoru (Stáhněte si z trezoru Azure Backup). Upozorňujeme, že přihlašovací údaje jsou platné jenom dva dny.

3. V části **vybrat externí MABS pro obnovení**vyberte server MABS, pro který chcete obnovit databázi, zadejte šifrovací heslo a vyberte **OK.**

4. V seznamu dostupných bodů obnovení vyberte ten, který chcete použít. Vyberte možnost **Vymazat externí MABS** a vraťte se do místního zobrazení MABS.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Zálohování databáze MABS do fondu úložiště MABS

> [!NOTE]  
> Tato možnost je k dispozici pro MABS s Moderní úložiště zálohování.

1. V konzole MABS vyberte **ochrana**  >  **vytvořit skupinu ochrany**.
2. Na stránce **Vybrat typ skupiny ochrany** vyberte **servery**.
3. Na stránce **Vybrat členy skupiny** vyberte **databáze aplikace DPM**. Rozbalte server MABS a vyberte DPMDB.
4. Na stránce **Vybrat způsob ochrany dat** vyberte možnost **Chci krátkodobou ochranu pomocí disku**. Určete možnosti zásad krátkodobé ochrany.
5. Po počáteční replikaci databáze MABS spusťte následující skript SQL:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>Obnovení databáze MABS

Chcete-li rekonstruovat MABS se stejnou databází, je třeba nejprve obnovit databázi MABS a synchronizovat ji s čerstvě nainstalovaným MABS.

#### <a name="use-the-following-steps"></a>Použijte následující postup.

1. Otevřete příkazový řádek pro správu a spusťte příkaz a spusťte `psexec.exe -s powershell.exe` okno PowerShellu v kontextu systému.
2. Určete umístění, ze kterého chcete databázi obnovit:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Kopírování databáze z poslední zálohy

1. Přejít na cestu k virtuálnímu pevnému disku repliky `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Připojte soubor **Disk0. vhdx** , který je přítomný pomocí `mount-vhd disk0.vhdx` příkazu.
3. Jakmile je virtuální pevný disk repliky připojený, použijte `mountvol.exe` k přiřazení písmene jednotky svazku repliky pomocí fyzického ID repliky z výstupu skriptu SQL. Příklad: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Kopírování databáze z předchozího bodu obnovení

1. Přejděte do adresáře kontejnerů DPMDB  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . V rámci odpovídajících bodů obnovení pro MABS DB se zobrazí více adresářů s některými jedinečnými identifikátory identifikátorů GUID. Jiné adresáře reprezentují body obnovení.
2. Přejděte k libovolné cestě VHD `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` a připojte se k ní pomocí příkazu **Disk0. vhdx** . `mount-vhd disk0.vhdx`
3. Jakmile je virtuální pevný disk repliky připojený, použijte `mountvol.exe` k přiřazení písmene jednotky svazku repliky pomocí ID fyzické repliky z výstupu skriptu SQL. Příklad: `mountvol X: \?\Volume{}\`

   Všechny z podmínek, které se zobrazí ve výše uvedených krocích s úhlovými závorkami, jsou držitelé. Nahraďte je příslušnými hodnotami následujícím způsobem:
   - **ReFSVolume** – cesta k přístupu z výstupu skriptu SQL
   - **MABSSERVER FQDN** – plně kvalifikovaný název serveru MABS
   - **PhysicalReplicaId** – ID fyzické repliky z skriptu SQL
   - **PITId** -identifikátor GUID jiný než ID fyzické repliky v adresáři kontejneru.
4. Otevřete další příkazový řádek pro správu a spusťte `psexec.exe -s cmd.exe` příkazový řádek v kontextu systému.
5. Změňte adresář na jednotku X: a přejděte do umístění souborů databáze MABS.
6. Zkopírujte je do umístění, odkud je půjde snadno obnovit. Po zkopírování zavřete okno PsExec cmd.
7. Přejděte do okna prostředí PowerShell PsExec otevřené v kroku 1, přejděte do cesty VHDX a odpojte soubor VHDX pomocí příkazu `dismount-vhd disk0.vhdx` .
8. Po přeinstalaci serveru MABS můžete použít obnovený DPMDB pro připojení k serveru MABS spuštěním `DPMSYNC-RESTOREDB` příkazu.
9. Spuštění `DPMSYNC-SYNC` jednou `DPMSYNC-RESTOREDB` je dokončeno.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Zálohování databáze zálohováním fondu úložiště MABS

> [!NOTE]
> Tato možnost je k dispozici pro MABS se starší verzí úložiště.

Než začnete, budete muset spustit skript, který načte cestu k přípojnému bodu svazku repliky MABS, abyste věděli, který bod obnovení obsahuje zálohu MABS. Proveďte to po počáteční replikaci provedené pomocí služby Azure Backup. Ve skriptu nahraďte `dplsqlservername%` názvem SQL Server instance, která je hostitelem databáze MABS.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. V konzole MABS vyberte **ochrana**  >  **vytvořit skupinu ochrany**.

2. Na stránce **Vybrat typ skupiny ochrany** vyberte možnost **Servery**.

3. Na stránce **Vybrat členy skupiny** vyberte databázi MABS. Rozbalte položku Server MABS a vyberte možnost **DPMDB**.

4. Na stránce  **Vybrat způsob ochrany dat** vyberte možnost **Chci krátkodobou ochranu pomocí disku**. Určete možnosti zásad krátkodobé ochrany. Pro databáze MABS doporučujeme rozsah uchovávání dva týdny.

#### <a name="recover-the-database"></a>Obnovení databáze

Pokud je server MABS stále v provozu a fond úložiště je v pořádku (například problémy se službou nebo konzolou MABS), zkopírujte databázi ze svazku repliky nebo stínové kopie následujícím způsobem:

1. Rozhodněte se, z jakého období chcete databázi obnovit.

    - Pokud chcete zkopírovat databázi z poslední zálohy provedené přímo ze svazku repliky MABS, použijte **mountvol.exe** k přiřazení písmene jednotky svazku repliky pomocí identifikátoru GUID z výstupu skriptu SQL. Příklad: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Pokud chcete zkopírovat databázi z předchozího bodu obnovení (stínové kopie), musíte uvést všechny stínové kopie pro repliku pomocí identifikátoru GUID svazku z výstupu skriptu SQL. Tento příkaz vypíše stínové kopie pro daný svazek: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Poznamenejte si čas vytvoření a ID stínové kopie, ze které chcete provést obnovení.

2. Pak pomocí **diskshadow.exe** připojte stínovou kopii k nepoužitému písmenu jednotky X: pomocí ID stínové kopie, abyste mohli kopírovat soubory databáze.

3. Otevřete příkazový řádek pro správu a spusťte příkazový `psexec.exe -s cmd.exe` řádek v kontextu systému, abyste měli oprávnění k přechodu na svazek repliky (X:). a zkopírujte soubory.

4. Vložte disk CD na jednotku X: a přejděte do umístění souborů databáze MABS. Zkopírujte je do umístění, odkud je půjde snadno obnovit. Po dokončení kopírování existuje okno PsExec cmd a spusťte **diskshadow.exe** a vystavte si svazek X:.

5. Teď můžete obnovit soubory databáze pomocí SQL Management Studio nebo spuštěním příkazu **DpmSync \- restoredb**.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Zálohování pomocí nativního zálohování serveru SQL Server na místní disk

Databázi MABS můžete zálohovat na místní disk s nativním zálohováním SQL Server, nezávisle na MABS.

- Získejte [přehled](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) o zálohování SQL Serveru.

- Najděte [další informace](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) o zálohování SQL Serveru do cloudu.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Zálohování do sdíleného úložiště chráněného přes MABS

Tato možnost zálohování používá nativní SQL pro zálohování databáze MABS do sdílené složky, ochranu sdílené složky pomocí MABS a k usnadnění obnovení používá předchozí verze Windows VSS.

### <a name="before-you-start"></a>Než začnete

1. Na SQL Server vytvořte složku na disku s dostatkem volného místa pro uložení jedné kopie zálohy. Například: `C:\MABSBACKUP`.

1. Složku nasdílejte. Sdílejte například `C:\MABSBACKUP` složku jako *DPMBackup*.

1. Zkopírujte a vložte příkaz OSQL níže do poznámkového bloku a uložte ho do souboru s názvem `C:\MABSACKUP\bkupdb.cmd` . Ujistěte se, že neexistuje přípona. txt. Upravte SQL_Instance_name a DPMDB_NAME tak, aby odpovídaly instanci a názvu DPMDB používaného serverem MABS.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Pomocí poznámkového bloku otevřete **ScriptingConfig.xml** soubor umístěný ve `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` složce na serveru MABS.

1. Upravte **ScriptingConfig.xml** a změňte **zdroj dat** (úložiště DPMDBBACKUP) na písmeno jednotky, které obsahuje složku nebo sdílenou složku. Změňte položku PreBackupScript na úplnou cestu a název **BkUpDB. cmd** uložený v kroku 3.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Uložte změny **ScriptingConfig.xml**.

1. Chraňte složku C:\MABSBACKUP nebo `\sqlservername\MABSBACKUP` sdílenou složku pomocí MABS a počkejte na vytvoření počáteční repliky. Ve složce C:\MABSBACKUP by měl být **DPMDB. bak** v důsledku předzálohovacího skriptu spuštěného, který byl následně zkopírován do repliky MABS.

1. Pokud nepovolíte samoobslužné obnovení, budete potřebovat některé další kroky pro sdílení složky MABSBACKUP v replice:

    1. V konzole MABS > **Protection**vyhledejte zdroj dat MABSBACKUP a vyberte ho. V části Podrobnosti vyberte **kliknutím kliknutím zobrazíte podrobnosti** o odkazu na cestu k replice a zkopírujte cestu do poznámkového bloku. Odeberte zdrojovou cestu a uložte cílovou cestu. Cesta by měla vypadat nějak takto: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Vytvořte v této cestě sdílenou složku pomocí názvu sdílené složky **MABSSERVERNAME-DPMDB**. Můžete použít příkaz Net Share z příkazového řádku pro správu.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Nakonfigurujte zálohování

Databázi MABS můžete zálohovat stejně jako jakoukoli jinou SQL Server databázi pomocí SQL Server nativního zálohování.

- Získejte [přehled](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) o zálohování SQL Serveru.

- Najděte [další informace](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) o zálohování SQL Serveru do cloudu.

### <a name="recover-the-mabs-database"></a>Obnovení databáze MABS

1. Připojte se ke `\\MABSServer\MABSSERVERNAME-dpmdb` sdílené složce pomocí Průzkumníka z libovolného počítače se systémem Windows.

2. Klikněte pravým tlačítkem na soubor **DPMDB. bak** a zobrazte vlastnosti. Na kartě **Předchozí verze** se zobrazí všechny zálohy, které můžete vybrat a kopírovat. K dispozici je také velmi poslední záloha stále umístěná ve složce C:\MABSBACKUP, která je také snadno dostupná.

3. Pokud potřebujete přesunout disk MABS s připojením k síti SAN na jiný server, abyste mohli číst ze svazku repliky nebo přeinstalovat systém Windows ke čtení místně připojených disků, budete muset znát cestu k přípojnému bodu svazku repliky MABS nebo identifikátor GUID svazku, abyste věděli, jaký svazek obsahuje zálohu databáze. Skript SQL níže slouží k extrakci těchto informací kdykoli po počáteční ochraně, ale před potřebou obnovení. Nahraďte `%dpmsqlservername%` názvem SQL Server, který je hostitelem databáze.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. Pokud potřebujete provést obnovení po přesunutí disků fondu úložiště MABS nebo opětovném sestavení serveru MABS:

    1. Máte identifikátor GUID svazku, takže pokud se svazek musí připojit k jinému serveru Windows nebo po opětovném sestavení serveru MABS, použijte **mountvol.exe** k přiřazení písmene jednotky pomocí identifikátoru GUID svazku z výstupu skriptu SQL: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Znovu nasdílejte složku MABSBACKUP na svazku repliky pomocí písmene jednotky a části cesty repliky představující strukturu složek.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Připojte se ke `\\SERVERNAME\MABSSERVERNAME-dpmdb` sdílené složce pomocí Průzkumníka z libovolného počítače se systémem Windows.

    4. Klikněte pravým tlačítkem na soubor **DPMDB. bak** a zobrazte vlastnosti. Na kartě **Předchozí verze** se zobrazí všechny zálohy, které můžete vybrat a kopírovat.

## <a name="using-dpmsync"></a>Použití příkazu DPMSync

**DpmSync** je nástroj příkazového řádku, který umožňuje synchronizovat databázi MABS se stavem disků ve fondu úložiště a s nainstalovanými agenty ochrany. Nástroj DpmSync obnoví databázi MABS, synchronizuje databázi MABS s replikami ve fondu úložiště, obnoví databázi sestav a znovu přidělí chybějící repliky.

### <a name="parameters"></a>Parametry

| Parametr      | Popis    |
|----------------|-----------------------------|
| **– RestoreDb**                       | Obnoví databázi MABS ze zadaného umístění.|
| **-Sync**                            | Synchronizuje obnovené databáze. Po obnovení databází je nutné spustit nástroj **DpmSync – synchronizace** . Po spuštění příkazu **DpmSync – Sync**mohou být některé repliky stále označeny jako chybějící. |
| **– DbLoc** *umístění*                | Určuje umístění zálohy databáze MABS.|
| **-InstanceName** <br/>*server\instance*     | Instance, do které se musí obnovit DPMDB|
| **– ReallocateReplica**         | Znovu přidělí všechny chybějící svazky repliky bez synchronizace. |
| **– Kopírováno**                      | Indikuje, že jste dokončili načítání dat do nově přidělených svazků repliky. To platí jenom pro klientské počítače. |

**Příklad 1:** Chcete-li obnovit databázi MABS z místního záložního média na serveru MABS, spusťte následující příkaz:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

Po obnovení databáze MABS pro synchronizaci databází spusťte následující příkaz:

```cmd
DpmSync -Sync
```

Po obnovení a synchronizaci databáze MABS a před obnovením repliky spusťte následující příkaz, který znovu přidělí místo na disku repliky:

```cmd
DpmSync -ReallocateReplica
```

**Příklad 2:** Chcete-li obnovit databázi MABS ze vzdálené databáze, spusťte na vzdáleném počítači následující příkaz:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

Po obnovení databáze MABS pro synchronizaci databází spusťte na serveru MABS následující příkaz:

```cmd
DpmSync -Sync
```

Po obnovení a synchronizaci databáze MABS a před obnovením repliky spusťte na serveru MABS následující příkaz, který znovu přidělí místo na disku repliky:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Další kroky

- [MABS support Matrix](backup-support-matrix-mabs-dpm.md)
- [NEJČASTĚJŠÍ DOTAZY K MABS](backup-azure-dpm-azure-server-faq.md)
