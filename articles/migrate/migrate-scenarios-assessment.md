---
title: Posouzení vhodnosti místních úloh k migraci do Azure pomocí DMA a služby Azure Migrate | Microsoft Docs
description: Zjistěte, jak připravit Azure na migraci místních počítačů pomocí nástroje Data Migration Assistant (DMA) a služby Azure Migrate.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/18/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0d8ef36e001aaf417b84efaf99a992fd64f01b6f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366337"
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>Scénář 1: Posouzení vhodnosti místních úloh k migraci do Azure

V rámci zvažování migrace do Azure chtějí ve společnosti Contoso provést interní technické a finanční hodnocení, aby zjistili, jestli jsou jejich místní úlohy vhodné k migraci do cloudu. Konkrétně chtějí posoudit kompatibilitu počítačů a databází s migrací a odhadnout kapacitu a náklady, které bude provoz jejich prostředků v Azure vyžadovat.

Pro začátek a lepší pochopení příslušných technologií posuzují a migrují malou místní cestovní aplikaci. Jedná se o dvouvrstvou aplikaci, kdy na jednom virtuálním počítači běží webová aplikace a na druhém databáze SQL Serveru. Aplikace je nasazená ve VMware a správu prostředí zajišťuje vCenter Server. Posouzení provedou pomocí nástroje Data Migration Assistant (DMA) a služby Azure Migrate. Pokud byste chtěli takový scénář vyzkoušet na této ilustrativní cestovní aplikaci, můžete si ji stáhnout z [GitHubu](https://github.com/Microsoft/SmartHotel360).

**Technologie** | **Popis** | **Náklady**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA vyhodnocuje a detekuje problémy s kompatibilitou, které můžou ovlivnit fungování databáze v Azure. Kromě toho posuzuje paritu funkcí mezi zdrojem a cílem SQL Serveru a doporučuje vylepšení výkonu a spolehlivosti pro cílové prostředí. | Tento nástroj je zdarma ke stažení.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Tato služba pomáhá posuzovat vhodnost místních počítačů k migraci do Azure. Posuzuje vhodnost počítačů k migraci a poskytuje odhady velikostí a nákladů, které bude vyžadovat jejich provoz v Azure. V současné době může služba Azure Migrate posuzovat vhodnost k migraci do Azure u místních virtuálních počítačů VMware. | V současné době (duben 2018) se za používání této služby neplatí žádné poplatky.
[Mapa služeb](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate využívá mapu služeb k zobrazení závislostí mezi počítači, které chcete migrovat. |  Mapa služeb je součástí Azure Log Analytics. V současné době je možné ji používat po dobu 180 dnů bez poplatků.

V tomto scénáři stáhneme a spustíme DMA za účelem posouzení místní databáze SQL Serveru pro naši cestovní aplikaci. Pomocí služby Azure Migrate a mapování závislostí posoudíme virtuální počítače s aplikací před jejich migrací do Azure.

> [!NOTE]
> Pro účely tohoto scénáře bude naším cílem posouzení pro databázi SQL Server na virtuálním počítači Azure. V našem dalším článku se scénářem však provedeme migraci do spravované instance Azure SQL. Tento přístup používáme, protože DMA aktuálně nepodporuje posouzení pro cíl spravované instance Azure SQL.

## <a name="architecture"></a>Architektura


![Architektura posouzení migrace](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

V tomto scénáři:
- Contoso je smyšlený název představující typickou podnikovou organizaci. Contoso chce vyhodnotit a migrovat svou místní dvouvrstvou cestovní aplikaci.
- Contoso má místní datacentrum (**contoso-datacenter**) s místním řadičem domény ( **contosodc1**).
- Interní cestovní aplikace je rozvrstvená na dva virtuální počítače **WEBVM** a **SQLVM** a umístěná na hostiteli VMware ESXi **contosohost1.contoso.com**.
- Správu prostředí VMware zajišťuje vCenter Server (**vcenter.contoso.com**) spuštěný na virtuálním počítači.




## <a name="prerequisites"></a>Požadavky

Tady je seznam všeho, co k nasazení tohoto scénáře potřebujete:

- Místní server vCenter verze 5.5, 6.0 nebo 6.5.
- Účet jen pro čtení na serveru vCenter nebo oprávnění k jeho vytvoření.
- Oprávnění k vytvoření virtuálního počítače na serveru vCenter pomocí šablony .OVA.
- Alespoň jednoho hostitele ESXi verze 5.0 nebo novější.
- Alespoň dva místní virtuální počítače VMware, na jednom z nichž běží databáze SQL Serveru.
- Na obou virtuálních počítačích byste měli mít oprávnění k instalaci agentů Azure Migrate.
- Virtuální počítače by měly mít přímé připojení k internetu.
        – Internetový přístup můžete omezit na [požadované adresy URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        – Pokud máte počítače bez připojení k internetu, musíte na ně stáhnout a nainstalovat [bránu OMS](../log-analytics/log-analytics-oms-gateway.md).
- Plně kvalifikovaný název domény virtuálního počítače, na kterém je spuštěná instance SQL Serveru, pro účely posouzení databáze.
- Brána Windows Firewall na virtuálním počítači s SQL Serverem by měla umožňovat externí připojení na portu TCP 1433 (výchozí nastavení), aby se nástroj DMA mohl připojit.


## <a name="scenario-overview"></a>Přehled scénáře

Tady je přehled toho, co uděláme:


> [!div class="checklist"]
> * **Krok 1: Příprava Azure.** Vše, co potřebujeme, je předplatné Azure.
> * **Krok 2: Stažení a instalace DMA.** Příprava DMA na posouzení místní databáze SQL Serveru.
> * **Krok 3: Posouzení databáze.** Spuštění a analýza posouzení databáze.
> * **Krok 4: Příprava na posouzení virtuálních počítačů pomocí služby Azure Migrate.** Nastavení místních účtů a úprava nastavení VMware.
> * **Krok 5: Vyhledání místních virtuálních počítačů.** Vytvoření virtuálního počítače kolektoru Azure Migrate. Následné spuštění kolektoru pro vyhledání virtuálních počítačů k posouzení.
> * **Krok 6: Příprava na analýzu závislostí.** Instalace agentů Azure Migrate na virtuální počítače za účelem zjištění mapování závislostí mezi virtuálními počítači.
> * **Krok 7: Posouzení virtuálních počítačů.** Kontrola závislostí, seskupení virtuálních počítačů a spuštění posouzení. Analýza posouzení v rámci přípravy na migraci, jakmile bude připravené.


## <a name="step-1-prepare-azure"></a>Krok 1: Příprava Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

- Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.
- Pokud použijete existující předplatné a nejste jeho správcem, musíte kontaktovat jeho správce, aby vám přidělil oprávnění správce nebo přispěvatele k předplatnému nebo ke skupině prostředků, které pro účely tohoto scénáře použijete.


## <a name="step-2-download-and-install-the-dma"></a>Krok 2: Stažení a instalace DMA

1. Stáhněte DMA z webu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Nástroj Assistant můžete nainstalovat na jakýkoli počítač s možností připojení k instanci SQL. Není potřeba ho spouštět na počítači s SQL Serverem.
    - Neměli byste ho spouštět na hostitelském počítači SQL Serveru.
2. Dvakrát klikněte na stažený instalační soubor (DownloadMigrationAssistant.msi) a spusťte instalaci.
3. Na stránce **Dokončit** se ujistěte, že je vybraná možnost **Spustit nástroj Microsoft Data Migration Assistant**, a klikněte na **Dokončit**.

## <a name="step-3-run-and-analyze-the-database-assessment"></a>Krok 3: Spuštění a analýza posouzení databáze

Spusťte posouzení, které analyzuje vaši zdrojovou instanci SQL Serveru proti zadanému cíli.

1. V části **New** (Nový) vyberte **Assessment** (Posouzení) a zadejte název projektu posouzení.
2. V části **Source server type** (Typ zdrojového serveru) vyberte **SQL Server**. V části **Target server type** (Typ cílového serveru) vyberte **SQL Server on Azure Virtual Machines** (SQL Server na virtuálních počítačích Azure).

    ![Výběr zdroje](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      V současné době DMA nepodporuje posouzení pro migraci do spravované instance SQL. Jako alternativní řešení používáme pro posouzení jako předpokládaný cíl SQL Server na virtuálním počítači Azure.

1.  V části **Select Target Version** (Výběr cílové verze) zadejte cílovou verzi SQL Serveru, kterou chcete spustit v Azure, a co chcete při posuzování zjistit:
    - **Compatibility Issues** (Problémy s kompatibilitou) vás informuje o změnách, které můžou narušit migraci nebo které před migrací vyžadují menší úpravu. Informuje vás také o všech funkcích, které aktuálně používáte a které jsou zastaralé. Problémy jsou uspořádané podle úrovně kompatibility.
    - **New features' recommendation** (Doporučení nových funkcí) vás informuje o nových funkcích na cílové platformě SQL Serveru, které se po migraci můžou použít pro vaši databázi. Tyto funkce jsou uspořádané podle výkonu, zabezpečení a úložiště.

    ![Výběr cíle](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. V části **Connect to a server** (Připojení k serveru) zadejte název počítače, na kterém je spuštěná instance SQL Serveru, typ ověřování a podrobnosti o připojení. Pak klikněte na **Connect** (Připojit).

    ![Výběr cíle](./media/migrate-scenarios-assessment/dma-assessment-3.png)

3. V části **Add source** (Přidání zdroje) vyberte databázi, kterou chcete posoudit, a klikněte na **Add** (Přidat).
4. Vytvoří se posouzení s názvem, který jste zadali.

    ![Vytvoření posouzení](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  Kliknutím na **Next** (Další) spusťte posouzení.
6. V části **Review Results** (Kontrola výsledků) se zobrazí výsledky posuzovacích testů, které jste zapnuli.


### <a name="analyze-the-database-assessment"></a>Analýza posouzení databáze

Jakmile budou k dispozici, v nástroji Assistant se zobrazí výsledky.

1. V sestavě **Compatibility Issues** (Problémy s kompatibilitou) zkontrolujte, jestli má vaše databáze problémy na jednotlivých úrovních kompatibility. Pokud ano, přečtěte si, jak je opravit. Mapování úrovní kompatibility na verze SQL Serveru je následující:
    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Problémy s kompatibilitou](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. V sestavě **Feature recommendations** (Doporučení funkcí) si prohlédněte funkce výkonu, zabezpečení a úložiště, které posouzení doporučuje po migraci nakonfigurovat. Doporučuje se celá řada funkcí, včetně funkcí Columnstore a OLTP v paměti, Stretch Database, Always Encrypted, Dynamického maskování dat databáze SQL a transparentního šifrování dat.

    ![Doporučení funkcí](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. Pokud opravíte některé problémy, kliknutím na **Restart Assessment** (Restartovat posouzení) znovu spusťte posouzení.
4. Kliknutím na **Export report** (Exportovat sestavu) získáte sestavu posouzení ve formátu JSON nebo CSV.

Pokud spouštíte posouzení většího rozsahu:

- Můžete spustit několik posouzení současně a zobrazit jejich stav na stránce **All Assessments** (Všechna posouzení).
- Můžete [konsolidovat posouzení do databáze SQL Serveru](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- Můžete [konsolidovat posouzení do sestavy PowerBI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>Krok 4: Příprava na posouzení virtuálních počítačů pomocí služby Azure Migrate

Vytvořte účet VMware, který služba Azure Migrate použije k automatickému zjišťování virtuálních počítačů k posouzení, ověřte, že máte oprávnění k vytvoření virtuálního počítače, poznamenejte si porty, které je potřeba otevřít, a nastavte úroveň nastavení statistiky.

### <a name="set-up-a-vmware-account"></a>Nastavení účtu VMware

 Potřebujete účet jen pro čtení ve vCenter. Pokud ho nemáte, vytvořte účet VMware s následujícími vlastnostmi:

- Typ uživatele: Alespoň uživatel jen pro čtení.
- Oprávnění: Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only.
- Podrobnosti: Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v tomto datacentru.
- Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.

### <a name="verify-permissions-to-create-a-vm"></a>Ověření oprávnění k vytvoření virtuálního počítače

Importováním souboru ve formátu .OVA zkontrolujte, že máte oprávnění k vytvoření virtuálního počítače. [Další informace](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Ověření portů

V tomto scénáři nakonfigurujeme mapování závislostí. Tato funkce vyžaduje, aby na posuzovaných virtuálních počítačích byl nainstalovaný agent. Tento agent se na jednotlivých virtuálních počítačích musí být schopný připojit k Azure z portu TCP 443. [Další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) o požadavcích na připojení.


### <a name="set-statistics-settings"></a>Nastavení statistiky

Než začnete s nasazením, statistika pro vCenter Server by měla být nastavená na úroveň 3. Poznámky:
- Po nastavení úrovně je potřeba před spuštěním posouzení alespoň den počkat. Jinak nemusí fungovat podle očekávání.
- Pokud je úroveň vyšší než 3, posouzení bude fungovat, ale:
    - Nebudou se shromažďovat data o výkonu disků a sítí.
    - V případě úložiště služba Azure Migrate doporučí v Azure standardní disk stejné velikosti jako místní disk.
    - V případě sítí se pro každý místní síťový adaptér doporučí síťový adaptér v Azure.
    - V případě výpočetních prostředků služba Azure Migrate zjistí počet jader a velikost paměti virtuálního počítače a doporučí virtuální počítač Azure se stejnou konfigurací. Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.


[Další informace](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) o určování velikosti u úrovně 3.

Nastavte úroveň následujícím způsobem:

1. Ve webovém klientovi vSphere otevřete instanci serveru vCenter.
2. Vyberte kartu **Manage** (Správa) a v části **Settings** (Nastavení) klikněte na **General** (Obecné).
3. Klikněte na **Edit** (Upravit) a v části **Statistics** (Statistika) nastavte úroveň nastavení statistiky na **Level 3** (Úroveň 3).

    ![Úroveň statistiky vCenter](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>Krok 5: Vyhledání virtuálních počítačů

Vytvořte projekt Azure Migrate a stáhněte a nastavte virtuální počítač kolektoru. Pak spusťte kolektor, který vyhledá vaše virtuální počítače.

### <a name="create-a-project"></a>Vytvoření projektu

1. Přihlaste se k webu [Azure Portal ](https://portal.azure.com) a klikněte na **Vytvořit prostředek**.
2. Vyhledejte **Azure Migrate**. Ve výsledcích hledání vyberte **Azure Migrate** a klikněte na **Vytvořit**.
3. Zadejte název projektu a předplatné Azure.
4. Vytvořte novou skupinu prostředků.
5. Zadejte umístění projektu a klikněte na **Vytvořit**.

    - Projekt Azure Migrate můžete vytvořit pouze v oblasti Střed USA – západ nebo USA – východ.
    - Migraci můžete naplánovat pro jakékoli cílové umístění.
    - Umístění projektu slouží pouze k uložení metadat shromážděných z místních virtuálních počítačů.

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)




### <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Azure Migrate vytvoří místní virtuální počítač, kterému se říká zařízení kolektoru. Tento virtuální počítač vyhledá místní virtuální počítače VMware a odešle jejich metadata do služby Azure Migrate. Pro nastavení zařízení kolektoru je nutné stáhnout soubor .OVA, importovat ho do místního vCenter Serveru a vytvořit tak virtuální počítač.

1. V projektu služby Azure Migrate klikněte na **Začínáme** > **Zjistit a posoudit** > **Zjistit počítače**.
2. V nabídce **Zjistit počítače** klikněte na **Stáhnout**. Tím stáhnete soubor .OVA.
3. V části **Kopírování přihlašovacích údajů projektu** zkopírujte ID a klíč projektu. Budete je potřebovat při konfiguraci kolektoru.

    ![Stažení souboru .OVA](./media/migrate-scenarios-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Než nasadíte soubor .OVA, zkontrolujte, jestli je bezpečný.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Vygenerovaná hodnota hash by měla odpovídat následujícímu nastavení (verze 1.0.9.8):

    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff


### <a name="create-the-collector-appliance"></a>Vytvoření zařízení kolektoru

Importujte stažený soubor do vCenter Serveru.

1. V konzoli vSphere Client klikněte na **File** (Soubor) > **Deploy OVF Template** (Nasadit šablonu OVF).

    ![Nasazení šablony OVF](./media/migrate-scenarios-assessment/vcenter-wizard.png)

2. V části Deploy OVF Template Wizard (Průvodce nasazením šablony OVF) > **Source** (Zdroj) zadejte umístění souboru .OVA a klikněte na **Next** (Další).
3. V části **OVF Template Details** (Podrobnosti o šabloně OVF) klikněte na **Next** (Další). V části **End User License Agreement** (Licenční smlouva s koncovým uživatelem) kliknutím na **Accept** (Přijmout) přijměte smlouvu a pak klikněte na **Next** (Další).
4. V části **Name and Location** (Název a umístění) zadejte popisný název virtuálního počítače kolektoru a umístění v inventáři, ve kterém bude daný virtuální počítač hostovaný, a pak klikněte na **Next** (Další). Zadejte hostitele nebo cluster, na kterém se bude zařízení kolektoru spouštět.
5. V části **Storage** (Úložiště) zadejte, kam chcete uložit soubory pro zařízení, a klikněte na **Next** (Další).
6. V části **Disk Format** (Formát disku) zadejte, jak chcete úložiště zřídit.
7. V části **Network Mapping** (Mapování sítě) zadejte síť, ke které se bude virtuální počítač kolektoru připojovat. Aby mohla síť odesílat metadata do Azure, potřebuje připojení k internetu.
8. V části **Ready to Complete** (Připraveno k dokončení) zkontrolujte nastavení, vyberte **Power on after deployment** (Spustit po nasazení) a pak klikněte na **Finish** (Dokončit).

Po vytvoření zařízení se zobrazí zpráva potvrzující úspěšné dokončení.

### <a name="run-the-collector-to-discover-vms"></a>Spuštění kolektoru pro vyhledání virtuálních počítačů

Než začnete, mějte na paměti, že kolektor v současné době podporuje jako jazyk operačního systému a jazyk rozhraní kolektoru pouze angličtinu (Spojené státy).

1. V konzoli vSphere Client klikněte pravým tlačítkem na daný virtuální počítač a pak na **Open Console** (Otevřít konzolu).
2. Nastavte pro zařízení preferovaný jazyk, časové pásmo a heslo.
3. Na ploše klikněte na zástupce **Spustit kolektor**.

    ![Zástupce kolektoru](./media/migrate-scenarios-assessment/collector-shortcut.png)

4. Ve službě Azure Migrate Collector otevřete nabídku **Nastavit požadavky**.
    - Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - Kolektor zkontroluje, že má virtuální počítač připojení k internetu, synchronizaci času a spuštění služby kolektoru (na virtuálním počítači je nainstalovaná ve výchozím nastavení). Také zkontroluje, že je nainstalované VMware PowerCLI.

    > [!NOTE]
    > Předpokládáme, že má virtuální počítač přímý přístup k internetu bez proxy.

    ![Ověření požadavků](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)


5. V části **Zadejte podrobnosti vCenter Serveru** udělejte toto:
    - Zadejte název (plně kvalifikovaný název domény) nebo IP adresu vCenter Serveru.
    - V části **Uživatelské jméno** a **Heslo** zadejte přihlašovací údaje k účtu jen pro čtení, který kolektor použije ke zjištění virtuálních počítačů na serveru vCenter.
    - V části **Vyberte rozsah** vyberte rozsah zjišťování virtuálních počítačů. Kolektor může vyhledat jen virtuální počítače v rámci zadaného rozsahu. Jako rozsah můžete vybrat konkrétní složku, datové centrum nebo cluster. Neměl by obsahovat víc než 1500 virtuálních počítačů.

    ![Připojení k vCenter](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. V části **Zadejte projekt migrace** zadejte ID projektu služby Azure Migrate a klíč, který jste zkopírovali z portálu. Pokud jste ho nezkopírovali, na virtuálním počítači kolektoru otevřete Azure Portal. Na stránce **Přehled** projektu klikněte na **Zjistit počítače** a zkopírujte příslušné hodnoty.  

    ![Připojení k Azure](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. V části **Zobrazit průběh shromažďování** můžete sledovat zjišťování a kontrolovat, jestli metadata shromážděná z virtuálních počítačů patří do zadaného rozsahu. Kolektor vás informuje o tom, jak dlouho bude zjišťování přibližně trvat.

    ![Probíhající shromažďování](./media/migrate-scenarios-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení shromažďování zkontrolujte, že se virtuální počítače zobrazí na portálu.

1. V projektu Azure Migrate klikněte na **Spravovat** > **Počítače**.
2. Zkontrolujte, jestli se na stránce zobrazí virtuální počítače, které jste chtěli vyhledat.

    ![Zjištěné počítače](./media/migrate-scenarios-assessment/discovery-complete.png)

3. Všimněte si, že na počítačích aktuálně nejsou nainstalovaní agenti Azure Migrate. Musíme je nainstalovat, abychom mohli zobrazit závislosti.

    ![Zjištěné počítače](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>Krok 6: Příprava na analýzu závislostí

Abychom mohli zobrazit závislosti mezi virtuálními počítači, které chceme posoudit, stáhneme a nainstalujeme agenty na virtuální počítače s webovou aplikací (WEBVM a SQLVM).

### <a name="take-a-snapshot"></a>Pořízení snímku

Pokud před úpravou virtuálního počítače chcete mít jeho kopii, před instalací agentů pořiďte jeho snímek.

![Snímek počítače](./media/migrate-scenarios-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače

1.  V projektu Azure Migrate na stránce **Počítače** vyberte počítač a klikněte na **Vyžaduje instalaci** ve sloupci **Závislosti**.
2.  Na stránce **Zjistit počítače** pro jednotlivé virtuální počítače stáhněte a nainstalujte agenta Microsoft Monitoring Agent (MMA) a agenta závislostí.
3.  Zkopírujte ID a klíč pracovního prostoru. Budete je potřebovat při instalaci agenta MMA.

    ![Stažení agenta](./media/migrate-scenarios-assessment/download-agents.png)



#### <a name="install-the-mma"></a>Instalace agenta MMA

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **Licenční podmínky** kliknutím na **Souhlasím** přijměte licenci.
3. V části **Cílová složka** ponechte výchozí složku instalace a klikněte na **Další**.
4. V části **Možnosti instalace agenta** vyberte **Připojit agenta k Azure Log Analytics** > **Další**.

    ![Instalace agenta MMA](./media/migrate-scenarios-assessment/mma-install.png)
5. V části **Azure Log Analytics** vložte ID a klíč pracovního prostoru, které jste zkopírovali z portálu. Klikněte na **Další**.
    ![Instalace agenta MMA](./media/migrate-scenarios-assessment/mma-install2.png)

6. V části **Připraveno k instalaci** nainstalujte agenta MMA.



#### <a name="install-the-dependency-agent"></a>Instalace agenta závislostí

1.  Dvakrát klikněte na staženého agenta závislostí.
2.  Na stránce **Licenční podmínky** kliknutím na **Souhlasím** přijměte licenci.
3.  V části **Instalace** počkejte na dokončení instalace. Pak klikněte na tlačítko **Další**.

    ![Agent závislostí](./media/migrate-scenarios-assessment/dependency-agent.png)



## <a name="step-7-run-and-analyze-the-vm-assessment"></a>Krok 7: Spuštění a analýza posouzení virtuálních počítačů

Ověřte závislosti počítačů a vytvořte skupinu. Pak spusťte posouzení.

### <a name="verify-dependencies-and-create-a-group"></a>Ověřte závislosti a vytvořte skupinu.

1.  Na stránce **Počítače** klikněte na **Zobrazit závislosti** u virtuálních počítačů, které chcete analyzovat.

    ![Zobrazení závislostí počítačů](./media/migrate-scenarios-assessment/view-machine-dependencies.png)

2. Pro virtuální počítač SQLVM se na mapě závislostí zobrazí následující podrobnosti:

    - Spuštěné skupiny procesů nebo procesy s aktivními síťovými připojeními na virtuálním počítači SQLVM v zadaném časovém období (ve výchozím nastavení je to hodina).
    - Příchozí (klient) připojení přes protokol TCP ke všem závislým počítačům a odchozí (server) připojení přes protokol TCP ze všech závislých počítačů.
    - Závislé počítače s nainstalovanými agenty Azure Migrate se zobrazí v samostatných polích.
    - U počítačů bez nainstalovaných agentů se zobrazí informace o portu a IP adrese.

 3. U počítačů s nainstalovaným agentem (WEBVM) můžete kliknutím na pole počítače zobrazit další informace, včetně plně kvalifikovaného názvu domény, operačního systému a adresy MAC.

    ![Zobrazení skupinových závislostí](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. Teď vyberte virtuální počítače, které chcete přidat do skupiny (SQLVM a WEBVM).  Stisknutím klávesy CTRL a kliknutím můžete vybrat několik virtuálních počítačů.
5. Klikněte na **Vytvořit skupinu** a zadejte název (smarthotelapp).

> [!NOTE]
    > Pokud chcete zobrazit podrobnější závislosti, můžete rozšířit časový rozsah. Můžete vybrat konkrétní dobu nebo počáteční a koncové datum.


### <a name="run-an-assessment"></a>Spuštění posouzení


1. Na stránce **Skupiny** otevřete skupinu (smarthotelapp).
2. Klikněte na **Vytvořit posouzení**.

    ![Vytvoření posouzení](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. Posouzení se zobrazí na stránce **Správa** > **Posouzení**.


### <a name="modify-assessment-settings"></a>Úprava nastavení posouzení

Pro účely tohoto kurzu jsme použili výchozí nastavení posouzení. Nastavení však můžete upravit následujícím způsobem:

1. Na stránce **Posouzení** projektu migrace vyberte posouzení a klikněte na **Upravit vlastnosti**.
2. Upravte vlastnosti podle následující tabulky:

    **Nastavení** | **Podrobnosti** | **Výchozí**
    --- | --- | ---
    **Cílové umístění** | Umístění Azure, do kterého chcete migrovat. | Žádná výchozí hodnota
    **Redundance úložiště** | Typ redundance úložiště, který budou po migraci využívat virtuální počítače Azure. | Výchozí hodnota je [Místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md). Azure Migrate podporuje pouze posouzení založená na spravovaných discích a spravované disky podporují pouze LRS, proto výběr možnosti LRS.
    **Kritérium určení velikosti** | Kritérium, podle kterého Azure Migrate určí správnou velikost virtuálních počítačů pro Azure. Můžete provést určení velikosti *na základě výkonu* nebo použít velikost virtuálních počítačů *jako v místním prostředí* bez ohledu na historii výkonu. | Výchozí možnost je určení velikosti na základě výkonu.
    **Historie výkonu** | Doba, která se má zohlednit při vyhodnocování výkonu virtuálních počítačů. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*. | Výchozí hodnota je jeden den.
    **Percentilové využití** | Hodnota percentilu sady vzorků výkonu, která se má zohlednit při určování správné velikosti. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*.  | Výchozí hodnota je 95. percentil.
    **Cenová úroveň** | Můžete zadat [cenovou úroveň (Basic nebo Standard)](../virtual-machines/windows/sizes-general.md) cílových virtuálních počítačů Azure. Pokud například plánujete migrovat produkční prostředí, měli byste zvážit úroveň Standard, která poskytuje virtuální počítače s nízkou latencí, ale může být dražší. Na druhou stranu, pokud máte prostředí pro vývoj a testování, měli byste zvážit úroveň Basic s virtuálními počítači s vyšší latencí, která je levnější. | Ve výchozím nastavení se použije úroveň [Standard](../virtual-machines/windows/sizes-general.md).
    **Faktor komfortu** | Azure Migrate při posuzování počítá s rezervou (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry. | Výchozí nastavení je 1,3×.
    **Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. | Výchozí hodnota je [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Měna** | Fakturační měna. | Výchozí hodnota je USD.
    **Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure. | Výchozí nastavení je 0 %.
    **Zvýhodněné hybridní využití Azure** | Zadejte, jestli máte Software Assurance a nárok na [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. | Výchozí hodnota je Yes (Ano).

3. Kliknutím na **Uložit** aktualizujte nastavení posouzení.


### <a name="analyze-the-vm-assessment"></a>Analýza posouzení virtuálních počítačů

Posouzení služby Azure Migrate obsahuje informace o kompatibilitě místních virtuálních počítačů s Azure, navrhované správné velikosti pro virtuální počítače Azure a odhadovaných měsíčních nákladech na Azure.

![Sestava posouzení](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

![Zobrazení posouzení](./media/migrate-scenarios-assessment/assessment-display.png)

Vaše posouzení obdrží hodnocení spolehlivosti od 1 hvězdičky až po 5 hvězdiček (1 hvězdička je nejnižší a 5 hvězdiček je nejvyšší).
- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Toto hodnocení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti je užitečné v případě, že provádíte *určování velikosti na základě výkonu*, protože Azure Migrate k určení velikosti na základě výkonu nemusí mít k dispozici dostatek datových bodů. V případě *určování stejné velikosti jako v místním prostředí* je hodnocení spolehlivosti vždy 5 hvězdiček, protože Azure Migrate má k dispozici všechny datové body, které k určení velikosti virtuálního počítače potřebuje.
- Tady je poskytnuté hodnocení spolehlivosti posouzení v závislosti na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček

#### <a name="verify-readiness"></a>Ověření připravenosti

![Posouzení připravenosti](./media/migrate-scenarios-assessment/azure-readiness.png)  

V sestavě posouzení se zobrazí tabulka se souhrnem informací. Mějte na paměti, že k zobrazení velikostí určených na základě výkonu potřebuje služba Azure Migrate následující informace. Pokud tyto informace není možné shromáždit, posouzení velikostí nemusí být přesné.

- Data o využití procesoru a paměti.
- Informace o vstupně-výstupních operacích čtení a zápisu za sekundu a propustnosti pro všechny disky připojené k virtuálnímu počítači.
- Informace o síťových vstupech a výstupech pro všechny síťové adaptéry připojené k virtuálnímu počítači.


**Nastavení** | **Indikace** | **Podrobnosti**
--- | --- | ---
**Připravenost virtuálního počítače pro Azure** | Určuje, jestli je virtuální počítač připravený k migraci. | Možné stavy:</br><br/>– Připraveno pro Azure<br/><br/>– Připraveno s podmínkami <br/><br/>– Nepřipraveno pro Azure<br/><br/>– Připravenost neznámá<br/><br/> Pokud virtuální počítač není připravený, zobrazí se několik kroků pro odstranění problému.
**Velikost virtuálního počítače Azure** | Pro připravené virtuální počítače se zobrazí doporučená velikost virtuálního počítače Azure. | Doporučení velikosti závisí na vlastnostech posouzení:<br/><br/>– Pokud jste použili určení velikosti na základě výkonu, při určování velikosti se zohlední historie výkonu virtuálních počítačů.<br/><br/>– Pokud jste použili určení stejné velikosti jako v místním prostředí, určení velikosti bude založené na velikosti místního virtuálního počítače a data o využití se nepoužijí.
**Navrhovaný nástroj** | Vzhledem k tomu, že jsou na našich počítačích spuštění agenti, služba Azure Migrate prozkoumá spuštěné procesy v rámci počítače a určí, jestli daný počítač hostuje databázi, nebo ne.
**Informace o virtuálním počítači** | Tato sestava ukazuje nastavení místního virtuálního počítače, včetně informací o operačním systému, typu spouštění, discích a úložišti.




#### <a name="review-monthly-cost-estimates"></a>Kontrola odhadů měsíčních nákladů

Toto zobrazení informuje o celkových nákladech na výpočetní kapacitu a úložiště, které s sebou nese provoz virtuálních počítačů v Azure. Také nabízí podrobné údaje o jednotlivých počítačích.

![Posouzení připravenosti](./media/migrate-scenarios-assessment/azure-costs.png)

- Při výpočtu odhadovaných nákladů se používají doporučené velikosti počítačů.
- Odhadované měsíční náklady na výpočetní kapacitu a úložiště jsou agregované pro všechny virtuální počítače dané skupiny.


## <a name="conclusion"></a>Závěr

V tomto scénáři jsme provedli následující:

> [!div class="checklist"]
> * Posouzení místní databáze pomocí nástroje DMA
> * Posouzení místních virtuálních počítačů pomocí služby Azure Migrate a s využitím mapování závislostí
> * Kontrola posouzení za účelem ověření připravenosti místních prostředků na migraci do Azure

## <a name="next-steps"></a>Další kroky

Můžeme pokračovat k dalšímu scénáři, ve kterém provedeme migraci místních virtuálních počítačů a databáze do Azure metodou [lift and shift](migrate-scenarios-lift-and-shift.md).
