---
title: Refaktorujte nasazení Team Foundation Server do služby Azure DevOps v Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso refactors své místní nasazení TFS a migrujte ho ke službám Azure DevOps v Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 79c95a53ace4fd5ab638587d1b74980812fbc273
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116959"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-azure-devops-services"></a>Migrace Contoso: Refaktorovat nasazení Team Foundation Server ke službám Azure DevOps

Tento článek popisuje, jak jsou společnosti Contoso refaktoring jejich místní nasazení Team Foundation Server (TFS) a migrujte ho ke službám Azure DevOps v Azure. Vývojový tým společnosti Contoso použili za posledních pět let TFS pro týmovou spolupráci a správy zdrojového kódu. Nyní chce přesunout do cloudové řešení pro pracovní účely vývoje a testování a pro správu zdrojového kódu. Služby Azure DevOps se přehraje role jako přesunutí na model Azure DevOps a vyvíjet nové aplikace nativní pro cloud.

Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které ukazují, jak nastavit infrastrukturu migrace a spustit různé druhy migrace. Scénáře jejich složitost v. Přidáme další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
[Článek 3: Posouzení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak společnosti Contoso spuštění posouzení jejich místní dvouvrstvé SmartHotel aplikace spuštěné ve VMware. Jejich posouzení virtuálních počítačů aplikace s [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Metody opětovného hostování virtuálních počítačů Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do Azure. Migraci virtuálního počítače webové aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a aplikace pomocí databáze [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) služby k migraci do spravované Instance SQL. | K dispozici.
[Článek 5: Změna hostitele na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak migrovat Contoso jejich SmartHotel do virtuálních počítačů Azure IaaS, pomocí služby Site Recovery.
[Článek 6: Změna hostitele na virtuálních počítačích Azure a skupiny dostupnosti SQL serveru](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace. Používají Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do skupiny dostupnosti SQL serveru. | K dispozici.
[Článek 7: Změna hostitele Linuxovou aplikaci do virtuálních počítačů Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak společnosti Contoso migruje jejich osTicket Linuxovou aplikaci do virtuálních počítačů Azure IaaS pomocí služby Azure Site Recovery.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure serveru MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje osTicket Linuxovou aplikaci. Site Recovery pro migraci virtuálního počítače a aplikace MySQL Workbench používají k migraci na instanci serveru Azure MySQL. | K dispozici.
[Článek 9: Refaktorujte aplikace do webové aplikace Azure a Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Předvádí, jak společnosti Contoso migruje SmartHotel aplikací založených na kontejnerech webové aplikace Azure a migraci databáze aplikace do Azure SQL Server. | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci do Azure App Service a Azure serveru MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje osTicket Linuxovou aplikaci do služby Azure App Service pomocí kontejneru Dockeru s PHP 7.0. Základ kódu pro nasazení je migrovat na Githubu. Databáze aplikace je migrovat na Azure MySQL. | K dispozici.
Článek 11: Refaktorujte nasazení TFS ve službách Azure DevOps | Migrace aplikace dev TFS do služby Azure DevOps v Azure | V tomto článku
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | K dispozici.
[Článek 14: Škálování migrace do Azure](contoso-migration-scale.md) | Po vyzkoušení kombinace migrace, Contoso připraví škálovat na plné migraci do Azure. | K dispozici.


## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzké spolupráci s obchodními partnery pro identifikaci budoucí cíle. Partneři se zbytečně zabývají vývojových nástrojích a technologiích, ale zachycení tyto body:

- **Software**: bez ohledu na podstatu podnikání všech společností se teď softwarové společnosti, včetně společnosti Contoso. Obchodní vedení zájem o IT pomáhá vést společnosti pomocí nových pracovních postupů pro uživatele a prostředími pro své zákazníky.
- **Efektivita**: Contoso je potřeba zjednodušit proces a odebrat nepotřebná postupy pro vývojáře a uživatele. To vám umožní společnosti se smlouvou na požadavky zákazníků efektivněji. Obchodní potřeby IT k rychlá, bez plýtvání čas a peníze.
- **Flexibilita**: Contoso IT musí reagovat na obchodní potřeby a rychleji než na webu marketplace umožňující úspěch v globální ekonomiku reagovat. IT nesmí být blokování automaticky otevíraných oken pro firmy.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro migraci do služby Azure DevOps:

- Tým musí nástroj pro migraci dat do cloudu. By měl být potřeba několik ručních procesů.
- Pracovní položky dat a je potřeba migrovat historii za poslední rok.
- Jejich nechcete nastavit nový uživatelská jména a hesla. Všechny aktuální systému přiřazení musí být udržovány.
- Chtějí se přesuňte směrem od Team Foundation verze ovládacího prvku (TFVC) ke Gitu pro správu zdrojového kódu.
- Přímou migraci na Git bude "koncovou migraci", který importuje pouze nejnovější verze zdrojového kódu. Během výpadek to se stane, když veškerou práci se zastavilo jako staffhubu základu kódu. Tito uživatelé pochopit, že aktuální historie hlavní větev bude k dispozici po přesunutí.
- Jsou zajímá o změny a chcete ho testovat před provedením úplné přesunout. Mohou chtít zachovat přístup k serveru TFS i po přesunutí ke službám Azure DevOps.
- Mít několik kolekcí a chcete začít s jedním, který má pouze několik projektů pro lepší pochopení procesu.
- Tito uživatelé pochopit, že kolekce sady TFS jsou relace 1: 1 pomocí služby Azure DevOps organizace, tak budou mít více adres URL. Však hodnoty odpovídají jejich aktuální model oddělení základů kódu a projektů.


## <a name="proposed-architecture"></a>Navrhované architektury

- Contoso se projektů TFS se přesunout do cloudu a už hostovat jejich projekty nebo zdroj ovládacího prvku na místě.
- TFS budou migrovat do služby Azure DevOps.
- Contoso má aktuálně jeden TFS kolekci s názvem **ContosoDev**, které se budou migrovat do Azure DevOps služby organizace volá **contosodevmigration.visualstudio.com**.
- Ke službám Azure DevOps se migrovat projekty, pracovní položky, chyby a iterací v loňském roce.
- Contoso bude využívat jejich Azure Active Directory, která nastavené při jejich [nasadili infrastrukturu Azure](contoso-migration-infrastructure.md) na začátku své plánování migrace. 


![Architektura scénáře](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>Proces migrace

Contoso dokončí proces migrace následujícím způsobem:

1. Je zahrnutých spoustu přípravy. Jako první krok je potřeba upgradovat na podporovanou úroveň jejich provádění TFS Contoso. Contoso je aktuálně spuštěna TFS 2017 Update 3, ale k migraci databáze je nutné ho spustit podporovanou verzi 2018 s nejnovějšími aktualizacemi.
2. Po dokončení upgradu bude Contoso spustit nástroj pro migraci TFS a ověřit své kolekce.
3. Contoso se sestavení sady přípravě souborů a provést migraci zkušební spuštění pro testování.
4. Contoso se pak spustí další migraci, tentokrát z úplné migrace, který obsahuje pracovní položky, chyb, které jsou sprintech a kódu.
5. Po migraci společnosti Contoso přejde svůj kód od TFVC ke Gitu.

![Proces migrace](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>Kroky scénáře

Zde je, jak bude Contoso dokončení migrace:

> [!div class="checklist"]
> * **Krok 1: Vytvoření účtu služby Azure storage**: Tento účet úložiště se použije během procesu migrace.
> * **Krok 2: Upgrade serveru TFS**: Contoso upgradují na verzi TFS 2018 Upgrade 2 jejich nasazení. 
> * **Krok 3: Ověření kolekce**: Contoso se ověří kolekce sady TFS během přípravy na migraci.
> * **Krok 4: Sestavení přípravný soubor**: Contoso se vytvoří soubory migrace pomocí nástroje pro migraci TFS. 


## <a name="step-1-create-a-storage-account"></a>Krok 1: Vytvoření účtu úložiště

1. Správce společnosti Contoso na webu Azure Portal vytvořit účet úložiště (**contosodevmigration**).
2. Účet, umístěte do jejich sekundární oblasti, které používají převzetí služeb při selhání – USA (střed). Používají standardní účet pro obecné účely s místně záložní úložiště.

    ![Účet úložiště](./media/contoso-migration-tfs-vsts/storage1.png) 


**Potřebujete další pomoc?**

- [Úvod do služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>Krok 2: Upgrade serveru TFS

Správce společnosti Contoso upgradu serveru TFS na TFS 2018 Update 2. Před zahájením:

- Stažení [TFS 2018 Update 2](https://visualstudio.microsoft.com/downloads/)
- Po ověření, že [požadavky na hardware](https://docs.microsoft.com/tfs/server/requirements)a čtení až [poznámky k verzi](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes) a [upgradovat možná úskalí](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018).

Upgrade následujícím způsobem:

1. Pokud chcete začít, jejich zálohování jejich TFS serveru (běžícího na virtuálním počítači VMware) a pořízení snímku VMware.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. Spustí instalační program sady TFS, a jejich zvolte umístění instalace. Instalační program potřebuje přístup k Internetu.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. Po dokončení instalace, spustí se Průvodce konfigurací serveru.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. Po ověření se upgrade dokončí průvodce.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. Ověřují instalaci TFS kontrolou projekty, pracovním položkám a kódu.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> Některé inovací TFS nutné spustit Průvodce konfigurací funkcí po dokončení upgradu. [Další informace](https://docs.microsoft.com/azure/devops/reference/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**Potřebujete další pomoc?**

Další informace o [upgradu TFS](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>Krok 3: Ověření kolekce sady TFS

Správce společnosti Contoso, spusťte nástroj pro migraci TFS na databázi kolekce ContosoDev abyste ověřili, že před migrací.

1. Stáhněte a rozbalte [nástroj pro migraci TFS](https://www.microsoft.com/download/details.aspx?id=54274). Je důležité si stáhněte si verzi pro aktualizaci TFS, na kterém běží. Verze mohou být vráceny se změnami do konzoly pro správu.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. Spuštění nástrojů k provedení ověřování, tak, že zadáte adresu URL kolekce projektu:

        **TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev**


3. Nástroj zobrazí chybu.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. Nacházejí soubory jsou umístěny v protokolu **protokoly** složky, před nástroj umístění. Soubor protokolu se generuje pro každou hlavní ověření. **TfsMigration.log** obsahuje hlavní informace.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Které se vyskytnout tuto položku se týkající se identit.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Mohou být spuštěny **TfsMigration ověřit/Help** do příkazového řádku a Všimněte si, že příkaz **/tenantDomainName** zdá se, že se vyžaduje kvůli ověření identity.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. Znovu spusťte příkaz pro ověření a vložte tuto hodnotu, spolu s jejich názvem Azure AD: **TfsMigrator ověřit/Collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Azure AD přihlašovací obrazovka se zobrazí a, zadejte přihlašovací údaje globálního správce.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. Ověření projde a je potvrzen nástrojem.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>Krok 4: Vytvoření migrace souborů

Ověřování dokončeno správce společnosti Contoso pomocí nástroje pro migraci TFS k sestavení souborů migrace.

1. Spouštějí se v nástroji pro přípravný krok.

    **Příprava TfsMigrator/Collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![Příprava](./media/contoso-migration-tfs-vsts/prep1.png)

    Připravit následující:
    - Kolekce, kterou chcete najít seznam všech uživatelů vyhledá a naplní protokol identifikovat mapy (**IdentityMapLog.csv**])
    - Připraví připojení k Azure Active Directory se najít shodu pro každou identitu.
    - Contoso má již nasazené služby Azure AD a synchronizovat ji pomocí služby AD Connect, příprava by tak měly být schopen najít odpovídající identit a označit je jako aktivní.

2. Azure AD přihlašovací obrazovka se zobrazí, a zadání přihlašovacích údajů globálního správce.

    ![Příprava](./media/contoso-migration-tfs-vsts/prep2.png)

3. Příprava dokončí, a kterou nástroj hlásí, že úspěšně bylo vygenerováno import souborů.

    ![Příprava](./media/contoso-migration-tfs-vsts/prep3.png)

4. Může vidět, že IdentityMapLog.csv i soubor import.json byly vytvořeny nové složky.

    ![Příprava](./media/contoso-migration-tfs-vsts/prep4.png)

5. Soubor import.json poskytuje nastavení pro import. Obsahuje informace, jako je název požadované organizace a informace o účtu úložiště. Většina polí se vyplní automaticky. Některá pole vyžaduje vstup uživatele. Contoso otevře soubor a přidá název organizace služby Azure DevOps vytvářeného: **contosodevmigration**. S tímto názvem, bude mít adresu URL jejich Azure DevOps služby **contosodevmigration.visualstudio.com**.

    ![Příprava](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > Organizace musí být vytvořen před migrací, lze jej změnit poté, co se migrace dokončí.

6. Jejich kontrola identity mapování souboru protokolu, který zobrazuje účty, které se během importu zařazení do systému Azure DevOps služby. 

    - Aktivní identit najdete identity, které uživatelé ve službách Azure DevOps se stane po dokončení importu.
    - Na služby Azure DevOps tyto identity budou licencovány a zobrazují se jako uživatel v organizaci po migraci.
    - Tyto identity jsou označeny jako **aktivní** v **očekávaný stav importu** sloupec v souboru.

    ![Příprava](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-azure-devops-services"></a>Krok 5: Migrace do služby Azure DevOps

S přípravou na místě můžete teď správce společnosti Contoso soustředit na migraci. Po spuštění migrace, budete se přepnou od používání od TFVC ke Gitu pro správu verzí.

Dříve, než začnou, naplánujte správce výpadků díky vývojový tým se kolekce do offline režimu pro migraci. Toto jsou kroky pro migraci:

1. **Odpojit kolekci**: data identit pro kolekci se nachází v konfigurační databázi pro server TFS kolekce je připojené a online. Pokud je kolekce odpojena od serveru TFS, má kopii dat identity a balíčky s kolekcí pro přenos. Bez těchto dat nelze provést identity část importu. Doporučuje se, že kolekce zůstanou odpojená import byl dokončen, protože neexistuje žádný způsob, jak importovat změny, k nimž došlo při importu.
2. **Generovat zálohy**: dál procesu migrace je potřeba vytvořit zálohu, která může být importován do služby Azure DevOps. Datové vrstvy aplikace součástí balíčků (DACPAC), je funkce systému SQL Server, který umožňuje změny databáze zabalené do jednoho souboru, a nasadit do jiné instance SQL. Můžete také obnovit přímo ke službám Azure DevOps a používá se jako metoda balení pro získání kolekce dat do cloudu. Společnost Contoso použije k vygenerování DACPAC nástroje SqlPackage.exe. Tento nástroj je součástí nástrojů SQL Server Data Tools.
3. **Nahrání do úložiště**: po balíčku DACPAC se vytvoří, odesílají do služby Azure Storage. Po nahrání, dostanou sdíleného přístupového podpisu (SAS), pokud chcete povolit přístup nástroj pro migraci TFS na úložiště.
4. **Vyplňte import**: Contoso potom vyplnit chybějící pole v souboru importu, včetně nastavení balíčku DACPAC. Začít s jejich určíte, že se mají provádět **DryRun** import, chcete-li zkontrolovat, že všechno funguje správně před plné migraci.
5. **Provedení suchého**: zkušební spuštění importy vám pomůže otestovat migrace kolekce. Zkušební spuštění mají omezenou životnost a odstraní se před spuštěním migraci produkčního prostředí. Že se po nastaveném časovém období automaticky odstraní. Poznámka k při spuštění zkušební odstraní je součástí úspěchu přijetí e-mailu po dokončení importu. Všimněte si a podle toho naplánujte.
6. **Dokončení migrace produkčních**: S migrací zkušebnímu dokončit, správce společnosti Contoso provést konečnou migrací aktualizací import.json a spustit import znovu.



### <a name="detach-the-collection"></a>Odpojit kolekci

Před zahájením, správce společnosti Contoso provést místní zálohování serveru SQL Server a VMware snímek serveru TFS před odpojením.

1.  V konzole správce TFS, vyberou se chcete odpojit kolekci (**ContosoDev**).

    ![Migrace](./media/contoso-migration-tfs-vsts/migrate1.png)

2. V **Obecné**, vyberou **odpojení kolekce**

    ![Migrace](./media/contoso-migration-tfs-vsts/migrate2.png)

3. V Průvodci vytvořením kolekce projektu týmu odpojit > **servisní zpráva**, poskytují zprávu pro uživatele, kteří se pokusí připojit k projekty v kolekci.

    ![Migrace](./media/contoso-migration-tfs-vsts/migrate3.png)

4. V **odpojit průběh**, sledování průběhu a klikněte na tlačítko **Další** po dokončení procesu.

    ![Migrace](./media/contoso-migration-tfs-vsts/migrate4.png)

5. V **kontroly připravenosti spouštěné**, až kontrola skončí, klikněte na tlačítko **odpojit**.

    ![Migrace](./media/contoso-migration-tfs-vsts/migrate5.png)

6. Kliknutím na **Zavřít** k dokončení.

    ![Migrace](./media/contoso-migration-tfs-vsts/migrate6.png)

6. Kolekce se už odkazuje v konzole pro správu TFS.

    ![Migrace](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>Generovat DACPAC

Contoso vytvoří zálohu (DACPAC) pro import do služby Azure DevOps.

- SqlPackage.exe v SQL Server Data Tools slouží k vytvoření DACPAC. Existuje více verzí nástroje SqlPackage.exe nainstalované s SQL Server Data Tools, která je umístěna ve složce složky s názvy, například 120, 130 a 140. Je důležité použít správnou verzi Příprava DACPAC.
- TFS 2018 importy potřebovat SqlPackage.exe ve složce 140 nebo vyšší.  CONTOSOTFS, tento soubor se nachází ve složce: **C:\Program Files (x86) \Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Správce společnosti Contoso generovat DACPAC následujícím způsobem:

1. Otevřete příkazový řádek a přejděte do umístění SQLPackage.exe. Zadání tohoto příkazu vygenerujte DACPAC:

    **SqlPackage.exe /sourceconnectionstring: "zdroj dat = SQLSERVERNAME\INSTANCENAME; Initial Catalog = Tfs_ContosoDev; Integrated Security = True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData = true /p: mi gnoreUserLoginMappings = true /p:IgnorePermissions = true /p:Storage = paměti** 

    ![Backup](./media/contoso-migration-tfs-vsts/backup1.png)

2. Po provedení příkazu se zobrazí následující zpráva.

    ![Backup](./media/contoso-migration-tfs-vsts/backup2.png)

3. Po ověření, že vlastnosti DACPACfile

    ![Backup](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Aktualizace souboru do úložiště

Po vytvoření DACPAC Contoso nahraje ho do služby Azure Storage.

1. Jejich [stáhnout a nainstalovat](https://azure.microsoft.com/features/storage-explorer/) Průzkumníka služby Azure Storage.

    ![Odeslat](./media/contoso-migration-tfs-vsts/backup5.png)

4. Připojte se k jejich odběru a vyhledejte účet úložiště, jejich vytvoření na migraci (**contosodevmigration**). Vytvoří nový kontejner objektů blob **azuredevopsmigration**.

    ![Odeslat](./media/contoso-migration-tfs-vsts/backup6.png)

5. Určí soubor DACPAC nahrát jako objekt blob bloku.

    ![Odeslat](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. Po nahrání souboru je klepnutím na název souboru > **generovat SAS**. Rozbalte kontejnery objektů blob v účtu úložiště, vyberte kontejner s import souborů a klikněte na tlačítko **získat sdílený přístupový podpis**.

    ![Odeslat](./media/contoso-migration-tfs-vsts/backup8.png)

8. Přijměte výchozí hodnoty a klikněte na tlačítko **vytvořit**. To umožňuje přístup po dobu 24 hodin.

    ![Odeslat](./media/contoso-migration-tfs-vsts/backup9.png)

9. Jejich zkopírujte sdíleného přístupu podpis adresu URL, tak, aby ho můžete použít nástroj pro migraci TFS.

    ![Odeslat](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> Migrace musí dojít před v povoleném čase okno nebo oprávnění vyprší.
> Není generovat SAS klíč z portálu Azure portal. Klíče generované tímto způsobem jsou rozsahem účtu a nebude fungovat v importu.

### <a name="fill-in-the-import-settings"></a>Zadejte nastavení importu

Dříve správce společnosti Contoso částečně vyplnit, soubor se specifikacemi importu (import.json). Teď se musí přidat zbývající nastavení.

Otevřete soubor import.json a vyplňte následující pole: • umístění: umístění klíče SAS, který byl vygenerován výše.
• Dacpac: nastavte název souboru DACPAC jste nahráli do účtu úložiště. Příponu ".dacpac".
• ImportType: nastavte na DryRun teď.


![Import nastavení](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Proveďte migraci zkušební spuštění

Správce společnosti Contoso začínat zkušebnímu migrace, abyste měli jistotu, že všechno funguje podle očekávání.

1. Otevřete příkazový řádek a vyhledejte umístění TfsMigration (C:\TFSMigrator).
2. Jako první krok ověřují importovaný soubor. Opravdu chtějí je soubor správně naformátován a funkčnost klíče SAS.

    **TfsMigrator importovat /importFile:C:\TFSMigrator\import.json /validateonly**

3. Ověření vrátí chybu, že se klíč SAS se musí delší dobu vypršení platnosti.

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test1.png)

3. Používají Průzkumníka služby Azure Storage k vytvoření nového klíče SAS s koncem platnosti nastavte na sedm dní.

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test2.png)

3. Aktualizujte soubor import.json a spusťte znovu ověření. Tentokrát úspěšně nedokončí.

    **TfsMigrator importovat /importFile:C:\TFSMigrator\import.json /validateonly**

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test3.png)
    
7. Spuštění zkušební spuštění:

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json**

8. Zpráva se vydává potvrdit migraci. Všimněte si, pro který se zachová pracovních dat po spuštění zkušební dobu.

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test4.png)

9. Azure AD přihlásit se zobrazí a by měly být dokončené pomocí přihlášení správce společnosti Contoso.

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test5.png)

10. Zpráva s informacemi o importu.

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test6.png)

11. Po 15 minut, aby, přejděte na adresu URL a přečtěte si následující informace:

     ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test7.png)

12. Po dokončení migrace do Azure DevOps Services zkontroluje, jestli značky Contoso Dev zájemců zkušební spuštění fungovala správně. Po ověření služby Azure DevOps musí několik podrobností potvrďte organizace.

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test8.png)

13. Služby Azure DevOps vedoucí vývoje vidíte, že projekty, které se migrovaly ke službám Azure DevOps. Není k dispozici oznámení o tom, že organizace se odstraní během 15 dnů.

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test9.png)

14. Vedoucí vývoje způsobí otevření jedné z projektů a otevře **pracovních položek** > **Přiřazeno mně**. Ukazuje to, že data pracovní položky je dokončená, spolu s identity.

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test10.png)

15. Zájemce také kontroluje dalších projektů a kódu, potvrďte, že zdrojový kód a historie je dokončená.

    ![Zkušební spuštění](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Spustit migraci produkčního prostředí

Díky kompletní zkušební spuštění Správce společnosti Contoso přejít migraci produkčního prostředí. Jejich odstranit zkušební spuštění, aktualizujte nastavení importu a spustit import znovu.

1. Na portálu služby Azure DevOps se odstranit zkušebnímu organizace.
2. Aktualizujte soubor import.json nastavit **ImportType** k **ProductionRun**.

    ![Výroba](./media/contoso-migration-tfs-vsts/full1.png)

3. Zahájit migraci, stejně jako zkušební spuštění: **TfsMigrator import /importFile:C:\TFSMigrator\import.json**.
4. Zpráva zobrazí potvrďte migraci a vás upozorní, že data může uchovávat na bezpečném místě jako pracovní oblasti po dobu až sedmi dnů.

    ![Výroba](./media/contoso-migration-tfs-vsts/full2.png)

5. V Azure AD přihlásit určí přihlášení správce společnosti Contoso.

    ![Výroba](./media/contoso-migration-tfs-vsts/full3.png)

6. Zpráva s informacemi o importu.

    ![Výroba](./media/contoso-migration-tfs-vsts/full4.png)

7. Po přibližně 15 minut, přejděte na adresu URL a zobrazí následující informace:

    ![Výroba](./media/contoso-migration-tfs-vsts/full5.png)

8. Po dokončení migrace, vedoucí vývoje Contoso přihlásí Azure DevOps služby zkontrolujte, že migrace fungovala správně. Po přihlášení si můžete zobrazit migrovaly projekty.

    ![Výroba](./media/contoso-migration-tfs-vsts/full6.png)

8. Vedoucí vývoje způsobí otevření jedné z projektů a otevře **pracovních položek** > **Přiřazeno mně**. Ukazuje to, že data pracovní položky je dokončená, spolu s identity.

    ![Výroba](./media/contoso-migration-tfs-vsts/full7.png)

9. Zájemce zkontroluje jiných pracovních položek k potvrzení.

    ![Výroba](./media/contoso-migration-tfs-vsts/full8.png)

15. Zájemce také kontroluje dalších projektů a kódu, potvrďte, že zdrojový kód a historie je dokončená.

    ![Výroba](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>Přesunutí správy zdrojového kódu od TFVC ke GITU

Pomocí migrace byla dokončena Contoso chce přesunout od TFVC ke Gitu pro správu zdrojového kódu. Je třeba importovat zdrojový kód aktuálně ve své organizaci DevOps služby Azure jako úložiště Git ve stejné organizaci.

1. Na portálu Azure DevOps Services otevřou, jedno úložiště TFVC (**$/ PolicyConnect**) a zkontrolujte ji.

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. Kliknutí **zdroj** rozevíracího seznamu > **Import**.

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. V **typ zdroje** vyberou **TFVC**a zadejte cestu k úložišti. Jste se rozhodli není pro migraci historie.

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > Z důvodu rozdílů v tom, jak TFVC a Git ukládat informace o správě verzí doporučujeme, že Contoso není migrace historie. Jedná se o postup, který přijal Microsoft při její migraci Windows a další produkty od centralizované správy verzí na Git.

4. Po dokončení importu správci, projděte si kód.

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. Jejich postupujte stejně jako u druhé úložiště (**$/ SmartHotelContainer**).

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. Po kontrole zdroj, Dev vede souhlas s tím, že se provádí migraci do služby Azure DevOps. Služby Azure DevOps teď bude zdroj pro všechny vývoje v rámci týmů účastnících migrace.

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**Potřebujete další pomoc?**

[Další informace](https://docs.microsoft.com/azure/devops/repos/git/import-from-TFVC?view=vsts) import od TFVC.

##  <a name="clean-up-after-migration"></a>Vyčištění po migraci

Pomocí migrace byla dokončena Contoso potřebuje provést následující kroky:

- Zkontrolujte [po importu](https://docs.microsoft.com/azure/devops/articles/migration-post-import?view=vsts) informace o importu další aktivity.
- Odstranit úložiště TFVC, nebo je umístit do režimu jen pro čtení. Kód, který nesmí základních tříd používá, ale může být uvedené v jejich historie.

## <a name="next-steps"></a>Další postup

Contoso potřebovat k poskytnutí školení Azure DevOps Services a Gitem příslušné adaptéry.



