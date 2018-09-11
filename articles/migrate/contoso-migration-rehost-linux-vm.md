---
title: Migrovat metody opětovného hostování a změna hostitele aplikace v místním systému Linux do virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso změna hostitele aplikace v místním systému Linux migrací na virtuálních počítačích Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: raynew
ms.openlocfilehash: 4378351d6da6943485b7f382d96094543bfffa23
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297719"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Migrace Contoso: Změna hostitele aplikace v místním systému Linux do virtuálních počítačů Azure

Tento článek popisuje, jak se změna Contoso hostování aplikace helpdesku místní službu založenou na Linuxu (**osTicket**), na virtuální počítače Azure IaaS.

Tento dokument je jednou z řady článků, které dokumentují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a řada scénářů, které ukazují, jak nastavit infrastrukturu migrace a spustit různé druhy migrace. Scénáře jejich složitost v. Přidáme další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Přehled řady článku, strategie migrace společnosti Contoso a ukázkové aplikace, které se používají v řadě. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články týkající se migrace z této série se používá stejnou infrastrukturu. | K dispozici.
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md)  | Contoso spuštění posouzení své místní aplikace SmartHotel360 běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí služby Azure Migrate a databáze aplikace SQL Server pomocí Data Migration Assistant. | K dispozici.
[Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso běží lift and shift migrace do Azure pro své místní aplikace SmartHotel360. Contoso migruje aplikace front-endového virtuálního počítače pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migraci databáze aplikace do Azure SQL Database Managed Instance pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.   
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Contoso migruje jeho aplikace SmartHotel360 virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikace SmartHotel360. Společnost Contoso využívá Site Recovery k migraci virtuálních počítačů aplikace. Jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn využívá službu Database Migration Service. | K dispozici. 
Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem | Contoso dokončení migrace lift and shift aplikace osTicket Linux do virtuálních počítačů Azure pomocí Azure Site Recovery | V tomto článku
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Azure Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Contoso migruje aplikace SmartHotel360 do webové aplikace Azure a migraci databáze aplikace do instance serveru SQL Azure pomocí Pomocníka s migrací databáze | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci na Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje osTicket své Linuxové aplikace do webové aplikace Azure v několika oblastech Azure pomocí Azure Traffic Manageru, integrovaná se službou GitHub pro průběžné doručování. Contoso migraci databáze aplikace do Azure Database for MySQL – instance. | K dispozici. 
[Článek 11: Refaktorovat TFS na službách Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migrovat své místní nasazení serveru Team Foundation Server ke službám Azure DevOps v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jeho SmartHotel aplikace do Azure. Potom rearchitects webové vrstvy aplikace jako kontejner Windows se spuštěnou v Azure Service Fabric a databázi Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB... | K dispozici.



V tomto článku se bude migrovat Contoso dvě úrovně **osTicket** aplikace, běžící na Linuxu Apache MySQL PHP (LAMP) do Azure. Aplikace, virtuální počítače se budou migrovat pomocí služby Azure Site Recovery. Pokud chcete používat tuto aplikaci open source, můžete ji stáhnout [Githubu](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzké spolupráci s obchodními partnery pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso roste a kvůli tomu je tlak na místních systémů a infrastruktury.
- **Omezit riziko**: helpdesku app service je velmi důležité pro firmy Contoso. Contoso chce přesunout do Azure s nulovou rizika.
- **Rozšíření**: Contoso nechcete měnit aplikace hned teď. Jednoduše chce zajistit, že je aplikace stabilní.


## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci, určit nejlepší metody migrace:

- Po dokončení migrace aplikace v Azure by měla mít stejné možnosti výkonu stejně jako dnes v jejich místní prostředí VMWare.  Aplikace zůstanou jako kritické v cloudu je místní. 
- Contoso nechce investovat do této aplikace.  Je důležité pro podnikání, ale v aktuální podobě Contoso jednoduše chce, aby se bezpečně přesunout do cloudu.
- Chcete-li změnit model ops pro tuto aplikaci nechce contoso. Chce k interakci s touto aplikací v cloudu stejným způsobem, který teď dělají.
- Chcete-li změnit funkce nechce contoso. Se změní pouze umístění aplikace.
- Po dokončení několik přenesení aplikací Windows, Contoso chce informace o používání infrastruktury založené na Linuxu v Azure.


## <a name="solution-design"></a>Návrh řešení

Po Připnutí dolů cíle a požadavky, Contoso navrhuje, zkontrolujte nasazení řešení a identifikuje proces migrace, včetně služeb Azure, které Contoso použije pro migraci.

### <a name="current-app"></a>Aktuální aplikaci

- OSTicket aplikace je rozvrstvená na dva virtuální počítače (**OSTICKETWEB** a **OSTICKETMYSQL**).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5).
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (**contoso-datacenter**), s řadičem domény v místním (**contosodc1**)

### <a name="proposed-architecture"></a>Navrhované architektury

- Protože se aplikace a produkční úlohy, virtuální počítače v Azure se bude nacházet v provozní skupině prostředků **ContosoRG**.
- Virtuální počítače migrovat do primární oblasti (východní USA 2), který se umístí do produkční sítě (VNET-PROD-EUS2):
    - Virtuální počítač na webu se bude nacházet ve front-endové podsíti (PROD-FE – EUS2).
    - Databáze, virtuální počítač se bude nacházet v podsíti databáze (PROD-DB-EUS2).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.

![Architektura scénáře](./media/contoso-migration-rehost-linux-vm/architecture.png) 

### <a name="solution-review"></a>Kontrola řešení

Contoso vyhodnotí navrhovaných návrhu společně vložením seznam výhody a nevýhody.

**Posouzení** | **Podrobnosti**
--- | ---
**V oblasti IT** | Virtuální počítače s aplikace se přesunou do Azure beze změn, provedení migrace jednoduché.<br/><br/> Contoso používá lift and shift pro oba virtuální počítače s aplikací, nejsou potřeba žádné speciální konfigurace nebo migrace nástroje pro databázi aplikace.<br/><br/> Contoso se zachovat plnou kontrolu nad ní virtuální počítače v Azure. </br>/br > aplikace virtuální počítače se systémem Ubuntu 16.04-TLS, což je doporučené distribuce Linuxu. [Další informace](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).
**Nevýhody** | Web a datové vrstvy aplikace zůstanou jediný bod převzetí služeb při selhání. <br/><br/> Contoso se potřebujete i nadále podporuje aplikace jako virtuální počítače Azure, spíše než Přesun do spravované služby, jako je například Azure App Service a Azure Database for MySQL.<br/><br/> Contoso je vědět, že udržováním věci jednoduše se migrace virtuálních počítačů lift and shift, že nejsou plně využít funkcí poskytovaných službou [– Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) (integrovanou vysokou dostupnost, předvídatelný výkon Jednoduché škálování, automatické zálohování a integrované zabezpečení).

### <a name="migration-process"></a>Proces migrace

Contoso provede migraci následujícím způsobem:

1. Jako první krok Contoso nastaví Azure a místní infrastrukturu potřebnou k nasazení Site Recovery.
2. Jakmile připravíte Azure a místní komponenty, Contoso nastaví a umožňuje replikaci pro virtuální počítače.
3. Poté, co funguje replikace Contoso migraci virtuálních počítačů podle převzetí služeb při selhání je do Azure.

![Proces migrace](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migraci a zotavení po havárii pro virtuální počítače Azure a místních virtuálních počítačů a fyzických serverů.  | Při replikaci do Azure se účtují poplatky za úložiště Azure.  Virtuální počítače Azure jsou vytvářeny a účtovat poplatky, pokud dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) nákladů a ceny.

 
## <a name="prerequisites"></a>Požadavky

Tady je Contoso potřebuje pro tento scénář.

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Vytvořit odběry v rané fázi článku v této sérii contoso. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura Azure** | Nastavit infrastrukturu Azure, jak je popsáno v contoso [infrastrukturu Azure na migraci](contoso-migration-infrastructure.md).<br/><br/> Další informace o konkrétních [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) požadavky pro Site Recovery.
**Místní servery** | By měl být spuštěn na místní server vCenter verze 5.5, 6.0 nebo 6.5<br/><br/> Hostitele ESXi verze 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware běží na hostiteli ESXi.
**Místní virtuální počítače** | [Kontrola počítače s Linuxem](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) , které jsou podporované migrace pomocí Site Recovery.<br/><br/> Ověřte podporované [souboru a úložných systémů Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Virtuální počítače musí splňovat [požadavky služby Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroky scénáře

Zde je, jak bude Contoso dokončení migrace:

> [!div class="checklist"]
> * **Krok 1: Příprava Azure Site Recovery**: Contoso vytvoří účet služby Azure storage k ukládání replikovaných dat a vytvoří trezor služby Recovery Services.
> * **Krok 2: Příprava VMware v místním prostředí pro Site Recovery**: Contoso připraví účty, které má být použit pro virtuální počítač zjišťování a instalaci agenta a připraví se připojit k virtuálním počítačům Azure po převzetí služeb při selhání.
> * **Krok 3: Replikace virtuálních počítačů**: Contoso nastaví zdrojového a cílového prostředí migrace, vytvoří zásady replikace a spustí replikaci virtuálních počítačů do Azure storage.
> * **Krok 4: Migrace virtuálních počítačů pomocí Site Recovery**: Contoso běží převzetí služeb při selhání testu Ujistěte se, že všechno funguje a pak spustí úplné převzetí služeb při selhání k migraci virtuálních počítačů do Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Příprava Azure pro službu Site Recovery

Contoso potřebuje několika komponent Azure Site Recovery:

- Nový účet úložiště Azure pro uložení replikovaná data. 
- Trezor služby Recovery Services v Azure.
- Převzetí služeb při selhání virtuální síť, ve kterém jsou umístěny prostředky. Contoso již vytvořené virtuální síti během [nasazení infrastruktury Azure](contoso-migration-infrastructure.md), takže stačí vytvořit účet úložiště a trezoru.

1. Správce společnosti Contoso vytvořit účet úložiště Azure (contosovmsacc20180528) v oblasti východní USA 2.

    - Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
    - Účet general purpose, používají úložiště úrovně standard a replikací LRS.

    ![Site Recovery úložiště](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Pomocí účtu sítě a úložiště na místě, vytvořte trezor (ContosoMigrationVault) a jeho umístění **ContosoFailoverRG** skupinu prostředků, v primární oblasti USA – východ 2.

    ![Trezor služby Recovery Services](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Příprava VMware v místním prostředí pro Site Recovery

Správce společnosti Contoso Příprava na místní infrastrukturu VMware následujícím způsobem:

- Vytvoří účet na vCenter serveru nebo hostiteli vSphere ESXi, pro automatizaci zjišťování virtuálních počítačů.
- Vytvoří účet, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware, který chcete replikovat.
- Místní virtuální počítače, přípravě tak, aby se mohly připojit k virtuálním počítačům Azure po jejich vytvoření po migraci.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automatické zjišťování virtuálních počítačů. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Budete potřebovat účet, který může spouštět operace, jako je vytváření a odebírání disků a zapnutí na virtuálních počítačích.

Účet správce společnosti Contoso nastavte následujícím způsobem:

1. Vytvoří roli na úrovni vCenter.
2. Tato role se přiřadit požadovaná oprávnění.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na virtuálních počítačů s Linuxem, které se budou migrovat, musí se nainstalovat službu Mobility.

- Po povolení replikace pro virtuální počítače, může Site Recovery poskytnout automatickou nabízenou instalaci této součásti.
- Pro automatické nabízené instalace budou muset připravit účet, který Site Recovery použije pro přístup k virtuálním počítačům.
- Podrobnosti o účtech se vstup během instalace replikace. 
- Účet může být doménový nebo místní účet s oprávněními k instalaci na virtuálních počítačích.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání do Azure Contoso chce mít možnost se připojit k replikované virtuální počítače v Azure. Chcete-li to provést, je několik věcí, které je potřeba provést správce společnosti Contoso:

- Pro přístup k virtuálním počítačům Azure přes internet, umožňují SSH v Linuxu místní virtuální počítač před migrací.  Pro Ubuntu to můžete provést pomocí následujícího příkazu: **Sudo apt-get ssh, nainstalujte -y**.
- Po spuštění migrace (převzetí služeb při selhání), můžete zkontrolovat **Diagnostika spouštění** zobrazíte snímek obrazovky virtuálního počítače.
- Pokud to nepomůže, budete potřebovat ke kontrole, že virtuální počítač běží a přečtěte si tyto [tipy pro řešení potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) vytvoření a přiřazení role pro automatické zjišťování.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) vytvoření účtu pro nabízenou instalaci služby Mobility.


## <a name="step-3-replicate-the-on-premises-vms"></a>Krok 3: Replikace místních virtuálních počítačů

Před webové virtuálního počítače, můžete migrovat do Azure, správce společnosti Contoso nastavit a povolit replikaci.

### <a name="set-a-protection-goal"></a>Nastavte cíl ochrany

1. V trezoru v části název_trezoru (ContosoVMVault) nastavují cíle replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu**.
2. Určí, že jsou jejich počítače nacházejí na místních, že jsou virtuální počítače VMware. proto, že chcete replikovat do Azure.
    ![Cíl replikace](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Chcete-li pokračovat, potvrzení, že jejich dokončili jste plánování nasazení tak, že vyberete **Ano, mám to hotové**. Contoso je pouze jeden virtuální počítač v tomto scénáři migrace které nevyžadují plánování nasazení.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Správce společnosti Contoso nyní nutné nakonfigurovat zdrojového prostředí. K tomuto účelu, stáhněte šablonu OVF a použít k nasazení Site Recovery konfigurační server jako vysoce dostupný, místní virtuální počítač VMware. Po vytvoření a spuštění je konfigurační server, aby se zaregistrovali v trezoru.

Konfigurační server běží komponent:

- Komponenta konfiguračního serveru, který koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.

Správce společnosti Contoso následujícím způsobem proveďte tyto kroky:

1. Stáhněte šablonu OVF z **připravit infrastrukturu** > **zdroj** > **konfigurační Server**.
    
    ![Stáhněte si OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Import šablony do VMware. Tím vytvoříte požadovaný virtuální počítač a nasazení virtuálního počítače.

    ![Šablona OVF](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. Při jejich převodu na virtuálním počítači poprvé, se spustí do prostředí instalace Windows serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace se přihlásit k virtuálnímu počítači jako správce. Při prvním přihlášení ve výchozím nastavení spustí nástroj pro konfiguraci Azure Site Recovery.
5. V nástroji, zadejte název pro účely registrace konfiguračního serveru v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání připojení se přihlásit k předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.

    ![Registrace konfiguračního serveru](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Přihlášení k počítači znovu, a automaticky spustí Průvodce správou konfiguračního serveru.
9. V průvodci, vyberte síťovou kartu pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení konfigurace.
10. Vyberte předplatné, skupinu prostředků a trezor, do kterého chcete konfigurační server zaregistrovat.

    ![Trezor](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. Stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. 
12. Po ověření, zadejte plně kvalifikovaný název domény nebo IP adresa serveru vCenter nebo vSphere hostiteli. Ponechte výchozí port a zadejte popisný název pro vCenter server.
13. Zadejte účet, který je vytvořil pro automatické zjišťování a přihlašovací údaje použité pro automatickou instalaci služby Mobility.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Po dokončení registrace na portálu Azure, zkontrolujte, že konfigurační server a VMware server jsou uvedeny na **zdroj** stránky v trezoru. Zjišťování může trvat 15 minut nebo déle. 
15. Site Recovery se pak připojí k serverům VMware a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavení cíle

Teď správce společnosti Contoso nakonfigurujte nastavení cíle replikace.

1. V **připravit infrastrukturu** > **cílové**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a síť v zadané cílové.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Po zdroje a cíle jsou nastavení, budou připravené k vytvoření zásady replikace.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružit**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota cíle bodu obnovení**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí hodnotu 24 hodin. Tato hodnota určuje, jak dlouho je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímků konzistentní vzhledem k**. Výchozí jednu hodinu. Tato hodnota určuje frekvenci, s jakou se vytvořit snímky konzistentní s aplikací.
 
        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružte zásady replikace](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Potřebujete další pomoc?**

- Můžete si přečíst Úplný návod tohoto všechny tyto kroky v [nastavit zotavení po havárii pro virtuální počítače VMware v místním](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici při [zdrojové prostředí nastavíte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfiguračního serveru](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [konfigurace nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Další informace](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) o agenta hosta Azure pro Linux.

**Potřebujete další pomoc?**

- Můžete si přečíst Úplný návod tohoto všechny tyto kroky v [nastavit zotavení po havárii pro virtuální počítače VMware v místním](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici při [zdrojové prostředí nastavíte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfiguračního serveru](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [konfigurace nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Další informace](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) o agenta hosta Azure pro Linux.



### <a name="enable-replication-for-osticketweb"></a>Povolení replikace pro OSTICKETWEB

Nyní můžete spustit Správce společnosti Contoso, replikaci **OSTICKETWEB** virtuálního počítače.

1. V **replikovat aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Výběrem, které chtějí povolit virtuální počítače, vyberte nastavení zdroje, včetně vCenter serveru a konfiguračního serveru.

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Určí nastavení cíle, včetně skupiny prostředků a virtuální síť, ve kterém virtuálním počítači Azure budou umístěné po převzetí služeb při selhání a účet úložiště, ve kterém se ukládají replikovaná data.

     ![Povolení replikace](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Vyberou **OSTICKETWEB** pro replikaci virtuálního počítače. 

    - V této fázi vyberou **OSTICKETWEB** pouze, protože virtuální síť a podsíť musí obě být vybrána, a virtuální počítače nejsou ve stejné podsíti.
    - Po povolení replikace pro virtuální počítač, Site Recovery automaticky nainstaluje službu Mobility.

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. Ve vlastnostech virtuálního počítače, vyberte účet, který se používá procesový server při automatické instalaci služby Mobility na počítači.

     ![Služba Mobility](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. v **nastavení replikace** > **konfigurace nastavení replikace**, ověřte, zda je správná zásada replikace použít a vyberte **povolením replikace**.
6.  Sledují průběh replikace v **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.



### <a name="enable-replication-for-osticketmysql"></a>Povolení replikace pro OSTICKETMYSQL

Nyní můžete spustit Správce společnosti Contoso, replikaci **OSTICKETMYSQL**.

1. V **replikovat aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje a cíle.
2. Vyberou **OSTICKETMYSQL** virtuálního počítače pro replikaci a vyberte účet určený pro instalaci služby Mobility.

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Použití stejných zásad replikace, která byla použita pro OSTICKETWEB a povolit replikaci.  

**Potřebujete další pomoc?**

Můžete si přečíst Úplný návod tohoto všechny tyto kroky v [povolit replikaci](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Krok 4: Migrace virtuálních počítačů 

Spustit rychlou správce společnosti Contoso testovací převzetí služeb při selhání a potom migrovat virtuální počítače.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Spuštění testovacího převzetí služeb při pomáhá zajistit, že všechno funguje podle očekávání před migrací. 

1. Spustit testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovaný**).
2. Vyberou **před spuštěním převzetí služeb při selhání vypnout počítač**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před aktivací převzetí služeb. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Spuštění testovacího převzetí služeb při selhání: 
    - Kontrola spuštění, abyste měli jistotu, že jsou všechny podmínky pro migraci na místě požadavků.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud je vybrán poslední bod obnovení, se z dat vytvoří bod obnovení.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.
3. Po dokončení převzetí služeb se zobrazí na webu Azure Portal repliku virtuálního počítače Azure. Zkontrolujte, že je virtuální počítač odpovídající velikost, který je připojený ke správné síti a že je spuštěná. 
4. Po ověření, že vyčistit převzetí služeb při selhání a zaznamenejte a uložte jakékoli připomínky.

### <a name="create-and-customize-a-recovery-plan"></a>Vytvořit a přizpůsobit plán obnovení

 Po ověření, zda testovací převzetí služeb fungovala podle očekávání, správce společnosti Contoso vytvořit plán obnovení pro migraci. 

- Plán obnovení určuje pořadí, ve které převzetí služeb při selhání, jak se virtuální počítače Azure aktivují v Azure.
- Vzhledem k tomu, které chtějí přenést dvouvrstvou aplikaci, že budete přizpůsobit plán obnovení tak, aby dat virtuálního počítače (SQLVM) spustí před front-endu (WEBVM).


1. V **plány obnovení (služba Site Recovery)** > **+ plánu obnovení**, vytvoření plánu a přidejte do ní virtuální počítače.

    ![Plán obnovení](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Po vytvoření plánu, vyberou se pro přizpůsobení (**plány obnovení** > **OsTicketMigrationPlan** > **vlastní**.
3.  Odstraňují **OSTICKETWEB** z **skupiny 1: Start**.  Tím se zajistí, že první akci spuštění ovlivňuje **OSTICKETMYSQL** pouze.

    ![Obnovení skupiny](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  V **+ skupina** > **přidat chráněné položky**, přidávají **OSTICKETWEB** k **2. skupina: Start**.  Potřebují ve dvou různých skupin.

    ![Obnovení skupiny](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrace virtuálních počítačů


Správce společnosti Contoso jsou nyní připraven ke spuštění převzetí služeb při selhání v plánu obnovení, migrace virtuálních počítačů.

1. Vyberou plán > **převzetí služeb při selhání**.
2.  Výběru se převzetí služeb při selhání do nejnovějšího bodu obnovení a určit, že Site Recovery se měli pokusit vypněte místní virtuální počítač před aktivací převzetí služeb. Průběh převzetí služeb při selhání, můžete postupovat podle **úlohy** stránky.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm/failover1.png)

3. Během převzetí služeb při selhání systém vCenter Server vysílá příkazy Zastavit dva virtuální počítače běží na hostiteli ESXi.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Po převzetí služeb při selhání ověřují, že se virtuální počítač Azure zobrazuje podle očekávání na webu Azure Portal.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Po ověření virtuálních počítačů v Azure, provést migraci na dokončení procesu migrace pro každý virtuální počítač. Tím se zastaví replikace virtuálního počítače a zastaví se fakturace služby Site Recovery pro virtuální počítač.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Virtuální počítač připojit k databázi

Posledním krokem v procesu migrace Contoso adins aktualizovat připojovací řetězec aplikace tak, aby odkazoval na databázi aplikace běžící na **OSTICKETMYSQL** virtuálního počítače. 

1. Ujistěte se, že připojení SSH ke **OSTICKETWEB** virtuálního počítače pomocí klienta Putty nebo jiného klienta SSH. Virtuální počítač je privátní, takže se připojit pomocí privátní IP adresu.

    ![připojení k databázi](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![připojení k databázi](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Ujistěte se, že potřebují **OSTICKETWEB** virtuální počítač může komunikovat s **OSTICKETMYSQL** virtuálního počítače. Konfigurace je aktuálně pevně zakódované 172.16.0.43 místní IP adresou.

    **Před aktualizací**
    
    ![Aktualizovat IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Po aktualizaci**
    
    ![Aktualizovat IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Restartu této služby s využitím **systemctl restartovat apache2**.

    ![Restartování](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Nakonec se aktualizovat záznamy DNS pro **OSTICKETWEB** a **OSTICKETMYSQL**, na jednom z řadičů domény Contoso.

    ![Aktualizovat DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Aktualizovat DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštění testovací převzetí služeb při selhání. 
- [Přečtěte si](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) převzetí služeb při selhání do Azure.

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Pomocí migrace byla dokončena aplikačních vrstvách osTicket běží na virtuálních počítačích Azure.

Nyní Contoso potřebuje k vyčištění následujícím způsobem: 

- Odebrání místních virtuálních počítačů z inventáře vCenter.
- Odebrání místních virtuálních počítačů z místní úlohy zálohování.
- Aktualizace zobrazíte nové umístění a IP adresy pro OSTICKETWEB a OSTICKETMYSQL své interní dokumentaci.
- Zkontrolujte všechny prostředky, které pracují s virtuálními počítači a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.
- Contoso používá služba Azure Migrate s mapováním závislostí pro vyhodnocení virtuálních počítačů pro migraci. Správci by měla odebrat agenta Microsoft Monitoring Agent a jejich instalaci pro tento účel, z virtuálního počítače agenta závislostí.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

V aplikaci aktuálně spuštěno musí společnosti Contoso plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

Bezpečnostní tým Contoso zkontrolujte OSTICKETWEB a OSTICKETMYSQLVMs, chcete-li zjistit všechny problémy se zabezpečením.

- Tým kontroly skupiny zabezpečení sítě (Nsg) pro virtuální počítače pro řízení přístupu. Skupiny zabezpečení sítě se používají k zajištění, že lze předat pouze provoz povolený pro aplikaci.
- Tým také bere v úvahu zabezpečení dat na discích virtuálních počítačů pomocí šifrování disku a Azure Key Vaultu.

[Přečtěte si další](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupech zabezpečení pro virtuální počítače.

### <a name="bcdr"></a>BCDR

Společnosti Contoso pro provozní kontinuitu a zotavení po havárii, provede následující akce:

- **Bezpečnost dat**: Contoso zálohuje data na virtuálních počítačích pomocí služby Azure Backup. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Udržujte aplikace rychle zprovoznit**: Contoso replikuje aplikace virtuální počítače v Azure do sekundární oblasti vzdálené používání služby Site Recovery. [Další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Po nasazení prostředků, Contoso přiřadí Azure značky definované během [nasazení infrastruktury Azure](contoso-migration-infrastructure.md#set-up-tagging).
- Contoso nemá žádné problémy s licencí se servery se systémem Ubuntu.
- Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management. 


## <a name="next-steps"></a>Další postup

V tomto článku, který jsme vám ukázali, jak společnost Contoso migraci místní služby podpory aplikace Vrstvená na dva virtuální počítače s Linuxem do virtuálních počítačů Azure IaaS, pomocí Azure Site Recovery.

V dalším článku v této sérii vám ukážeme, jak společnosti Contoso migruje stejné helpdesku app service do Azure. Tentokrát Contoso využívá Site Recovery k migraci front-endový virtuální počítač pro aplikaci a migraci databáze pomocí aplikace zálohování a obnovení do služby Azure Database for MySQL pomocí nástroje MySQL workbench. [Začínáme](contoso-migration-rehost-linux-vm-mysql.md).
