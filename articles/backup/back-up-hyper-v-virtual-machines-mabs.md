---
title: Zálohování virtuálních počítačů s technologií Hyper-V pomocí MABS
description: Tento článek obsahuje postupy pro zálohování a obnovení virtuálních počítačů pomocí serveru Microsoft Azure Backup (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 00d1dd04522c51e4d68450a7b8f25d7159d63724
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255065"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Zálohování virtuálních počítačů s technologií Hyper-V pomocí Azure Backup Server

Tento článek vysvětluje, jak zálohovat virtuální počítače Hyper-V pomocí serveru Microsoft Azure Backup (MABS).

## <a name="supported-scenarios"></a>Podporované scénáře

MABS může zálohovat virtuální počítače, které běží na hostitelských serverech Hyper-V, v následujících scénářích:

- **Virtuální počítače s místním nebo přímým úložištěm** – zálohuje virtuální počítače hostované na samostatných serverech hostitele Hyper-V, které mají místní nebo přímo připojené úložiště. Například: pevný disk, zařízení sítě SAN (Storage Area Network) nebo zařízení úložiště připojené k síti (NAS). Na všech hostitelích musí být nainstalován agent MABS Protection.

- **Virtuální počítače v clusteru s úložištěm CSV** – zálohuje virtuální počítače hostované v clusteru Hyper-V s úložištěm sdílený svazek clusteru (CSV). Agent MABS Protection je nainstalován na každý uzel clusteru.

## <a name="host-versus-guest-backup"></a>Zálohování v porovnání s hostem

MABS může provádět zálohování virtuálních počítačů Hyper-V na úrovni hostitele nebo hosta. Na úrovni hostitele se agent MABS Protection nainstaluje na hostitelský server nebo cluster Hyper-V a chrání všechny virtuální počítače a datové soubory běžící na tomto hostiteli.   Na úrovni hosta se agent nainstaluje do každého virtuálního počítače a chrání zatížení, které je v tomto počítači k dispozici.

Obě metody mají své specialisty i nevýhody:

- Zálohy na úrovni hostitele jsou flexibilní, protože pracují bez ohledu na typ operačního systému spuštěného v hostovaných počítačích a nevyžadují instalaci agenta MABS Protection na každý virtuální počítač. Pokud nasadíte zálohování na úrovni hostitele, můžete obnovit celý virtuální počítač nebo soubory a složky (obnovení na úrovni položek).

- Zálohování na úrovni hosta je užitečné, pokud chcete chránit konkrétní úlohy běžící na virtuálním počítači. Na úrovni hostitele můžete obnovit celý virtuální počítač nebo konkrétní soubory, ale neposkytnete obnovení v kontextu konkrétní aplikace. Pokud chcete například obnovit konkrétní položky SharePointu z zálohovaného virtuálního počítače, měli byste provést zálohování na úrovni hosta tohoto virtuálního počítače. Zálohování na úrovni hosta použijte v případě, že chcete chránit data uložená na průchozích discích. Passthrough umožňuje virtuálnímu počítači přímý přístup k úložnému zařízení a neukládá data virtuálních svazků do souboru VHD.

## <a name="how-the-backup-process-works"></a>Jak funguje proces zálohování

MABS provádí zálohování pomocí VSS následujícím způsobem. Kroky v tomto popisu jsou očíslovány, které vám pomůžou s přehledností.

1. Synchronizační modul založený na blokování MABS vytvoří počáteční kopii chráněného virtuálního počítače a zajistí, že kopie virtuálního počítače je dokončena a konzistentní.

2. Po provedení a ověření počáteční kopie MABS použije zapisovač VSS technologie Hyper-V k zachycení záloh. Zapisovač VSS poskytuje sadu diskových bloků s konzistentními daty, které jsou synchronizované se serverem MABS. Tento přístup poskytuje výhodu "úplného zálohování" se serverem MABS při minimalizaci zálohovaných dat, která je nutné přenést přes síť.

3. Agent MABS Protection na serveru, na kterém běží technologie Hyper-V, používá existující rozhraní API technologie Hyper-V k určení, jestli chráněný virtuální počítač podporuje také službu VSS.

   - Pokud virtuální počítač splňuje požadavky na online zálohování a je nainstalovaná komponenta integračních služeb technologie Hyper-V, pak zapisovač VSS technologie Hyper-V rekurzivně přepošle požadavek služby VSS do všech procesů podporujících službu VSS ve virtuálním počítači. Tato operace probíhá, aniž by byl na virtuálním počítači nainstalován agent MABS Protection. Rekurzivní požadavek VSS umožňuje zapisovači VSS technologie Hyper-V zajistit synchronizaci operací zápisu na disk, aby byl snímek služby VSS zachycen bez ztráty dat.

     Komponenta integračních služeb technologie Hyper-V vyvolá zapisovač VSS technologie Hyper-V ve službě Stínová kopie svazku (VSS) ve virtuálních počítačích, aby se zajistilo, že jejich aplikační data budou v konzistentním stavu.

   - Pokud virtuální počítač nesplňuje požadavky online zálohování, MABS pomocí rozhraní API technologie Hyper-V automaticky pozastaví virtuální počítač před tím, než zachytí datové soubory.

4. Jakmile se počáteční základní kopie virtuálního počítače synchronizuje se serverem MABS, budou všechny změny provedené v prostředcích virtuálního počítače zachyceny v novém bodu obnovení. Bod obnovení představuje konzistentní stav virtuálního počítače v určitou dobu. K zachycení bodu obnovení může dojít alespoň jednou denně. Při vytvoření nového bodu obnovení používá MABS replikaci na úrovni bloku ve spojení s zapisovačem VSS technologie Hyper-V k určení, které bloky se na serveru s technologií Hyper-V po vytvoření posledního bodu obnovení změnily. Tyto datové bloky jsou pak přeneseny na server MABS a jsou aplikovány na repliku chráněných dat.

5. Server MABS používá službu VSS na svazcích, které hostují data pro obnovení, aby bylo k dispozici více stínových kopií. Každá z těchto stínových kopií poskytuje samostatné obnovení. Body obnovení služby VSS jsou uloženy na serveru MABS. Dočasná kopie vytvořená na serveru s technologií Hyper-V je uložena pouze po dobu trvání synchronizace MABS.

>[!NOTE]
>
>Od verze Windows Server 2016 jsou virtuální pevné disky technologie Hyper-V integrované sledování změn známé jako odolné sledování změn (RCT). MABS používá RCT (nativní sledování změn v technologii Hyper-V), což snižuje nutnost časově náročné kontroly konzistence ve scénářích, jako jsou například chyby virtuálních počítačů. RCT poskytuje lepší odolnost proti sledování změn, které poskytuje zálohování na základě snímků služby Stínová kopie svazku (VSS). MABS V3 optimalizuje využití sítě a úložiště tím, že během jakýchkoli kontrol konzistence přenáší jenom změněná data.

## <a name="backup-prerequisites"></a>Předpoklady zálohování

Toto jsou požadavky pro zálohování virtuálních počítačů s technologií Hyper-V pomocí nástroje MABS:

|Požadavek|Podrobnosti|
|------------|-------|
|Požadavky na MABS|– Pokud chcete provést obnovení na úrovni položky pro virtuální počítače (obnovení souborů, složek, svazků), budete muset na server MABS nainstalovat roli Hyper-V.  Pokud chcete pouze obnovit virtuální počítač, nikoli úroveň položky, není tato role nutná.<br />– Můžete chránit až 800 virtuálních počítačů 100 GB na jednom serveru MABS a povolíte více serverů MABS podporujících větší clustery.<br />– MABS vyloučí stránkovací soubor z přírůstkového zálohování pro zlepšení výkonu zálohování virtuálních počítačů.<br />-MABS může zálohovat server nebo cluster Hyper-V ve stejné doméně jako server MABS nebo v podřízené nebo důvěryhodné doméně. Pokud chcete vytvořit zálohu technologie Hyper-V v pracovní skupině nebo v nedůvěryhodné doméně, bude nutné nastavit ověřování. Pro jeden server Hyper-V můžete použít ověřování protokolem NTLM nebo ověření certifikátu. V případě clusteru můžete použít pouze ověřování certifikátů.<br />-Použití zálohování na úrovni hostitele k zálohování dat virtuálního počítače na průchozí disky není podporováno. V tomto scénáři doporučujeme použít zálohování na úrovni hostitele k zálohování souborů VHD a zálohování na úrovni hosta k zálohování dalších dat, která nejsou na hostiteli viditelná.<br />   – Můžete zálohovat virtuální počítače uložené na svazcích s odstraněnými duplicitními daty.|
|Požadavky na virtuální počítač Hyper-V|-Verze integračních komponent, které běží na virtuálním počítači, by měla být stejná jako verze hostitele Hyper-V. <br />– Pro každou zálohu virtuálního počítače budete potřebovat volné místo na svazku, který je hostitelem souborů virtuálního pevného disku, abyste umožnili technologii Hyper-V dostatek místa pro rozdílové disky (AVHD) během zálohování. Prostor musí být minimálně roven **počáteční velikosti disku\*míra četnosti změn\*** čas okna zálohování. Pokud spouštíte více záloh v clusteru, budete potřebovat dostatečnou kapacitu úložiště pro přizpůsobení soubory AVHD sloučí pro každý virtuální počítač pomocí tohoto výpočtu.<br />– Chcete-li zálohovat virtuální počítače nacházející se na hostitelských serverech technologie Hyper-V se systémem Windows Server 2012 R2, musí mít virtuální počítač zadaný řadič SCSI, a to i v případě, že není připojen k žádnému. (V online zálohování Windows Serveru 2012 R2 Hostitel Hyper-V připojí nový virtuální pevný disk ve virtuálním počítači a později ho odpojí. To může podporovat jenom řadič SCSI, a proto se vyžaduje pro online zálohování virtuálního počítače.  Bez tohoto nastavení se při pokusu o zálohování virtuálního počítače vydá událost s ID 10103.)|
|Předpoklady pro Linux|– Virtuální počítače se systémem Linux můžete zálohovat pomocí MABS. Podporovány jsou pouze snímky konzistentní se soubory.|
|Zálohování virtuálních počítačů pomocí úložiště CSV|– Pro úložiště CSV nainstalujte poskytovatele hardwaru služby Stínová kopie svazku (VSS) na serveru Hyper-V. Obraťte se na dodavatele sítě SAN (Storage Area Network) pro poskytovatele hardwaru VSS.<br />– Pokud se jeden uzel v clusteru CSV neočekávaně ukončí, MABS provede kontrolu konzistence u virtuálních počítačů, které v tomto uzlu běžely.<br />– Pokud potřebujete restartovat server Hyper-V, který má nástroj BitLocker Drive Encryption povolený na clusteru CSV, musíte spustit kontrolu konzistence pro virtuální počítače s technologií Hyper-V.|
|Zálohování virtuálních počítačů s úložištěm SMB|– Zapněte automatické připojení na serveru, na kterém běží technologie Hyper-V, a povolte ochranu virtuálního počítače.<br />   -Zakázat snižování zátěže TCP Chimney.<br />– Zajistěte, aby všechny účty počítačů s technologií Hyper-V měly úplná oprávnění ke konkrétním vzdáleným sdíleným složkám souborů protokolu SMB.<br />– Ujistěte se, že cesta k souboru pro všechny součásti virtuálního počítače během obnovení do alternativního umístění je kratší než 260 znaků. Pokud ne, obnovení může být úspěšné, ale technologie Hyper-V nebude moci připojit virtuální počítač.<br />– Následující scénáře nejsou podporovány:<br />     Nasazení, ve kterých jsou některé součásti virtuálního počítače na místních svazcích a některé součásti na vzdálených svazcích; adresa IPv4 nebo IPv6 pro souborový server umístění úložiště a obnovení virtuálního počítače do počítače, který používá vzdálené sdílené složky protokolu SMB.<br />– Službu agenta VSS souborového serveru budete muset povolit na každém serveru SMB – přidat ho do části **Přidat role a funkce** > **vybrat role serveru** > **Souborová služba a služba úložiště** > **SOUBOROVÉ** služby > souborová **Služba** **agenta VSS souborového serveru** > .|

## <a name="back-up-virtual-machines"></a>Zálohování virtuálních počítačů

1. Nastavte [Server MABS](backup-azure-microsoft-azure-backup.md) a [úložiště](backup-mabs-add-storage.md). Při nastavování úložiště použijte tyto pokyny pro kapacitu úložiště.
   - Průměrná velikost virtuálního počítače – 100 GB
   - Počet virtuálních počítačů na server MABS-800
   - Celková velikost 800 virtuálních počítačů – 80 TB
   - Požadované místo pro úložiště zálohování – 80 TB

2. Nastavte agenta ochrany MABS Protection na serverech Hyper-V nebo na uzlech clusteru Hyper-V. Pokud provádíte zálohování na úrovni hosta, nainstalujete agenta na virtuální počítače, které chcete zálohovat na úrovni hosta.

3. V konzole pro správu MABS klikněte na **ochrana** > **vytvořit skupinu ochrany** a otevřete průvodce **vytvořením nové skupiny ochrany** .

4. Na stránce **Vybrat členy skupiny** vyberte virtuální počítače, které chcete chránit z hostitelských serverů Hyper-V, na kterých jsou umístěné. Doporučujeme umístit všechny virtuální počítače, které budou mít stejné zásady ochrany, do jedné skupiny ochrany. Chcete-li efektivně využívat místo, povolte společné umístění. Kolokace umožňuje najít data z různých skupin ochrany na stejném disku nebo v páskovém úložišti, aby více zdrojů dat mělo jednu repliku a svazek bodu obnovení.

5. Na stránce **Vyberte způsob ochrany dat** zadejte název skupiny ochrany. Vyberte možnost **Chci krátkodobou ochranu pomocí disku** a vyberte možnost **Chci online ochranu** , pokud chcete zálohovat data do Azure pomocí služby Azure Backup.

6. V oblasti **zadat krátkodobé cíle** > **Rozsah uchování**určete, jak dlouho chcete uchovat data na disku. V části **četnost synchronizací**určete, jak často se má spouštět přírůstkové zálohování dat. Alternativně můžete místo výběru intervalu pro přírůstkové zálohování povolit **těsně před bodem obnovení**. Pokud je toto nastavení povoleno, MABS spustí expresní úplné zálohování těsně před každým naplánovaným bodem obnovení.

    > [!NOTE]
    >
    >Pokud chráníte úlohy aplikací, vytvoří se body obnovení v souladu s četností synchronizací, a to za předpokladu, že aplikace podporuje přírůstkové zálohování. Pokud ne, MABS spustí expresní úplné zálohování místo přírůstkového zálohování a vytvoří body obnovení v souladu s plánem expresního zálohování.

7. Na stránce **zkontrolovat přidělení disku** zkontrolujte přidělené místo na disku fondu úložiště pro skupinu ochrany.

   **Celková velikost dat** je velikost dat, která chcete zálohovat, a **místo na disku, které se má zřídit v MABS** , je místo, které MABS pro skupinu ochrany doporučuje. MABS vybere ideální záložní svazek na základě nastavení. Můžete ale upravit volby svazku pro zálohování v **podrobnostech přidělení disku**. V rozevírací nabídce vyberte preferované úložiště pro úlohy. Vaše úpravy mění hodnoty **celkového úložiště** a **bezplatného úložiště** v podokně **Disk Storage k dispozici** . Za nezřízené místo je množství MABS úložiště, které vám nabídne přidání do svazku, aby bylo možné v budoucnu pokračovat v zálohování.

8. Na stránce **Vybrat způsob vytvoření repliky** určete, jak se bude provádět počáteční replikace dat ve skupině ochrany. Pokud zvolíte **automatickou replikaci přes síť**, doporučujeme vám vybrat dobu mimo špičku. Pro velké objemy dat nebo méně, než jsou optimální síťové podmínky, zvažte možnost **ručního**výběru, který vyžaduje replikaci dat offline pomocí vyměnitelného média.

9. Na stránce **Možnosti kontroly konzistence** vyberte, jak chcete automatizovat kontroly konzistence. Kontrolu lze povolit pouze v případě, že se data repliky neshodují, nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, můžete kdykoli spustit ruční kontrolu tak, že pravým tlačítkem myši kliknete na skupinu ochrany a vyberete **provést kontrolu konzistence**.

    Po vytvoření skupiny ochrany dojde k počáteční replikaci dat v souladu s vámi vybranou metodou. Po počáteční replikaci proběhne Každá záloha v souladu s nastavením skupiny ochrany. Pokud potřebujete obnovit zálohovaná data, vezměte na vědomí následující:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Zálohování virtuálních počítačů konfigurovaných pro migraci za provozu

Pokud jsou virtuální počítače zapojené do migrace za provozu, MABS nadále chrání virtuální počítače, pokud je agent ochrany MABS nainstalovaný na hostiteli Hyper-V. Způsob, jakým MABS chrání virtuální počítače, závisí na typu migrace za provozu.

**Migrace za provozu v rámci clusteru** – Pokud je virtuální počítač migrován v rámci clusteru MABS, aplikace rozpozná migraci a zálohuje virtuální počítač z nového uzlu clusteru, aniž by byl nutný zásah uživatele. Vzhledem k tomu, že se umístění úložiště nezměnilo, MABS pokračuje s expresním úplným zálohováním.

**Migrace za provozu mimo cluster** – při migraci virtuálního počítače mezi samostatnými servery, různými clustery nebo mezi samostatným serverem a clusterem MABS detekuje migraci a může zálohovat virtuální počítač bez zásahu uživatele.

### <a name="requirements-for-maintaining-protection"></a>Požadavky na údržbu ochrany

Níže jsou uvedené požadavky na údržbu ochrany během migrace za provozu:

- Hostitelé Hyper-V pro virtuální počítače se musí nacházet v cloudu System Center VMM na serveru VMM, na kterém běží aspoň System Center 2012 s aktualizací SP1.

- Na všech hostitelích Hyper-V musí být nainstalovaný agent MABS Protection.

- Servery MABS musí být připojené k serveru VMM. Všechny hostitelské servery Hyper-V v cloudu VMM musí být taky připojené k serverům MABS. Díky tomu může MABS komunikovat se serverem VMM, aby MABS zjistit, na kterém hostitelském serveru Hyper-V je virtuální počítač momentálně spuštěný, a vytvořit novou zálohu z tohoto serveru Hyper-V. Pokud nelze navázat připojení k serveru Hyper-V, zálohování se nepovede a zobrazí se zpráva, že agent MABS Protection je nedosažitelný.

- Všechny servery MABS, servery VMM a hostitelské servery Hyper-V musí být ve stejné doméně.

### <a name="details-about-live-migration"></a>Podrobnosti o migraci za provozu

Při zálohování během migrace za provozu Pamatujte na toto:

- Pokud migrace za provozu přenáší úložiště, MABS provede úplnou kontrolu konzistence virtuálního počítače a pak pokračuje v expresním úplném zálohování. Pokud dojde k migraci úložiště za provozu, technologie Hyper-V změní uspořádání virtuálního pevného disku (VHD) nebo VHDX, což způsobí jednorázové špičky ve velikosti zálohovaných dat MABS.

- Na hostiteli virtuálního počítače zapněte automatické připojení, aby se aktivovala virtuální ochrana, a zakažte snižování zátěže TCP Chimney.

- MABS používá jako výchozí port pro hostování pomocné služby DPM-VMM port 6070. Postup změny registru:

    1. Přejděte na **HKLM\Software\Microsoft\Microsoft data Protection Manager\Configuration**.
    2. Vytvořte 32 hodnotu typu DWORD: DpmVmmHelperServicePort a zapište aktualizované číslo portu jako součást klíče registru.
    3. Otevřete ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```a změňte číslo portu z 6070 na nový port. Příklad: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Restartujte službu DPM-VMM Helper a restartujte službu DPM.

### <a name="set-up-protection-for-live-migration"></a>Nastavení ochrany pro migraci za provozu

Nastavení ochrany pro migraci za provozu:

1. Nastavte server MABS a jeho úložiště a na každý hostitelský server Hyper-V nebo uzel clusteru v cloudu VMM nainstalujte agenta MABS Protection. Pokud v clusteru používáte úložiště SMB, nainstalujte agenta ochrany MABS na všech uzlech clusteru.

2. Nainstalujte konzolu VMM jako součást klienta na serveru MABS, aby MABS mohl komunikovat se serverem VMM. Konzola by měla mít stejnou verzi jako ta, která běží na serveru VMM.

3. Přiřaďte účet MABSMachineName $ jako účet správce jen pro čtení v management server VMM.

4. Připojte všechny hostitelské servery Hyper-V ke všem serverům MABS pomocí rutiny prostředí PowerShell `Set-DPMGlobalProperty`. Rutina přijímá více názvů serverů MABS. Použijte formát: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Další informace najdete v tématu [set-DPMGlobalProperty](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmglobalproperty?view=systemcenter-ps-2019).

5. Po zjištění všech virtuálních počítačů spuštěných v hostitelích Hyper-V v cloudech VMM nastavte skupinu ochrany a přidejte virtuální počítače, které chcete chránit. Automatické kontroly konzistence by měly být povolené na úrovni skupiny ochrany pro ochranu v rámci scénářů mobility virtuálních počítačů.

6. Po nakonfigurování nastavení migruje virtuální počítač z jednoho clusteru do jiného, jak bude pokračovat. Můžete ověřit, jestli je migrace za provozu zapnutá podle očekávání, a to takto:

   1. Ověřte, zda je spuštěna pomocná služba DPM-VMM. Pokud není, spusťte ji.

   2. Otevřete Microsoft SQL Server Management Studio a připojte se k instanci, která je hostitelem databáze MABS (DPMDB). V DPMDB spusťte následující dotaz: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Tento dotaz obsahuje vlastnost s názvem `KnownVMMServer`. Tato hodnota by měla být stejná jako hodnota, kterou jste zadali pomocí rutiny `Set-DPMGlobalProperty`.

   3. Spusťte následující dotaz, který ověří parametr *VMMIdentifier* v `PhysicalPathXML` pro konkrétní virtuální počítač. Nahraďte `VMName` názvem virtuálního počítače.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Otevřete soubor. XML, který tento dotaz vrátí, a ověřte, že pole *VMMIdentifier* má hodnotu.

### <a name="run-manual-migration"></a>Spustit ruční migraci

Po dokončení kroků v předchozích částech a dokončení úlohy MABS Summary Manageru je migrace povolená. Ve výchozím nastavení se tato úloha spustí o půlnoci a spustí se každé ráno. Pokud chcete spustit ruční migraci, abyste zkontrolovali, jestli všechno funguje podle očekávání, udělejte toto:

1. Otevřete SQL Server Management Studio a připojte se k instanci, která je hostitelem databáze MABS.

2. Spusťte následující dotaz: `SELECT SCH.ScheduleId FROM tbl_JM_JobDefinition JD JOIN tbl_SCH_ScheduleDefinition SCH ON JD.JobDefinitionId = SCH.JobDefinitionId WHERE JD.Type = '282faac6-e3cb-4015-8c6d-4276fcca11d4' AND JD.IsDeleted = 0 AND SCH.IsDeleted = 0`. Tento dotaz vrátí **hodnotu ScheduleID**. Poznamenejte si toto ID, protože ho budete používat v dalším kroku.

3. V SQL Server Management Studio rozbalte položku **Agent SQL Server**a potom rozbalte položku **úlohy**. Klikněte pravým tlačítkem na **hodnotu ScheduleID** , které jste si poznamenali, a **v kroku vyberte spustit úlohu**.

Výkon zálohování je ovlivněn při spuštění úlohy. Velikost a rozsah nasazení určuje, jak dlouho trvá dokončení úlohy.

## <a name="back-up-replica-virtual-machines"></a>Zálohování virtuálních počítačů repliky

Pokud je MABS spuštěný v systému Windows Server 2012 R2 nebo novějším, můžete zálohovat virtuální počítače repliky. To je užitečné z několika důvodů:

**Omezuje dopad zálohování na běžící úlohu** – zálohování virtuálního počítače při vytváření snímku nevede k nějaké režii. Přesměrováním procesu zálohování na sekundární vzdálený web je spuštěná úloha již ovlivněna operací zálohování. To platí jenom pro nasazení, kde se záložní kopie ukládá na vzdálené lokalitě. Například můžete provádět denní zálohy a ukládat data místně, abyste zajistili dobu rychlého obnovení, ale měsíční nebo čtvrtletní zálohy z virtuálních počítačů repliky, které jsou uloženy vzdáleně pro dlouhodobé uchovávání.

**Šetří šířku pásma** – v typických nasazeních vzdálené větve pobočky nebo ústředí potřebujete pro přenos zálohovaných dat mezi lokalitami dostatečné množství zřízené šířky pásma. Pokud vytvoříte strategii replikace a převzetí služeb při selhání kromě vaší strategie zálohování dat, můžete snížit množství redundantních dat odesílaných přes síť. Zálohováním dat virtuálního počítače repliky místo primárního počítače ušetříte režijní náklady posílání zálohovaných dat po síti.

**Povoluje zálohování hostitele** – můžete použít hostované datové centrum jako web repliky, a to bez nutnosti sekundárního datového centra. V takovém případě bude SLA hostitele vyžadovat konzistentní zálohování virtuálních počítačů replik.

Virtuální počítač repliky je vypnutý, dokud se nespustí převzetí služeb při selhání a služba VSS nemůže zaručit zálohování konzistentní s aplikacemi pro virtuální počítač repliky. Zálohování virtuálního počítače repliky bude proto jenom v souladu s chybami. Pokud nejde zaručit konzistenci havárií, zálohování se nepovede a k tomu může dojít v několika podmínkách:

- Virtuální počítač repliky není v pořádku a je v kritickém stavu.

- Virtuální počítač repliky se znovu synchronizuje (ve stavu probíhá opětovná synchronizace nebo je vyžadována opětovná synchronizace).

- Probíhá počáteční replikace mezi primárním a sekundárním webem nebo čeká na virtuální počítač.

- protokoly. hrl se používají pro virtuální počítač repliky nebo se předchozí akce pro použití protokolů. hrl na virtuálním disku nezdařila, nebo byla zrušena nebo přerušena.

- Probíhá migrace nebo převzetí služeb při selhání virtuálního počítače repliky.

## <a name="recover-backed-up-virtual-machines"></a>Obnovení zálohovaných virtuálních počítačů

Při obnovení zálohovaného virtuálního počítače můžete použít Průvodce obnovením k výběru virtuálního počítače a konkrétního bodu obnovení. Otevření Průvodce obnovením a obnovení virtuálního počítače:

1. V konzole pro správu MABS zadejte název virtuálního počítače, nebo rozbalte seznam chráněných položek a vyberte virtuální počítač, který chcete obnovit.

2. V podokně **body obnovení pro** klikněte v kalendáři na libovolné datum, aby se zobrazily dostupné body obnovení. Pak v podokně **cesta** vyberte bod obnovení, který chcete použít v Průvodci obnovením.

3. V nabídce **Akce** kliknutím na tlačítko **obnovit** otevřete Průvodce obnovením.

    Vybraný virtuální počítač a bod obnovení se zobrazí na obrazovce **Revize výběru obnovení** . Klikněte na **Další**.

4. Na obrazovce **Vybrat typ obnovení** vyberte, kam chcete data obnovit, a potom klikněte na **Další**.

    - **Obnovit na původní instanci**: při obnovení na původní instanci se odstraní původní virtuální pevný disk. MABS obnoví virtuální pevný disk a další konfigurační soubory do původního umístění pomocí zapisovače VSS technologie Hyper-V. Na konci procesu obnovení jsou virtuální počítače pořád vysoce dostupné.
        Pro obnovení musí být k dispozici skupina prostředků. Pokud není k dispozici, proveďte obnovení do alternativního umístění a pak vytvořte vysokou dostupnost virtuálního počítače.

    - **Obnovit jako virtuální počítač na libovolného hostitele**: MABS podporuje obnovení do alternativního umístění (ALR), které poskytuje bezproblémové obnovení chráněného virtuálního počítače s technologií Hyper-v na jiného hostitele Hyper-v, nezávisle na architektuře procesoru. Virtuální počítače Hyper-V, které jsou obnoveny do uzlu clusteru, nebudou vysoce dostupné. Pokud zvolíte tuto možnost, Průvodce obnovením zobrazí další obrazovku pro určení cílové a cílové cesty.

    - **Kopírovat do síťové složky**: MABS podporuje obnovení na úrovni položek (ilr), které umožňuje provádět obnovení na úrovni položek souborů, složek, svazků a virtuálních pevných disků (VHD) ze záloh virtuálních počítačů Hyper-V na úrovni hostitele do sdílené síťové složky nebo svazku na chráněném serveru MABS. K provedení obnovení na úrovni položek není nutné instalovat agenta MABS Protection v rámci hosta. Pokud zvolíte tuto možnost, Průvodce obnovením zobrazí další obrazovku pro určení cílové a cílové cesty.

5. V části **zadat možnosti obnovení** nakonfigurujte možnosti obnovení a klikněte na **Další**:

    - Pokud obnovujete virtuální počítač přes malou šířku pásma, klikněte na **Upravit** a povolte **omezování využití šířky pásma sítě**. Po zapnutí možnosti omezení můžete zadat šířku pásma, kterou chcete zpřístupnit, a čas, kdy je tato šířka pásma k dispozici.
    - Pokud jste nakonfigurovali síť, vyberte možnost **Povolit obnovení na základě San pomocí snímků hardwaru** .
    - Pokud chcete, aby se e-mailová oznámení odeslala po dokončení procesu obnovení, vyberte **Odeslat e-mail po dokončení obnovení** a pak zadejte e-mailové adresy.

6. Na obrazovce souhrnu se ujistěte, že jsou všechny podrobnosti správné. Pokud nejsou podrobnosti správné nebo chcete provést změnu, klikněte na tlačítko **zpět**. Pokud jste s nastavením spokojeni, kliknutím na **obnovit** spusťte proces obnovení.

7. Na obrazovce **stav obnovení** jsou uvedeny informace o úloze obnovení.

## <a name="next-steps"></a>Další kroky

[Obnovení dat z Azure Backup Serveru](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
