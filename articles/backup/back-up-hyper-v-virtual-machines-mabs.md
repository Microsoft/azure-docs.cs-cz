---
title: Zálohování virtuálních počítačů Hyper-V pomocí MABS
description: Tento článek obsahuje postupy pro zálohování a obnovení virtuálních počítačů pomocí Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 00d1dd04522c51e4d68450a7b8f25d7159d63724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255065"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Zálohování virtuálních počítačů Hyper-V pomocí Azure Backup Server

Tento článek vysvětluje, jak zálohovat virtuální počítače Hyper-V pomocí Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Podporované scénáře

MABS můžete zálohovat virtuální počítače běžící na hostitelských serverech Hyper-V v následujících scénářích:

- **Virtuální počítače s místním nebo přímým úložiště** – Zálohujete virtuální počítače hostované na samostatných hostitelských serverech Hyper-V, které obsahují místní nebo přímo připojené úložiště. Například: pevný disk, zařízení san (STORAGE NETWORK) nebo zařízení s úložištěm připojeným k síti (NAS). Agent ochrany MABS musí být nainstalován na všech hostitelích.

- **Virtuální počítače v clusteru s úložištěm CSV** – Zálohujete virtuální počítače hostované v clusteru Hyper-V s úložištěm CSV (sdílený svazek clusteru). Agent ochrany MABS je nainstalován v každém uzlu clusteru.

## <a name="host-versus-guest-backup"></a>Zálohování hostitele versus hosta

MABS můžete provést hostitele nebo hosta-úrovni zálohování virtuálních vavek Hyper-V. Na úrovni hostitele je agent ochrany MABS nainstalován na hostitelském serveru nebo v clusteru Hyper-V a chrání celé virtuální počítače a datové soubory spuštěné na tomto hostiteli.   Na úrovni hosta je agent nainstalován na každém virtuálním počítači a chrání úlohy přítomné v tomto počítači.

Obě metody mají svá pro a proti:

- Zálohy na úrovni hostitele jsou flexibilní, protože fungují bez ohledu na typ operačního systému spuštěného na počítačích hosta a nevyžadují instalaci agenta ochrany MABS na každém virtuálním počítači. Pokud nasadíte zálohování na úrovni hostitele, můžete obnovit celý virtuální počítač nebo soubory a složky (obnovení na úrovni položky).

- Zálohování na úrovni hosta je užitečné, pokud chcete chránit konkrétní úlohy spuštěné na virtuálním počítači. Na úrovni hostitele můžete obnovit celý virtuální virtuální ms nebo konkrétní soubory, ale nebude poskytovat obnovení v kontextu konkrétní aplikace. Chcete-li například obnovit konkrétní položky služby SharePoint z zálohovaného virtuálního počítače, měli byste provést zálohu na úrovni hosta tohoto virtuálního počítače. Pokud chcete chránit data uložená na průchodných discích, použijte zálohování na úrovni hosta. Průchod umožňuje virtuálnímu počítači přímý přístup k paměťovému zařízení a neukládá data virtuálního svazku do souboru Virtuálního pevného disku.

## <a name="how-the-backup-process-works"></a>Jak funguje proces zálohování

MABS provádí zálohování s VSS následujícím způsobem. Kroky v tomto popisu očíslované, aby byly přehlednější.

1. Modul synchronizace založený na bloku MABS vytvoří počáteční kopii chráněného virtuálního počítače a zajistí, že kopie virtuálního počítače je úplná a konzistentní.

2. Po vytvoření a ověření počáteční kopie používá MABS zapisovač EVS k zachycení záloh. Zapisovač VSS poskytuje sadu diskových bloků konzistentních s daty, které jsou synchronizovány se serverem MABS. Tento přístup poskytuje výhodu "úplné zálohování" se serverem MABS při minimalizaci záložních dat, která musí být přenesena v síti.

3. Agent ochrany MABS na serveru, na který je spuštěna technologie Hyper-V, používá existující api technologie Hyper-V k určení, zda chráněný virtuální počítač podporuje také službu VSS.

   - Jestliže virtuální počítač odpovídá požadavkům zálohování online a je v něm instalována komponenta integračních služeb technologie Hyper-V, potom zapisovač VSS technologie Hyper-V rekurzivně předává požadavek služby VSS do všech procesů podporujících službu VSS ve virtuálním počítači. K této operaci dochází bez instalace agenta ochrany MABS ve virtuálním počítači. Pomocí tohoto rekurzivního požadavku služby VSS může zapisovač VSS technologie Hyper-V zajistit synchronizaci operací zápisu na disk, aby se snímek služby VSS pořídil bez ztráty dat.

     Komponenta integračních služeb technologie Hyper-V vyvolá zapisovač VSS technologie Hyper-V ve službě Stínová kopie svazku (VSS) ve virtuálních počítačích, aby bylo zajištěno, že jejich aplikační data budou v konzistentním stavu.

   - Pokud virtuální počítač nesplňuje požadavky na zálohování online, MABS automaticky používá rozhraní API Hyper-V k pozastavení virtuálního počítače před tím, než zachytí datové soubory.

4. Po počáteční základní kopii virtuálního počítače synchronizuje se serverem MABS, všechny změny, které jsou provedeny na prostředky virtuálního počítače jsou zachyceny v novém bodě obnovení. Bod obnovení představuje konzistentní stav virtuálního stroje v určitou dobu. K zachycení bodu obnovení musí dojít alespoň jednou denně. Při vytvoření nového bodu obnovení používá MABS replikaci na úrovni bloku ve spojení se zapisovatelem služby VSS technologie Hyper-V k určení, které bloky byly změněny na serveru, na kterém je spuštěna technologie Hyper-V po vytvoření posledního bodu obnovení. Tyto datové bloky jsou pak přeneseny na server MABS a jsou použity na repliku chráněných dat.

5. Server MABS používá službu VSS na svazcích, které hostují data pro obnovení, aby bylo k dispozici více stínových kopií. Každá z těchto stínových kopií poskytuje samostatné obnovení. Body obnovení VSS jsou uloženy na serveru MABS. Dočasná kopie, která je provedena na serveru s technologieí Hyper-V, je uložena pouze po dobu trvání synchronizace MABS.

>[!NOTE]
>
>Počínaje Windows Server 2016, Hyper-V virtuální pevné disky mají integrované sledování změn známé jako odolné sledování změn (RCT). MABS používá RCT (nativní sledování změn v Hyper-V), což snižuje potřebu časově náročné kontroly konzistence ve scénářích, jako jsou selhání virtuálních aplikací. RCT nabízí lepší odolnost než sledování změn poskytované zálohami založenými na snímcích služby Stínová kopie svazku (VSS). MABS V3 dále optimalizuje spotřebu sítě a úložiště přenosem pouze změněných dat během všech kontrol konzistence.

## <a name="backup-prerequisites"></a>Požadavky pro zálohování

Toto jsou předpoklady pro zálohování virtuálních počítačů Hyper-V pomocí MABS:

|Požadavek|Podrobnosti|
|------------|-------|
|Předpoklady MABS|- Pokud chcete provést obnovení na úrovni položek pro virtuální počítače (obnovení souborů, složek, svazků), pak budete muset nainstalovat roli Hyper-V na serveru MABS.  Pokud chcete pouze obnovit virtuální počítač a ne úroveň položky, pak role není vyžadována.<br />- Můžete chránit až 800 virtuálních počítačů 100 GB každý na jednom serveru MABS a povolit více MABS servery, které podporují větší clustery.<br />- MABS vyloučí stránkovací soubor z přírůstkových záloh pro zlepšení výkonu zálohování virtuálního počítače.<br />- MABS může zálohovat server nebo cluster Hyper-V ve stejné doméně jako server MABS nebo v podřízené nebo důvěryhodné doméně. Pokud chcete zálohovat technologii Hyper-V v pracovní skupině nebo v nedůvěryhodné doméně, budete muset nastavit ověřování. U jednoho serveru Hyper-V můžete použít ověřování NTLM nebo certifikátu. U clusteru můžete použít pouze ověřování certifikátů.<br />– Na úrovni hostitele se nedají zálohovat data virtuálního počítače z průchozích disků. V tomto scénáři doporučujeme použít zálohování na úrovni hostitele k zálohování souborů Virtuálního pevného disku a zálohování na úrovni hosta k zálohování dalších dat, která nejsou viditelná na hostiteli.<br />   -Můžete zálohovat virtuální chody uložené na deduplikovaných svazcích.|
|Požadavky virtuálních počítačů Hyper-V|- Verze integration komponenty, která běží na virtuálním počítači by měla být stejná jako verze hostitele Hyper-V. <br />– Pro každou zálohu virtuálního počítače budete potřebovat volné místo na svazku, který je hostitelem souborů virtuálního pevného disku, abyste zajistili technologii Hyper-V dost místa pro rozdílové porovnávání disků (AVHD) během zálohování. Prostor musí být minimálně roven času vypočítanému jako **Počáteční velikost disku\*Klidové vytížení\*Zálohování**. Pokud spouštíte více záloh v clusteru, budete potřebovat dostatečnou kapacitu úložiště (použijte tento výpočet) pro AVHD na všech virtuálních počítačích.<br />- Chcete-li zálohovat virtuální počítače umístěné na hostitelských serverech Hyper-V se systémem Windows Server 2012 R2, měl by mít virtuální počítač zadaný řadič SCSI, i když není k ničemu připojen. (V zálohování online systému Windows Server 2012 R2 hostitel Hyper-V připojí nový virtuální pevný disk do virtuálního počítače a později jej odpojí. Tento ovladač SCSI může podporovat pouze, a proto je vyžadován pro online zálohování virtuálního počítače.  Bez tohoto nastavení se při pokusu o zálohování virtuálního počítače zobrazí ID události 10103.)|
|Požadované součásti systému Linux|- Virtuální počítače Linux můžete zálohovat pomocí MABS. Podporovány jsou pouze snímky konzistentních souborů.|
|Zálohování virtuálních počítačů s úložištěm CSV|– K úložišti CSV nainstalujte na server Hyper-V zprostředkovatele hardwaru služby Stínová kopie svazku (VSS). Kontaktujte dodavatele sítě SAN pro poskytovatele hardwaru VSS.<br />- Pokud jeden uzel neočekávaně vypne v clusteru CSV, MABS provede kontrolu konzistence proti virtuální počítače, které byly spuštěny na tomto uzlu.<br />– Pokud potřebujete restartovat server Hyper-V, který má na clusteru CSV povolený nástroj BitLocker Drive Encryption, je potřeba, abyste spustili kontrolu konzistence virtuálních počítačů Hyper-V.|
|Zálohování virtuálních počítačů s úložištěm SMB|– Zapnutím automatického připojení na serveru, který používá technologii Hyper-V, povolte ochranu virtuálních počítačů.<br />   – Zakažte funkci TCP Chimney Offload.<br />– Zajistěte, aby všechny účty machine$ s technologií Hyper-V měly úplná oprávnění v konkrétních vzdálených sdílených složkách souborů SMB.<br />- Ujistěte se, že cesta k souboru pro všechny součásti virtuálního počítače během obnovení do alternativního umístění je menší než 260 znaků. Pokud ne, obnovení může být úspěšné, ale Hyper-V nebude možné připojit virtuální počítač.<br />- Následující scénáře nejsou podporovány:<br />     Nasazení, kde některé součásti virtuálního počítače jsou na místních svazcích a některé součásti jsou na vzdálených svazcích; Adresa IPv4 nebo IPv6 pro souborový server umístění úložiště a obnovení virtuálního počítače do počítače, který používá vzdálené sdílené složky SMB.<br />- Budete muset povolit službu File Server VSS Agent na každém serveru SMB - Přidat ji **do přidat role a funkce** > **Vyberte role** > serveru**Souborová** > služba**Souborová služba Souborová** > **služba** > Souborová služba**VSS Agent Service**.|

## <a name="back-up-virtual-machines"></a>Zálohování virtuálních počítačů

1. Nastavte [server MABS](backup-azure-microsoft-azure-backup.md) a [úložiště](backup-mabs-add-storage.md). Pro nastavení úložiště postupujte podle těchto pokynů pro jeho kapacitu.
   - Průměrná velikost virtuálního počítače: 100 GB
   - Počet virtuálních strojů na server MABS - 800
   - Celková velikost 800 virtuálních počítačů: 80 TB
   - Požadované místo pro úložiště pro zálohování: 80 TB

2. Nastavte agenta ochrany MABS na uzlech serveru Hyper-V nebo clusteru Hyper-V. Pokud provádíte zálohování na úrovni hosta, nainstalujete agenta na virtuální počítače, které chcete zálohovat na úrovni hosta.

3. V konzole Správce MABS klepnutím na tlačítko **Protection** > **Create protection group** otevřete průvodce Vytvořit novou **skupinu ochrany.**

4. Na stránce **Vybrat členy skupiny** vyberte virtuální počítače, které chcete chránit, a to z hostitelských serverů Hyper-V, na kterých jsou umístěny. Všechny virtuální počítače se stejnými zásadami ochrany doporučujeme dát do jedné skupiny ochrany. Pokud chcete šetřit místem, zapněte kolokaci. Kolokace umožňuje najít data z různých skupin ochrany na stejném disku nebo pásce, aby více zdrojů dat mělo jen jednu repliku a jeden svazek bodu obnovení.

5. Na stránce **Vyberte způsob ochrany dat** zadejte název skupiny ochrany. Pokud chcete zálohovat data na Azure pomocí služby Zálohování Azure, vyberte **Chci krátkodobou ochranu pomocí disku** a potom **Chci online ochranu** .

6. V poli Určení**rozsahu uchovávání** **krátkodobých cílů** > určete, jak dlouho chcete uchovávat data disku. V **četnosti synchronizace**určete, jak často mají být přírůstková zálohování dat spuštěna. Nebo můžete místo intervalu přírůstkového zálohování vybrat možnost **Těsně před bodem obnovení**. Je-li toto nastavení povoleno, spustí MABS expresní úplnou zálohu těsně před každým plánovaným bodem obnovení.

    > [!NOTE]
    >
    >Pokud chráníte úlohy aplikace a ta podporuje přírůstkové zálohování, vytváří se body obnovení na základě nastavení četnosti synchronizací. Pokud tomu tak není, pak MABS spustí expresní úplné zálohování namísto přírůstkové zálohy a vytvoří body obnovení v souladu s plánem expresní zálohování.

7. Na stránce **Zkontrolovat přidělení disku** zkontrolujte místo na disku fondu úložiště přidělené pro skupinu ochrany.

   **Celková velikost dat** je velikost dat, která chcete zálohovat, a **místo na disku, které se má zřídit na MABS,** je místo, které MABS doporučuje pro skupinu ochrany. MABS vybere ideální záložní svazek na základě nastavení. Možnosti záložního svazku ale můžete upravit v části **Podrobnosti přidělení disku**. V rozevírací nabídce vyberte požadované úložiště pro úlohy. Úpravy, které provedete, změní hodnoty v polích **Celková velikost úložiště** a **Volný úložný prostor** v podokně **Dostupný úložný prostor na disku**. Nedostatečně zřízené místo je velikost úložiště MABS navrhuje přidat do svazku, pokračovat v zálohování hladce v budoucnu.

8. Na stránce **Vyberte způsob vytvoření repliky** určete, jak se bude provádět počáteční replikace dat ve skupině ochrany. Pokud zvolíte **možnost Automaticky replikovat v síti**, doporučujeme zvolit čas mimo špičku. U velkého množství dat nebo menšího než optimálních podmínek v síti zvažte možnost **ručního výběru**, která vyžaduje replikaci dat offline pomocí vyměnitelného média.

9. Na stránce **Možnosti kontroly konzistence** vyberte, jak chcete automatizovat kontroly konzistence. Spuštění kontroly můžete povolit jenom pro případ, že začnou být nekonzistentní data repliky, nebo podle plánu. Pokud automatickou kontrolu konzistence nechcete konfigurovat, můžete kdykoliv spustit ruční kontrolu tak, že pravým tlačítkem kliknete na skupinu ochrany a vyberete **Provést kontrolu konzistence**.

    Po vytvoření skupiny ochrany dojde k počáteční replikaci dat v souladu s vámi zvoleným způsobem. Všechna zálohování po počáteční replikaci probíhají podle nastavení skupiny ochrany. Pokud potřebujete obnovit zálohovaná data, poznamenejte si následující:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Zálohování virtuálních počítačů konfigurovaných na migraci za provozu

Pokud jsou virtuální počítače zapojeny do migrace za provozu, MABS nadále chrání virtuální počítače tak dlouho, dokud je agent ochrany MABS nainstalován na hostiteli Hyper-V. Způsob, jakým MABS chrání virtuální počítače, závisí na typu migrace za provozu.

**Migrace za provozu v rámci clusteru** – při migraci virtuálního počítače v rámci clusteru MABS zjistí migraci a zálohuje virtuální počítač z nového uzlu clusteru bez nutnosti zásahu uživatele. Vzhledem k tomu, že umístění úložiště se nezměnilo, MABS pokračuje s expresní úplné zálohy.

**Migrace za provozu mimo cluster** – Při migraci virtuálního počítače mezi samostatnými servery, různými clustery nebo mezi samostatným serverem a clusterem mabs zjistí migraci a může zálohovat virtuální počítač bez zásahu uživatele.

### <a name="requirements-for-maintaining-protection"></a>Podmínky zachování ochrany

Aby se během migrace zachovala ochrana, je nutné splnit následující podmínky:

- Hostitelé technologie Hyper-V pro virtuální počítače musí být umístěni v cloudu System Center VMM na serveru VMM a se serverem System Center 2012 s sp1.

- Agent ochrany MABS musí být nainstalován na všech hostitelích technologie Hyper-V.

- Servery MABS musí být připojeny k serveru VMM. Všechny hostitelské servery Hyper-V v cloudu VMM musí být také připojeny k serverům MABS. To umožňuje MABS komunikovat se serverem VMM, takže MABS může zjistit, na kterém hostitelském serveru Hyper-V je virtuální počítač aktuálně spuštěn, a vytvořit novou zálohu z tohoto serveru Hyper-V. Pokud nelze navázat připojení k serveru Hyper-V, zálohování se nezdaří se zprávou, že agent ochrany MABS je nedostupný.

- Všechny servery MABS, servery VMM a hostitelské servery Hyper-V musí být ve stejné doméně.

### <a name="details-about-live-migration"></a>Podrobnosti o migraci za provozu

Při zálohování během migrace za provozu pamatujte na toto:

- Pokud migrace za provozu přenáší úložiště, MABS provede úplnou kontrolu konzistence virtuálního počítače a pak pokračuje s expresní úplné zálohy. Dojde-li k migraci úložiště za provozu, technologie Hyper-V reorganizuje virtuální pevný disk (VHD) nebo VHDX, což způsobí jednorázový nárůst velikosti záložních dat MABS.

- Na hostiteli virtuálního počítače zapněte automatické připojení. Tím zapnete virtuální ochranu a vypnete funkci TCP Chimney Offload.

- MABS používá port 6070 jako výchozí port pro hostování pomocné služby DPM-VMM. Registr změníte takto:

    1. Přejděte k **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Vytvořte 32bitovou hodnotu typu DWORD nazvanou DpmVmmHelperServicePort a nové číslo portu zapište jako součást klíče registru.
    3. Otevřete ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config``` a změňte číslo portu 6070 na číslo nového portu. Příklad: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Restartujte službu pomoci DPM VMM a restartujte službu DPM.

### <a name="set-up-protection-for-live-migration"></a>Nastavení ochrany pro migraci za provozu

Ochranu pro migraci za provozu nastavíte takto:

1. Nastavte server MABS a jeho úložiště a nainstalujte agenta ochrany MABS na každý hostitelský server nebo uzel clusteru Hyper-V v cloudu VMM. Pokud používáte úložiště SMB v clusteru, nainstalujte agenta ochrany MABS do všech uzlů clusteru.

2. Nainstalujte konzolu VMM jako klientskou součást na server MABS, aby mabs mohl komunikovat se serverem VMM. Konzola by měla mít stejnou verzi, jaká běží na serveru VMM.

3. Přiřaďte účet MABSMachineName$ jako účet správce jen pro čtení na serveru pro správu VMM.

4. Připojte všechny hostitelské servery Hyper-V ke `Set-DPMGlobalProperty` všem serverům MABS pomocí rutiny prostředí PowerShell. Rutina přijímá více názvů serverů MABS. Použijte formát: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Další informace naleznete v tématu [Set-DPMGlobalProperty](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmglobalproperty?view=systemcenter-ps-2019).

5. Jakmile se všechny virtuální počítače spuštěné na hostitelích Hyper-V v cloudech VMM objeví ve službě VMM, nastavte skupinu ochrany a přidejte virtuální počítače, které chcete chránit. Automatické kontroly konzistence by měly být povoleny na úrovni skupiny ochrany pro ochranu ve scénářích mobility virtuálních strojů.

6. Po konfiguraci nastavení, když virtuální počítač migruje z jednoho clusteru do druhého všechny zálohy pokračovat podle očekávání. Ověřit, zda se povolila migrace za provozu dle očekávání, můžete ověřit takto:

   1. Zkontrolujte, zda je spuštěna pomocná služba DPM-VMM. Pokud ne, začněte.

   2. Otevřete Microsoft SQL Server Management Studio a připojte se k instanci, která je hostitelem databáze MABS (DPMDB). V databázi aplikace DPMDB spusťte následující dotaz: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Tento dotaz obsahuje `KnownVMMServer`vlastnost s názvem . Tato hodnota by měla být stejná jako hodnota zadaná v rutině `Set-DPMGlobalProperty`.

   3. Spuštěním následujícího dotazu ověřte parametr *VMMIdentifier* v řetězci `PhysicalPathXML` u konkrétního virtuálního počítače. Nahraďte položku `VMName` názvem virtuálního počítače.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Otevřete soubor .xml vrácený tímto dotazem a ověřte, jestli má pole *VMMIdentifier* nějakou hodnotu.

### <a name="run-manual-migration"></a>Ruční spuštění migrace

Po dokončení kroků v předchozích částech a dokončení úlohy MABS Summary Manager je migrace povolena. Ve výchozím nastavení se tato úloha spustí o půlnoci a proběhne každé ráno. Pokud chcete spustit ruční migraci a zkontrolovat, jestli všechno funguje podle očekávání, postupujte takto:

1. Otevřete SQL Server Management Studio a připojte se k instanci, která je hostitelem databáze MABS.

2. Spusťte následující dotaz: `SELECT SCH.ScheduleId FROM tbl_JM_JobDefinition JD JOIN tbl_SCH_ScheduleDefinition SCH ON JD.JobDefinitionId = SCH.JobDefinitionId WHERE JD.Type = '282faac6-e3cb-4015-8c6d-4276fcca11d4' AND JD.IsDeleted = 0 AND SCH.IsDeleted = 0`. Tento dotaz vrátí hodnotu **ScheduleID**. Poznamenejte si ji, protože ji budete potřebovat v následujícím kroku.

3. V nástroji SQL Server Management Studio rozbalte položku **Agent systému SQL Server** a potom rozbalte **Úlohy**. Klikněte pravým tlačítkem na hodnotu **ScheduleID**, kterou jste si poznamenali, a vyberte **Spustit úlohu v kroku**.

Výkon zálohování je ovlivněna při spuštění úlohy. Velikost a rozsah nasazení určuje, jak dlouho trvá dokončení úlohy.

## <a name="back-up-replica-virtual-machines"></a>Zálohování virtuálních počítačů repliky

Pokud mabs běží na Windows Server 2012 R2 nebo vyšší, pak můžete zálohovat virtuální počítače repliky. To je užitečné z několika důvodů:

**Sníží to dopad zálohování na běžící úlohu** – zálohování virtuálního počítače při vytváření snímku totiž trochu zvyšuje zatížení. Převedením procesu zálohování do sekundární vzdálené lokality již není spuštěná úloha operací zálohování ovlivněna. To se vztahuje pouze na nasazení, kde se záložní kopie ukládají na vzdálený web. Například můžete provádět denní zálohy a ukládat data místně pro zajištění rychlých časů obnovení, ale provádět měsíční nebo čtvrtletně zálohy z virtuálních počítačů repliky uložené pro dlouhodobé ukládání.

**Šetří šířku pásma** – v typických nasazeních vzdálené větve pobočky nebo sídla společnosti potřebujete zajistit odpovídající šířku přenosového pásma k přenosu zálohovaných dat mezi servery. Pokud vedle strategie pro zálohu dat vytvoříte také strategii pro replikaci nebo pro převzetí služeb při selhání, můžete tím snížit objem redundantních dat posílaných prostřednictvím sítě. Zálohováním dat virtuálního počítače repliky, nikoli primární, uložíte režii odesílání zálohovaných dat v síti.

**Umožňuje to zálohování hostitele** – jako server replik můžete použít hostované datové centrum, aniž byste potřebovali sekundární datové centrum. V tomto případě plán SLA hoster vyžaduje konzistentní zálohování virtuálních počítačů repliky.

Virtuální počítač repliky je do převzetí služeb při selhání vypnutý, takže služba Stínová kopie svazku (VSS) nemůže zaručit, že takový počítač zazálohuje konzistentně vzhledem k aplikacím. Proto je zálohování virtuálního počítače repliky konzistentní jenom vzhledem k selháním. Pokud ani konzistence vzhledem k selháním nemůže být zaručena, zálohování se nepovede. To se může stát v několika situacích:

- Virtuální počítač repliky není v pořádku a je v kritickém stavu.

- Virtuální počítač repliky se opětovně synchronizuje (je ve stavu Probíhá nová synchronizace nebo Vyžadována opětovná synchronizace).

- Probíhá počáteční replikace mezi primárním a sekundárním webem nebo čeká na dokončení pro virtuální počítač.

- Protokoly HRL jsou aplikovány na virtuální počítač repliky nebo předchozí akce k použití protokolů HRL na virtuálním disku se nezdařila nebo byla zrušena nebo přerušena.

- Probíhá migrace virtuálního počítače repliky nebo převzetí služeb při jeho selhání.

## <a name="recover-backed-up-virtual-machines"></a>Obnovení zálohovaných virtuálních počítačů

Když chcete obnovit zálohovaný virtuální počítač, pomocí Průvodce obnovením vyberte daný virtuální počítač a konkrétní bod obnovení. Průvodce obnovením otevřete a virtuální počítač obnovíte takto:

1. V konzole Správce MABS zadejte název virtuálního počítače nebo rozbalte seznam chráněných položek a vyberte virtuální počítač, který chcete obnovit.

2. V kalendáři v podokně **Body obnovení pro** klikněte na libovolné datum. Zobrazíte tak dostupné body obnovení. Pak v podokně **Cesta** vyberte bod obnovení, který chcete použít v Průvodci obnovením.

3. V nabídce **Akce** klikněte na **Obnovit**. Tím se otevře Průvodce obnovením.

    Vybraný virtuální počítač a bod obnovení se zobrazí v obrazovce **Revidovat výběr obnovení**. Klikněte na **Další**.

4. Na obrazovce **Vybrat typ obnovení** vyberte, kde chcete obnovit data, a pak klikněte na **Další**.

    - **Obnovit na původní instanci**: Při obnovení na původní instanci se odstraní původní virtuální pevný disk (VHD). MABS obnoví virtuální pevný disk a další konfigurační soubory do původního umístění pomocí zapisovače VSS hyper-VSS. Na konci procesu obnovení jsou virtuální počítače stále vysoce dostupné.
        Pro obnovení musí být dostupná skupina prostředků. Pokud není dostupná, proveďte obnovení do alternativního umístění a pak změňte virtuální počítač na vysoce dostupný.

    - **Obnovit jako virtuální počítač libovolnému hostiteli**: MABS podporuje obnovení alternativního umístění (ALR), které poskytuje bezproblémové obnovení chráněného virtuálního počítače Hyper-V jinému hostiteli Hyper-V, nezávisle na architektuře procesoru. Virtuální počítače s technologií Hyper-V, které jsou obnovovány do uzlu clusteru, nebudou vysoce dostupné. Pokud zvolíte tuto možnost, Průvodce obnovením vám nabídne další obrazovku, kde identifikujete cíl a cílovou cestu.

    - **Kopírování do síťové složky**: MABS podporuje obnovení na úrovni položky (ILR), což umožňuje provést obnovení souborů, složek, svazků a virtuálních pevných disků (VD) na úrovni hostitele ze zálohy virtuálních počítačů Hyper-V na sdílenou síť nebo na svazku na serveru chráněném mabs. Agent ochrany MABS nemusí být nainstalován uvnitř hosta k provedení obnovení na úrovni položky. Pokud zvolíte tuto možnost, Průvodce obnovením vám nabídne další obrazovku, kde identifikujete cíl a cílovou cestu.

5. V části **Zadat možnosti obnovení** nakonfigurujte možnosti obnovení a klikněte na **Další**:

    - Pokud provádíte obnovení virtuálního počítače prostřednictvím sítě s malou šířkou písma, kliknutím na možnost **Upravit** povolíte volbu **Omezení využití šířky pásma sítě**. Až zapnete omezení využití šířky pásma, můžete zadat šířku písma sítě, kterou chcete mít k dispozici, a čas, kdy chcete, aby byla dostupná.
    - Pokud jste nakonfigurovali síť, vyberte **Použít obnovení založené na síti SAN za využití snímků hardwaru**.
    - Pokud chcete po dokončení procesu obnovení dostat e-mailové upozornění, vyberte **Odeslat e-mail po dokončení obnovení** a pak zadejte e-mailovou adresu.

6. Na obrazovce Souhrn zkontrolujte, jestli jsou všechny podrobnosti správné. Pokud podrobnosti nejsou správné nebo chcete provést změnu, klikněte na tlačítko **Zpět**. Pokud jste s nastavením spokojení, kliknutím na tlačítko **Obnovit** spustíte proces obnovení.

7. Obrazovka **Stav obnovení Status** poskytuje informace o úloze obnovení.

## <a name="next-steps"></a>Další kroky

[Obnovení dat z Azure Backup Serveru](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
