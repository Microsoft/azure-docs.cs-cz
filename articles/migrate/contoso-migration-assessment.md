---
title: Posouzení vhodnosti místních úloh pro Contoso migraci do Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso vyhodnocuje jeho místních počítačů pro migraci do Azure pomocí Azure Migrate a Data Migration Assistant.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: b9ef650b772a160f7ddf8932f646f8081cb9e551
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407125"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migrace Contoso: posouzení vhodnosti místních úloh pro migraci do Azure

V tomto článku Contoso vyhodnocuje jeho místní SmartHotel360 aplikaci pro migraci do Azure.

Tento článek je součástí série, která popisuje, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje informatio na pozadí a scénáře podrobné nasazení, které ukazují, jak nastavit infrastrukturu migrace vyhodnotit vhodnost migrace místních prostředků a spouštět různé druhy migrace. Scénáře jejich složitost v. V čase se přidají do série článků.

Článek | Podrobnosti | Status
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Přehled řady článku, strategie migrace společnosti Contoso a ukázkové aplikace, které se používají v řadě. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články v této sérii se používá stejnou infrastrukturu. | K dispozici.
Článek 3: Posouzení místních prostředků pro migraci do Azure | Contoso spuštění posouzení své místní aplikace SmartHotel360 běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí služby Azure Migrate a databáze aplikace SQL Server pomocí Data Migration Assistant. | V tomto článku
[Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso běží lift and shift migrace do Azure pro své místní aplikace SmartHotel360. Migrace aplikace front-endu pomocí služby Azure Site Recovery. Migrace databáze aplikace do Azure SQL Database Managed Instance pomocí Azure Database Migration Service. | K dispozici.
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Contoso migruje jeho aplikace SmartHotel360 virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikace SmartHotel360 pomocí služby Site Recovery k migraci virtuálních počítačů aplikace a Database Migration Service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Contoso se dokončí migrace lift and shift jeho Linux osTicket aplikace na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje jeho Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Site Recovery. Migrace databáze aplikace ke službě Azure Database for MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace do webové aplikace Azure a Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migraci své aplikace SmartHotel360 do webové aplikace Azure a migraci databáze aplikace do instance serveru SQL Azure pomocí Pomocníka s migrací databáze. | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci v Azure webové aplikace a služby Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje osTicket své Linuxové aplikace do webové aplikace Azure v několika oblastech Azure pomocí Azure Traffic Manageru, integrovaná se službou GitHub pro průběžné doručování. Contoso migraci databáze aplikace do Azure Database for MySQL – instance. | K dispozici.
[Článku 11: Refaktorujte Team Foundation Server na službách Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migrovat své místní nasazení serveru Team Foundation Server ke službám Azure DevOps v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jeho SmartHotel aplikace do Azure. Potom rearchitects webové vrstvy aplikace jako kontejner Windows se spuštěnou v Azure Service Fabric a databázi Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB. | K dispozici.
[Článek 14: Škálování migrace do Azure](contoso-migration-scale.md) | Po vyzkoušení kombinace migrace, Contoso připraví škálovat na plné migraci do Azure. | K dispozici.


## <a name="overview"></a>Přehled

Jak Contoso považuje migraci do Azure, společnost chce provést interní technické a finanční hodnocení, chcete-li zjistit, jestli jsou jejich místní úlohy vhodné k migraci do cloudu. Konkrétně se chce tým Contoso posoudit kompatibilitu počítačů a databází pro migraci. Chce odhadnout kapacitu a náklady na provoz od Contosa prostředky v Azure.

Abyste mohli začít, abyste lépe poznali technologií společnosti Contoso vyhodnocuje dvě z jeho místní aplikace, které jsou shrnuty v následující tabulce. Společnost vyhodnocuje pro scénáře migrace této metody opětovného hostování a Refaktorujte aplikací pro migraci. Další informace o opětovném hostování a refaktoring v [Přehled migrace Contoso](contoso-migration-overview.md).

App name (Název aplikace) | Platforma | Úrovně aplikace | Podrobnosti
--- | --- | --- | ---
SmartHotel360<br/><br/> (spravuje Contoso cestovní požadavky) | Běží na Windows s databází SQL serveru | Dvouvrstvém aplikace. Front-endový Web ASP.NET běží na jeden virtuální počítač (**WEBVM**) a SQL Server běží na jiný virtuální počítač (**SQLVM**). | Virtuální počítače VMware, které běží na hostiteli ESXi spravované přes vCenter Server.<br/><br/> Můžete stáhnout ukázkovou aplikaci z [Githubu](https://github.com/Microsoft/SmartHotel360).
osTicket<br/><br/> (Contoso helpdesku app service) | Spustí se v systému Linux/Apache s PHP s MySQL (LAMP) | Dvouvrstvém aplikace. Front-endu webu v PHP běží na jeden virtuální počítač (**OSTICKETWEB**) a databáze MySQL se spustí na jiný virtuální počítač (**OSTICKETMYSQL**). | Aplikace používá ke sledování problémů pro interní zaměstnance a externí zákazníky zákaznických služeb aplikací.<br/><br/> Můžete stáhnout ukázku z [Githubu](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Aktuální architektura

Tento diagram znázorňuje aktuální místní infrastrukturou společnosti Contoso:

![Aktuální architektura Contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso má jeden hlavní datové centrum. Datového centra se nachází v města New York východní USA.
- Contoso má tři další místní větve ve Spojených státech amerických.
- Hlavní datové centrum je připojený k Internetu pomocí fiber připojení k síti Ethernet Metro (500 MB/s).
- Každá větev je místně připojen k Internetu pomocí podnikové třídy připojení s IPsec VPN tunely zpět do hlavního datového centra. Instalační program umožňuje společnosti Contoso celá síť trvale připojený k Internetu a optimalizuje připojení k Internetu.
- Hlavní datové centrum je plně virtualizovaný s VMware. Contoso má dva hostitele ESXi 6.5 virtualizace, které jsou spravované přes vCenter Server verze 6.5.
- Contoso používá službu Active Directory pro správu identit. Contoso používá servery DNS v interní síti.
- Běží na virtuálních počítačích VMware řadiče domény v datovém centru. Řadiče domény v místní větve spustit na fyzických serverech.

## <a name="business-drivers"></a>Obchodní faktory

Společnosti Contoso vedení IT úzké spolupráci s obchodními partnery společnosti pochopit, co obchodní chce, aby k dosažení této migraci:

- **Adresa obchodní růst**: Contoso roste. V důsledku toho přetížení zvýšil na místních systémech a infrastrukturu vaší společnosti.
- **Zvýšení efektivity**: Contoso je potřeba odebrat nepotřebné postupy a zjednodušte procesy pro její vývojáře a uživatele. Obchodní potřeby IT být rychlé a k není produkovaného odpadu čas a peníze, takže společnost doručovat rychleji na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změny, ke kterým dochází v marketplace pro společnost v globální ekonomiku proběhla úspěšně. IT ve společnosti Contoso nesmí získat způsobem, nebo se Staňte obchodní blokování.
- **Škálování**: s růstem společnosti úspěšně Contoso IT musí poskytnout systémy, které můžou růst stejným tempem.

## <a name="assessment-goals"></a>Posouzení cíle

Tým cloudových Contoso zjistila cíle pro jeho posouzení migrace:

- Po dokončení migrace aplikací v Azure by měl mít stejné možnosti výkonu, které aplikace mají dnes v prostředí VMWare v místním společnosti Contoso. Přesun do cloudu, neznamená, že výkon aplikace je méně kritické.
- Contoso potřebuje pochopit kompatibilitu své aplikace a databáze s požadavky na Azure. Contoso je také potřeba pochopit jeho možnosti hostování v Azure.
- Správa databází společnosti Contoso byste měli minimalizovat po přesunu aplikace do cloudu.  
- Contoso chce znát pouze jeho možnosti migrace, ale také náklady spojené s vaší stávající infrastrukturou po přesunu do cloudu.

## <a name="assessment-tools"></a>Nástroje pro vyhodnocení

Společnost Contoso využívá Microsoft nástroje pro posouzení migrace. Nástroje bylo v souladu s cíli vaší společnosti a by měla poskytnout všechny informace, které potřebuje Contoso.

Technologie | Popis | Náklady
--- | --- | ---
[Pomocník s migrací dat](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Společnost Contoso využívá k vyhodnocení a zjištění problémů s kompatibilitou, které by mohly ovlivnit funkčnost databází v Azure Data Migration Assistant. Data Migration Assistant posuzuje paritu funkcí mezi SQL zdroje a cíle. Doporučuje vylepšení výkonu a spolehlivosti. | Data Migration Assistant je zdarma ke stažení nástroje.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Společnost Contoso využívá služba Azure Migrate k vyhodnocení svých virtuálních počítačů VMware. Azure Migrate posuzuje vhodnost k migraci počítačů. Poskytuje odhady velikostí a nákladů pro spuštění v Azure.  | Od května 2018 Azure Migrate je bezplatná služba.
[Mapa služeb](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate využívá mapu služeb k zobrazení závislostí mezi počítači, které společnost chce migrovat. | Mapa služeb je součástí Azure Log Analytics. V současné době Contoso, můžete použít Service Map po dobu 180 dnů bez poplatků.

V tomto scénáři Contoso stáhne a spustí Data Migration Assistant k posouzení místní databáze SQL serveru pro jeho cestovní aplikaci. Contoso používá Azure Migrate se mapování závislostí k vyhodnocení aplikací virtuálních počítačů před migrací do Azure.

## <a name="assessment-architecture"></a>Architektura posouzení

![Architektura posouzení migrace](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso je fiktivní název, který představuje typické podnikové organizace.
- Contoso má místní datacentrum (**contoso-datacenter**) a řadiče domény v místním (**CONTOSODC1**, **CONTOSODC2**).
- Virtuální počítače VMware jsou umístěny na VMware ESXi hostitelích používajících verze 6.5 (**contosohost1**, **contosohost2**).
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**běžící na virtuálním počítači).
- Cestovní aplikace SmartHotel360 má tyto vlastnosti:
    - Aplikace je rozvrstvená na dva virtuální počítače VMware (**WEBVM** a **SQLVM**).
    - Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com**.
    - Virtuální počítače se systémem Windows Server 2008 R2 Datacenter s aktualizací SP1.
- Správu prostředí VMware zajišťuje vCenter Server (**vcenter.contoso.com**) spuštěný na virtuálním počítači.
- Aplikace helpdesku osTicket service:
    - Aplikace je rozvrstvená na dva virtuální počítače (**OSTICKETWEB** a **OSTICKETMYSQL**).
    - Virtuální počítače se systémem Ubuntu Linux Server 16.04-LTS.
    - **OSTICKETWEB** běží Apache 2 a PHP 7.0.
    - **OSTICKETMYSQL** MySQL 5.7.22 běží.

## <a name="prerequisites"></a>Požadavky

Contoso a ostatní uživatelé musí splňovat následující požadavky pro assessment:

- Komunikační oprávnění vlastníka nebo přispěvatele pro předplatné Azure nebo pro skupinu prostředků v předplatném Azure.
- Místní vCenter instance serveru spuštěná verze 6.5, 6.0 nebo 5.5.
- Účet jen pro čtení ve vCenter serveru nebo oprávnění k jeho vytvoření.
- Oprávnění k vytvoření virtuálního počítače na instanci serveru vCenter pomocí šablony .ova.
- Minimálně jednoho hostitele ESXi verze 5.0 nebo novější.
- Alespoň dva místní virtuální počítače VMware, na jednom z nichž běží databáze SQL Serveru.
- Oprávnění k instalaci agentů Azure Migrate na každém virtuálním počítači.
- Virtuální počítače by měly mít přímé připojení k internetu.  
        – Internetový přístup můžete omezit na [požadované adresy URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).  
        – Pokud je vaše virtuální počítače nemají připojení k Internetu, Azure [Log Analytics Gateway](../log-analytics/log-analytics-oms-gateway.md) musí být nainstalované a agenta přenášená přes něj.
- Plně kvalifikovaný název domény virtuálního počítače, na kterém je spuštěná instance SQL Serveru, pro účely posouzení databáze.
- Brány Windows Firewall spuštěnou na virtuálním počítači SQL serveru by měla umožňovat externí připojení na portu TCP 1433 (výchozí). Toto nastavení umožní Data Migration Assistant pro připojení.

## <a name="assessment-overview"></a>Přehled posouzení

Tady je způsob, jakým Contoso provádí hodnocení:

> [!div class="checklist"]
> * **Krok 1: Stáhněte a nainstalujte Pomocníka s migrací dat**: Contoso připraví Data Migration Assistant pro posouzení místní databáze SQL serveru.
> * **Krok 2: Posouzení databáze s využitím Pomocníka s migrací dat**: Contoso běží a analyzuje posouzení databáze.
> * **Krok 3: Příprava na posouzení virtuálních počítačů pomocí Azure Migrate**: Contoso nastaví místních účtů a upraví nastavení VMware.
> * **Krok 4: Vyhledání místních virtuálních počítačů pomocí Azure Migrate**: Contoso vytvoří Azure Migrate collector virtuálního počítače. Potom Contoso spuštění kolektoru pro vyhledání virtuálních počítačů pro posouzení.
> * **Krok 5: Příprava na analýzu závislostí s využitím Azure Migrate**: nainstaluje Contoso agentů Azure Migrate na virtuálních počítačích, takže společnosti můžete zobrazit mapování závislostí mezi virtuálními počítači.
> * **Krok 6: Posouzení virtuálních počítačů pomocí Azure Migrate**: Contoso zkontroluje závislostí skupiny virtuálních počítačů a spuštění posouzení. Při hodnocení je připraven, analyzuje Contoso posouzení v rámci přípravy na migraci.

## <a name="step-1-download-and-install-data-migration-assistant"></a>Krok 1: Stáhněte a nainstalujte Pomocníka s migrací dat

1. Stáhne Data Migration Assistant ze společnosti Contoso [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Data Migration Assistant můžete nainstalovat na jakýkoli počítač, který se může připojit k instanci systému SQL Server. Contoso není nutné ho spustit na počítači s SQL serverem.
    - Data Migration Assistant neměli spouštět na hostitelském počítači SQL serveru.
2. Contoso se spouští na stažený instalační soubor (DownloadMigrationAssistant.msi) a spusťte tak instalaci.
3. Na **Dokončit** stránce, vybere Contoso **spusťte Microsoft Data Migration Assistant** před dokončením průvodce.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel360"></a>Krok 2: Spuštění a analýza posouzení databáze pro SmartHotel360

Contoso teď můžete spustit posouzení, které analyzuje jeho místní databáze systému SQL Server pro aplikace SmartHotel360.

1. V Data Migration Assistant Contoso vybere **nový** > **posouzení**a poté přiřadí název projektu posouzení.
2. Pro **serveru typ zdroje**, vybere Contoso **systému SQL Server na virtuálních počítačích Azure**.

    ![Data Migration Assistant – výběr zdroje](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      V současné době Data Migration Assistant nepodporuje posouzení pro migraci do Azure SQL Database Managed Instance. Jako alternativní řešení Contoso používá SQL Server na Virtuálním počítači Azure jako předpokládaný cíl pro posouzení.

3. V **vyberte cílovou verzi**, Contoso vybere jako cílovou verzi SQL serveru 2017. Contoso je potřeba vybrat tuto verzi, protože je verze, která používá SQL Database Managed Instance.
4. Contoso vybere sestavy, které pomáhají zjistit informace o kompatibilitě a nové funkce:
    - **Problémy s kompatibilitou** Všimněte si změny, které můžou narušit migraci nebo které vyžadují menší úpravu před migrací. Tato sestava zachová Contoso informováni o všech funkcích aktuálně používán, které jsou zastaralé. Problémy jsou uspořádané podle úrovně kompatibility.
    - **Nová doporučení funkcí** poznámky nové funkce v cílové platformě SQL serveru, který lze použít pro databázi po migraci. Nová doporučení funkcí jsou uspořádané pod záhlavím **výkonu**, **zabezpečení**, a **úložiště**.

    ![Data Migration Assistant – problémy s kompatibilitou a nové funkce](./media/contoso-migration-assessment/dma-assessment-2.png)

2. V **připojit k serveru**, Contoso zadá název virtuálního počítače, na kterém běží databáze a přihlašovací údaje pro přístup k ní. Vybere contoso **důvěřovat certifikátu serveru** k Ujistěte se, že virtuální počítač má přístup k SQL serveru. Potom vybere Contoso **připojit**.

    ![Data Migration Assistant – připojení k serveru](./media/contoso-migration-assessment/dma-assessment-3.png)

3. V **přidat zdroj**, Contoso přidá databáze, chce k vyhodnocení a poté vybere **Další** spusťte posouzení.
4. Posouzení se vytvoří.

    ![Data Migration Assistant – vytvořit posouzení](./media/contoso-migration-assessment/dma-assessment-4.png)

5. V **výsledků kontroly**, Contoso zobrazení výsledků posouzení.

### <a name="analyze-the-database-assessment"></a>Analýza posouzení databáze

Výsledky se zobrazí, jakmile jsou k dispozici. Pokud Contoso řeší problémy, musíte vybrat **restartovat posouzení** znovu spusťte posouzení.

1. V **problémy s kompatibilitou** sestavy Contoso vyhledá všechny problémy na jednotlivých úrovních kompatibility. Mapování úrovní kompatibility na verze SQL Serveru je následující:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Data Migration Assistant – sestava problémy s kompatibilitou](./media/contoso-migration-assessment/dma-assessment-5.png)

2. V **doporučení funkcí** sestavy Contoso zobrazení výkonu, zabezpečení a úložiště funkcí, které posouzení doporučuje po migraci. Doporučují se širokou škálu funkcí, včetně OLTP v paměti, indexy columnstore, Stretch Database, Always Encrypted, dynamického maskování dat a transparentní šifrování dat.

    ![Data Migration Assistant – sestava doporučení funkcí](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > By měl contoso [povolit transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) pro všechny databáze SQL serveru. To je ještě důležitější, když je databáze v cloudu, než když je hostovaný místně. Transparentní šifrování dat musí být povolené pouze po migraci. Pokud je již povolen transparentní šifrování dat, Contoso musí přesunout certifikátem nebo asymentrickým klíčem k hlavní databázi z cílového serveru. Zjistěte, jak [přesunout databázi transparentní šifrování chráněných dat do jiné instance systému SQL Server](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Společnost Contoso může exportovat posouzení ve formátu JSON nebo CSV.

> [!NOTE]
> Ve velkém měřítku vyhodnocení:
> - Spustit několik posouzení současně a zobrazit stav posouzení, která na **všechna posouzení** stránky.
> - Konsolidovat posouzení do [databáze systému SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
> - Konsolidovat posouzení do [sestavy Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).

## <a name="step-3-prepare-for-vm-assessment-by-using-azure-migrate"></a>Krok 3: Příprava na posouzení virtuálních počítačů pomocí Azure Migrate

Contoso je potřeba vytvořte účet VMware s Azure Migrate můžete použít k automatickému zjišťování virtuálních počítačů pro posouzení, ověřte oprávnění k vytvoření virtuálního počítače, poznamenejte si porty, které je potřeba otevřít a nastavte úroveň nastavení statistiky.

### <a name="set-up-a-vmware-account"></a>Nastavení účtu VMware

Zjišťování virtuálních počítačů vyžaduje účet jen pro čtení v systému vCenter Server, který má následující vlastnosti:

- **Typ uživatele**: alespoň uživatel jen pro čtení.
- **Oprávnění**: objekt datového centra, vyberte **rozšířit na podřízený objekt** zaškrtávací políčko. Pro **Role**vyberte **jen pro čtení**.
- **Podrobnosti o**: má uživatel přiřazené na úrovni datacentra s přístupem ke všem objektům v datacentru.
- Chcete-li omezit přístup, přiřaďte **bez přístupu** role s **rozšířit na podřízený** objekt podřízeným objektům (hostitelé vSphere, úložiště, virtuální počítače a sítě).

### <a name="verify-permissions-to-create-a-vm"></a>Ověření oprávnění k vytvoření virtuálního počítače

Contoso ověřuje, že má oprávnění k vytvoření virtuálního počítače pomocí importu souboru ve formátu .ova. Zjistěte, jak [vytvoření a přiřazení role se stejnými oprávněními](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Ověření portů

Posouzení Contoso využívá mapování závislostí. Mapování závislostí vyžaduje agenta nainstalovat na virtuální počítače, které bude použit k vyhodnocení. Agent musí být schopen připojit se k Azure z portu TCP 443 na každém virtuálním počítači. Další informace o [požadavky na připojení](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid).

### <a name="set-statistics-settings"></a>Nastavení statistiky

Před zahájením Contoso nasazení, nastavte nastavení statistiky systému vCenter Server na úroveň 3. 

> [!NOTE]
> - Po nastavení úrovně Contoso musíte počkat aspoň jeden den před spuštěním posouzení. V opačném případě posouzení nemusí fungovat podle očekávání.
> - Pokud je úroveň vyšší než 3, posouzení funguje, ale:
>    - Nejsou shromažďována data výkonu u disků a sítí.
>    - V případě úložiště služba Azure Migrate doporučí v Azure standardní disk stejné velikosti jako místní disk.
>    - Práce se sítí, pro každý místní síťový adaptér, doporučuje se síťový adaptér v Azure.
>    - Za výpočetní výkon Azure Migrate zjistí počet jader virtuálního počítače a velikosti paměti a doporučí virtuální počítač Azure se stejnou konfigurací. Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.
> - Další informace o určení velikosti pomocí úroveň 3, naleznete v tématu [změny velikosti](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

K nastavení úrovně:

1. Ve webovém klientovi vSphere Contoso se otevře na instanci serveru vCenter.
2. Vybere contoso **spravovat** > **nastavení** > **Obecné** > **upravit**.
3. V **statistiky**, Contoso nastaví Statistika nastavení úrovně **Level 3**.

    ![statistiky systému vCenter Server úroveň](./media/contoso-migration-assessment/vcenter-statistics-level.png)

## <a name="step-4-discover-vms"></a>Krok 4: Vyhledání virtuálních počítačů

Ke zjištění virtuálních počítačů společnosti Contoso vytvoří projekt Azure Migrate. Contoso stáhne a nastaví virtuální počítač kolektoru. Potom Contoso spuštění kolektoru pro vyhledání jeho místních virtuálních počítačů.

### <a name="create-a-project"></a>Vytvoření projektu

1. V [webu Azure portal](https://portal.azure.com), hledá Contoso **Azure Migrate**. Contoso vytvoří projekt.
2. Určuje název projektu Contoso (**ContosoMigration**) a předplatné Azure. Vytvoří novou skupinu prostředků Azure (**ContosoFailoverRG**). 
    > [!NOTE]
    > - Projekt Azure Migrate můžete vytvořit jenom v západní USA – střed nebo oblasti USA – východ.
    > - Migraci můžete naplánovat pro jakékoli cílové umístění.
    > - Umístění projektu slouží pouze k uložení metadat, která jsou shromážděna z místních virtuálních počítačů.

    ![Azure Migrate – vytvoření projektu migrace](./media/contoso-migration-assessment/project-1.png)

### <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Azure Migrate vytvoří místní virtuální počítač označované jako *zařízení kolektoru*. Virtuální počítač vyhledá místní virtuální počítače VMware a odešle metadata o virtuální počítače do služby Azure Migrate. Nastavení zařízení kolektoru, Contoso stáhne šablony OVA a importuje ho do místní instanci serveru vCenter vytvoření virtuálního počítače.

1. V projektu Azure Migrate Contoso vybere **Začínáme** > **zjistit a posoudit** > **zjistit počítače**. Contoso stáhne soubor OVA šablony.
2. Contoso zkopíruje ID a klíč projektu. Projekt a ID jsou požadovány pro konfiguraci kolektoru.

    ![Azure Migrate - stáhnout soubor OVA](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Před nasazením virtuálního počítače, Contoso kontroluje, zda je soubor OVA zabezpečení:

1. Na počítači, na kterém se soubor stáhne Contoso se otevře okno příkazového řádku správce.
2. Contoso spustí následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    
    **Příklad** 
    
    ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Vygenerovaná hodnota hash by měla odpovídat následujícímu nastavení (verze 1.0.9.14):

    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
    SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
    SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

### <a name="create-the-collector-appliance"></a>Vytvoření zařízení kolektoru

Contoso teď můžete importovat stažený soubor na instanci serveru vCenter a zřízení zařízení kolektoru virtuálního počítače:

1. V konzoli vSphere Client se vybere Contoso **souboru** > **Deploy OVF Template**.

    ![Webový klient - nasazení OVF template vSphere](./media/contoso-migration-assessment/vcenter-wizard.png)

2. V nasazení OVF Průvodce šablonou, vybere Contoso **zdroj**a poté určí umístění souboru pro soubory OVA.
3. V **název a umístění**, Contoso Určuje zobrazovaný název pro virtuální počítač kolektoru. Potom vybere umístění v inventáři ve kterém chcete hostovat virtuální počítač. Contoso také určuje hostitele nebo cluster, ve kterém se spustí zařízení kolektoru.
4. V **úložiště**, Contoso Určuje umístění úložiště. V **formát disku**, Contoso vybere, jak chce zřídit úložiště.
5. V **mapování sítě**, Contoso určuje sítě, ve které chcete připojit virtuální počítač kolektoru. Síť vyžaduje připojení k Internetu na odesílat metadata do Azure.
6. Contoso zkontroluje nastavení a potom vybere **Power on po nasazení** > **Dokončit**. Při vytvoření zařízení, zobrazí se zpráva, která potvrzuje úspěšné dokončení.

### <a name="run-the-collector-to-discover-vms"></a>Spuštění kolektoru pro vyhledání virtuálních počítačů

Nyní Contoso spuštění kolektoru pro vyhledání virtuálních počítačů. V současné době kolektoru aktuálně podporuje pouze **Angličtina (Spojené státy)** jako jazyk operačního systému a jazyk rozhraní kolektoru.

1. V konzoli vSphere Client se vybere Contoso **otevřete konzoly**. Contoso Určuje jazyk, časové pásmo a heslo předvolby pro virtuální počítač kolektoru.
2. Na ploše, vybere Contoso **spustit kolektor** zástupce.

    ![Konzola klienta vSphere - zástupce Kolektoru](./media/contoso-migration-assessment/collector-shortcut.png)

3. V Azure Migrate Collector Contoso vybere **nastavit požadavky**. Contoso přijímá licenční podmínky a načte informace třetích stran.
4. Kolektor zkontroluje, že virtuální počítač má přístup k Internetu, je čas synchronizovaný a zda je spuštěna služba kolektoru. (Ve výchozím nastavení na virtuálním počítači je nainstalována služba kolektoru.) Contoso se nainstaluje taky VMware PowerCLI.

    > [!NOTE]
    > Předpokládá se, že má virtuální počítač přímý přístup k Internetu bez použití proxy serveru.

    ![Azure Migrate Collector - Ověřte požadavky](./media/contoso-migration-assessment/collector-verify-prereqs.png)

5. V **zadejte podrobnosti vCenter serveru**Contoso zadá název (FQDN) nebo IP adresa instance serveru vCenter a přihlašovacích údajů jen pro čtení používá pro zjišťování.
6. Contoso vybere rozsah zjišťování virtuálních počítačů. Kolektor může vyhledat pouze virtuální počítače, které jsou v rámci zadaného oboru. Rozsah můžete nastavit na konkrétní složku, datové centrum nebo cluster. Rozsah nesmí obsahovat více než 1 500 virtuálních počítačů.

    ![Zadejte podrobnosti vCenter Serveru](./media/contoso-migration-assessment/collector-connect-vcenter.png)

7. V **zadejte projekt migrace**, zadá Contoso ID projektu Azure Migrate a klíč, který jste zkopírovali ze na portálu. K získání ID projektu a klíče, Contoso přejít do projektu **přehled** stránky > **zjistit počítače**.  

    ![Zadejte projekt migrace](./media/contoso-migration-assessment/collector-connect-azure.png)

8. V **zobrazit průběh shromažďování**, Contoso můžete sledovat zjišťování a zkontrolujte, jestli metadata shromážděná z virtuálních počítačů je v oboru. Kolektor vás informuje o tom, jak dlouho bude zjišťování přibližně trvat.

    ![Zobrazit průběh shromažďování](./media/contoso-migration-assessment/collector-collection-process.png)

### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení shromažďování Contoso zkontroluje, že se virtuální počítače na portálu:

1. V projektu Azure Migrate Contoso vybere **spravovat** > **počítače**. Contoso ověří, že jsou zobrazeny, které si přeje zjistit virtuální počítače.

    ![Azure Migrate - zjištěné počítače](./media/contoso-migration-assessment/discovery-complete.png)

2. V současné době nemají počítačích nainstalovaní agenti Azure Migrate. Contoso musíte nainstalovat agenty Chcete-li zobrazit závislosti.

    ![Azure Migrate – je požadována instalace agenta](./media/contoso-migration-assessment/machines-no-agent.png)

## <a name="step-5-prepare-for-dependency-analysis"></a>Krok 5: Příprava na analýzu závislostí

Chcete-li zobrazit závislosti mezi virtuálními počítači, které chce k vyhodnocení, Contoso stáhne a nainstaluje agenti na virtuálních počítačích app. Nainstaluje contoso agenty na všech virtuálních počítačích pro aplikace pro Windows i Linux.

### <a name="take-a-snapshot"></a>Pořízení snímku

Aby kopie virtuálních počítačů před jejich úpravou Contoso pořídí snímek předtím, než jsou nainstalovaní agenti.

![Snímek počítače](./media/contoso-migration-assessment/snapshot-vm.png)

### <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače

1. V **počítače**, Contoso vybere tento počítač. V **závislosti** vybere sloupce, Contoso **vyžaduje instalaci**.
2. V **zjistit počítače** podokně Contoso:
    - Soubory ke stažení Agenta Microsoft Monitoring Agent (MMA) a agenta závislostí pro každý virtuální počítač Windows.
    - Stáhnout agenta MMA a agenta závislostí pro každý virtuální počítač s Linuxem.
3. Contoso zkopíruje ID pracovního prostoru a klíč. Contoso potřebuje ID pracovního prostoru a klíč při instalaci agenta MMA.

    ![Stažení agenta](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Nainstalovat agenty na virtuálních počítačích s Windows

Contoso spustí instalaci na každém virtuálním počítači.

#### <a name="install-the-mma-on-windows-vms"></a>Instalace agenta MMA na virtuálních počítačích s Windows

1. Contoso dvakrát klikne na staženého agenta.
2. V **cílovou složku**, Contoso udržuje výchozí instalační složku a potom vybere **Další**.
3. V **možnosti instalace agenta**, vybere Contoso **připojit agenta k Azure Log Analytics** > **Další**.

    ![Instalační program agenta – možnosti instalace agenta sledování společnosti Microsoft](./media/contoso-migration-assessment/mma-install.png)

4. V **Azure Log Analytics**, Contoso vloží ID pracovního prostoru a klíč, který se zkopíruje z portálu.

    ![Microsoft Monitoring Agent – instalační program Azure Log Analytics](./media/contoso-migration-assessment/mma-install2.png)

5. V **připraveno k instalaci**, nainstaluje Contoso agenta MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Instalace agenta závislostí na virtuálních počítačích s Windows

1. Contoso dvakrát klikne na staženého agenta závislostí.
2. Contoso přijímá licenční podmínky a čeká na dokončení instalace.

    ![Instalace agenta závislostí – instalace](./media/contoso-migration-assessment/dependency-agent.png)

### <a name="install-the-agents-on-linux-vms"></a>Nainstalovat agenty na virtuální počítače s Linuxem

Contoso spustí instalaci na každém virtuálním počítači.

#### <a name="install-the-mma-on-linux-vms"></a>Instalace agenta MMA na virtuální počítače s Linuxem

1. Knihovna Python ctypes na každý virtuální počítač nainstaluje contoso pomocí následujícího příkazu:

    `sudo apt-get install python-ctypeslib`
2. Společnosti Contoso musí spustit příkaz k instalaci agenta MMA jako uživatel root. Contoso se kořenový spustí následující příkaz a potom zadá kořenové heslo:

    `sudo -i`
3. Nainstaluje agenta MMA Contoso:
    - Společnosti Contoso přejde do ID pracovního prostoru a klíč v příkazu.
    - Příkazy jsou pro 64bitovou verzi.
    - ID pracovního prostoru a primární klíč jsou umístěny v pracovním prostoru Log Analytics na portálu Azure portal. Vyberte **nastavení**a pak vyberte **připojené zdroje** kartu.
    - Spusťte následující příkazy ke stažení agenta Log Analytics, ověření kontrolního součtu a nainstalovat a začlenit agenta:

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```

#### <a name="install-the-dependency-agent-on-linux-vms"></a>Instalace agenta závislostí na virtuální počítače s Linuxem

Po instalaci agenta MMA, nainstaluje Contoso agenta závislostí na virtuální počítače s Linuxem:

1. Agent závislostí je nainstalované na počítače s Linuxem pomocí Linux64.bin InstallDependencyAgent, skript prostředí, který má samorozbalovací binární soubor. Oprávnění k souboru, samotné ke spouštění contoso spuštění souboru pomocí TV, nebo ho přidá.

2. Jako uživatel root nainstaluje contoso agenta závislosti pro Linux:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```

## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Krok 6: Spuštění a analýza posouzení virtuálních počítačů

Contoso teď můžete ověřte závislosti počítačů a vytvořit skupinu. Pak spustí posouzení pro skupinu.

### <a name="verify-dependencies-and-create-a-group"></a>Ověřte závislosti a vytvořte skupinu

1. Chcete-li zjistit, které počítače k analýze, vybere Contoso **zobrazit závislosti**.

    ![Azure Migrate – zobrazení závislostí počítačů](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Pro virtuální počítač SQLVM se na mapě závislostí zobrazí následující podrobnosti:

    - Skupiny procesů nebo procesy, které mají aktivní síťové připojení, spuštěná na virtuálním počítači SQLVM během zadaného časového období (hodinu, ve výchozím nastavení).
    - Příchozí (klient) připojení přes protokol TCP ke všem závislým počítačům a odchozí (server) připojení přes protokol TCP ze všech závislých počítačů.
    - Závislé počítače s nainstalovanými agenty Azure Migrate se zobrazí v samostatných polích.
    - Počítače, které neobsahují nainstalovanými agenty zobrazit portu a informace o IP adresách.

3. Pro počítače s nainstalovaným agentem (WEBVM) vybere Contoso pole počítače zobrazit další informace. Informace zahrnují plně kvalifikovaný název domény, operačního systému a adresa MAC.

    ![Azure Migrate – zobrazení skupinových závislostí](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Contoso vybere virtuálních počítačů určených k přidání do skupiny (SQLVM a WEBVM). Contoso používá kombinaci kláves Ctrl + kliknutí vybrat několik virtuálních počítačů.
5. Vybere contoso **vytvořit skupinu**a potom zadá název (**smarthotelapp**).

    > [!NOTE]
    > Pokud chcete zobrazit podrobnější závislosti, můžete rozšířit časový rozsah. Můžete vybrat konkrétní dobu nebo vyberte počáteční a koncové datum.

### <a name="run-an-assessment"></a>Spuštění posouzení

1. V **skupiny**, Contoso otevře skupinu (**smarthotelapp**) a potom vybere **vytvořit posouzení**.

    ![Azure Migrate – vytvoření posouzení](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Chcete-li zobrazit posouzení, vybere Contoso **spravovat** > **posouzení**.

Contoso používá výchozí nastavení posouzení, ale můžete [nastavení](how-to-modify-assessment.md).

### <a name="analyze-the-vm-assessment"></a>Analýza posouzení virtuálních počítačů

Posouzení služby Azure Migrate obsahuje informace o kompatibilitě místních s Azure, navrhované správné velikosti pro virtuální počítač Azure a odhadovaných měsíčních nákladech na Azure.

![Azure Migrate – sestava posouzení](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

![Azure Migrate – zobrazení posouzení](./media/contoso-migration-assessment/assessment-display.png)

Posouzení má hodnocení spolehlivosti od 1 do 5 hvězdiček (1 hvězdička je nejnižší a 5 hvězdiček je nejvyšší).
- Hodnocení spolehlivosti je přiřazen k posouzení na základě dostupnosti datových bodů, které jsou potřeba pro výpočet posouzení.
- Toto hodnocení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti je užitečné, když provádíte *určení velikosti na základě výkonu*. Azure Migrate nemusí mít dostatek datových bodů pro určení velikosti na základě využití. Pro *jako místní* změny velikosti, je hodnocení spolehlivosti vždy 5 hvězdiček, protože Azure Migrate má k dispozici všechny datové body je potřeba upravit velikost virtuálního počítače.
- Tady je poskytnuté hodnocení spolehlivosti posouzení v závislosti na procentu dostupných datových bodů:

   Dostupnost datových bodů | Hodnocení spolehlivosti
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček

#### <a name="verify-azure-readiness"></a>Ověření připravenosti pro Azure

![Azure Migrate – posouzení připravenosti](./media/contoso-migration-assessment/azure-readiness.png)  

Sestavě posouzení se zobrazí informace, což je shrnuto v tabulce. Chcete-li zobrazit určení velikosti na základě výkonu potřebuje Azure Migrate následující informace. Pokud tyto informace není možné shromáždit, posouzení velikostí nemusí být přesné.

- Data o využití procesoru a paměti.
- Informace o vstupně-výstupních operacích čtení a zápisu za sekundu a propustnosti pro všechny disky připojené k virtuálnímu počítači.
- Informace o síťových vstupech a výstupech pro všechny síťové adaptéry připojené k virtuálnímu počítači.

Nastavení | Označení | Podrobnosti
--- | --- | ---
**Připravenost virtuálního počítače pro Azure** | Určuje, jestli je virtuální počítač připravený k migraci. | Možné stavy:</br><br/>– Připraveno pro Azure<br/><br/>– Připraveno s podmínkami <br/><br/>– Nepřipraveno pro Azure<br/><br/>– Připravenost neznámá<br/><br/> Pokud virtuální počítač není připravený, Azure Migrate ukazuje některé kroky k nápravě.
**Velikost virtuálního počítače Azure** | Pro připravené virtuální počítače Azure Migrate nabízí doporučená velikost virtuálního počítače Azure. | Doporučení velikosti závisí na vlastnostech posouzení:<br/><br/>– Pokud jste použili určení velikosti na základě výkonu, při určování velikosti se zohlední historie výkonu virtuálních počítačů.<br/><br/>– Pokud jste použili *jako místní*, určení velikosti bude založené na velikosti virtuálního počítače s místními a data o využití se nepoužijí.
**Navrhovaný nástroj** | Protože Azure počítačích běží agenti, Azure Migrate prozkoumá procesy, které jsou spuštěny na počítači. Určuje, jestli tento počítač hostuje databázi.
**Informace o virtuálním počítači** | Tato sestava ukazuje nastavení pro místní virtuální počítač, včetně operačního systému, spouštěcí typ a informace o disku a úložiště.

#### <a name="review-monthly-cost-estimates"></a>Kontrola odhadů měsíčních nákladů

Toto zobrazení ukazuje celkové náklady výpočetní výkon a úložiště spuštěných virtuálních počítačů v Azure. Také ukazuje podrobnosti o každém počítači.

![Azure Migrate – náklady na Azure](./media/contoso-migration-assessment/azure-costs.png)

- Odhadované náklady se počítají pomocí doporučené velikosti pro počítač.
- Odhadované měsíční náklady na výpočetní kapacitu a úložiště jsou agregované pro všechny virtuální počítače dané skupiny.

## <a name="clean-up-after-assessment"></a>Vyčištění po posouzení

- Po dokončení hodnocení, zachová Contoso zařízení Azure Migrate k použití v budoucnu hodnocení.
- Contoso vypne virtuální počítač VMware. Společnost Contoso použije znovu při je vyhodnocen jako další virtuální počítače.
- Contoso udržuje **Contoso migrace** projektu v Azure. Projekt je aktuálně nasazené v **ContosoFailoverRG** skupinu prostředků v oblasti Azure USA – východ.
-  Virtuální počítač kolektoru má 180denní zkušební licenci. Pokud tento limit vyprší, bude Contoso nutné stáhnout kolektoru a znovu nastavit.

## <a name="conclusion"></a>Závěr

V tomto scénáři Contoso vyhodnocuje jeho databáze aplikace SmartHotel360 pomocí nástroje pro posouzení migrace dat. Ho pomocí služby Azure Migrate posuzuje místní virtuální počítače. Contoso kontroly posouzení, abyste měli jistotu, které místní prostředky jsou připravené k migraci do Azure.

## <a name="next-steps"></a>Další postup

V dalším článku v řadě ke Contoso kolizi jeho SmartHotel360 aplikace v Azure pomocí migrace lift and shift. Contoso migruje front-endu WEBVM pro aplikace s využitím Azure Site Recovery. Migrace databáze aplikace do Azure SQL Database Managed Instance pomocí službu Database Migration Service. [Začínáme](contoso-migration-rehost-vm-sql-managed-instance.md) s tímto nasazením.
