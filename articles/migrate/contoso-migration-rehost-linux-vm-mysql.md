---
title: Změna hostitele Contoso Linuxovou aplikaci podpory služby Azure a MySQL v Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso ke kolizi aplikace v místním systému Linux migrací virtuálních počítačů Azure a Azure MySQL.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fbb70bd20b89bb1b711630ba54fe31806292385c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002224"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Migrace Contoso: Změna hostitele aplikace Linux místních virtuálních počítačů Azure a Azure MySQL

Tento článek popisuje, jak společnosti Contoso ke kolizi helpdesku aplikace v místním dvouvrstvé Linux service (osTicket), díky migraci na Azure a Azure MySQL.

Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které ukazují, jak nastavit infrastrukturu migrace a spustit různé druhy migrace. Scénáře jejich složitost v a budeme přidávat další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
[Článek 3: Posouzení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak společnosti Contoso spuštění posouzení jejich místní dvouvrstvé SmartHotel aplikace spuštěné ve VMware. Jejich posouzení virtuálních počítačů aplikace s [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Metody opětovného hostování virtuálních počítačů Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do Azure. Migraci virtuálního počítače webové aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a aplikace pomocí databáze [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) služby k migraci do spravované Instance SQL. | K dispozici.
[Článek 5: Změna hostitele na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak migrovat Contoso jejich SmartHotel do virtuálních počítačů Azure IaaS, pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele na virtuálních počítačích Azure a skupiny dostupnosti SQL serveru](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace. Používají Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do skupiny dostupnosti SQL serveru. | K dispozici.
[Článek 7: Změna hostitele Linuxovou aplikaci do virtuálních počítačů Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak společnosti Contoso migruje jejich osTicket Linuxovou aplikaci do virtuálních počítačů Azure IaaS pomocí služby Azure Site Recovery. | K dispozici.
Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure serveru MySQL | Ukazuje, jak společnosti Contoso migruje osTicket Linuxovou aplikaci. Site Recovery pro migraci virtuálního počítače a aplikace MySQL Workbench používají k migraci na instanci serveru Azure MySQL. | Tento článek.
[Článek 9: Refaktorujte aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci na Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje aplikace osTicket Linux do Azure Web Apps ve více lokalitách, integrovaná se službou GitHub pro průběžné doručování. Jejich migrovat databázi aplikace na instanci Azure MySQL. | K dispozici.
[Článek 11: Refaktorovat TFS na VSTS](contoso-migration-tfs-vsts.md) | Ukazuje, jak společnosti Contoso migruje svoje místní nasazení Team Foundation Server (TFS) a migrujte jej na Visual Studio Team Services (VSTS) v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | K dispozici.


V tomto článku se migruje Contoso aplikace služby podpory Linux Apache MySQL PHP (LAMP) dvouvrstvé (osTicket) do Azure. Pokud chcete používat tuto aplikaci open source, můžete ji stáhnout [Githubu](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chtějí dosáhnout:

- **Adresa obchodní růst**: Contoso roste a kvůli tomu je tlak na místních systémů a infrastruktury.
- **Omezit riziko**: helpdesku app service je velmi důležité pro firmy Contoso. Chtějí se přesunout do Azure s nulovou rizika.
- **Rozšíření**: Contoso nechce změnit aplikaci hned teď. Jednoduše chtějí zachovat stabilní.


## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci, aby bylo možné určit nejlepší metody migrace:

- Po dokončení migrace aplikace v Azure by měla mít stejné možnosti výkonu stejně jako dnes v jejich místní prostředí VMWare.  Aplikace zůstanou jako kritické v cloudu je místní. 
- Contoso nechce investovat do této aplikace.  Je důležité pro podnikání, ale v aktuální podobě chtějí jednoduše bezpečně přesunout do cloudu.
- Po dokončení několik přenesení aplikací Windows, Contoso chce informace o používání infrastruktury založené na Linuxu v Azure.
- Contoso chce minimalizovat úlohy správy databáze po přesunu aplikace do cloudu.

## <a name="proposed-architecture"></a>Navrhované architektury

V tomto scénáři:

- Aplikace je rozvrstvená na dva virtuální počítače (OSTICKETWEB a OSTICKETMYSQL).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5).
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Webová aplikace vrstvy v OSTICKETWEB budou migrovány na Virtuálním počítači Azure IaaS.
- Databáze aplikace budou migrovány na služba Azure Database for MySQL PaaS.
- Vzhledem k tomu, že migrujete produkční úlohy, budou uložené prostředky ve skupině prostředků produkční **ContosoRG**.
- Prostředky se replikují do primární oblasti (východní USA 2) a umístí do produkční sítě (VNET-PROD-EUS2):
    - Virtuální počítač na webu se bude nacházet ve front-endové podsíti (PROD-FE – EUS2).
    - Instance databáze budou umístěné v podsíti databáze (PROD-DB-EUS2).
- Databáze aplikace bude migrovat na Azure MySQL pomocí nástroje MySQL.
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.


![Architektura scénáře](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>Proces migrace

Contoso dokončí proces migrace následujícím způsobem:

K migraci virtuálního počítače na webu:

1. Jako první krok Contoso nastaví Azure a místní infrastrukturu potřebnou k nasazení Site Recovery.
2. Jakmile připravíte Azure a místní komponenty, jejich nastavení a povolíte replikaci pro virtuální počítač na webu.
3. Spuštěnou a až po replikaci se migrovat virtuální počítač po převzetí služeb při selhání se k Azure.

Pro migraci databáze:

1. Contoso zřídí instanci MySQL v Azure.
2. Nastavení aplikace MySQL workbench a proveďte zálohu databáze v místním prostředí.
3. Potom se obnovit databázi z místního zálohování do Azure.

![Proces migrace](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migraci a zotavení po havárii pro virtuální počítače Azure a místních virtuálních počítačů a fyzických serverů.  | Při replikaci do Azure se účtují poplatky za úložiště Azure.  Virtuální počítače Azure jsou vytvářeny a účtovat poplatky, pokud dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) nákladů a ceny.
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Databáze je založen na open source modulu MySQL Server. Poskytuje plně spravované a připravená pro podnikové Společenství databáze MySQL jako služba pro vývoj a nasazování aplikací. 

 
## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit tento scénář můžete vy (a Contoso), zde je, co byste měli mít.

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste již vytvořili předplatné během dřívější články v této sérii. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura Azure** | Nastavit infrastrukturu Azure, jak je popsáno v contoso [infrastrukturu Azure na migraci](contoso-migration-infrastructure.md).<br/><br/> Další informace o konkrétních [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) požadavky pro Site Recovery.
**Místní servery** | Vaše místní vCenter server by měl být spuštěn verze 5.5, 6.0 nebo 6.5<br/><br/> Hostitele ESXi verze 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware běží na hostiteli ESXi.
**Místní virtuální počítače** | [Zkontrolujte požadavky na virtuální počítač s Linuxem](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) , které jsou podporované migrace pomocí Site Recovery.<br/><br/> Ověřte podporované [souboru a úložných systémů Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Virtuální počítače musí splňovat [požadavky služby Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroky scénáře

Zde je, jak Azure dokončí migraci:

> [!div class="checklist"]
> * **Krok 1: Příprava Azure Site Recovery**: vytvoření účtu služby Azure storage k ukládání replikovaných dat a vytvoříte trezor služby Recovery Services.
> * **Krok 2: Příprava VMware v místním prostředí pro Site Recovery**: Příprava virtuálního počítače zjišťování a instalaci agenta účty a příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání.
 * **Krok 3: Zřízení databáze]**: V Azure, zřídit instanci databáze Azure MySQL.
> * **Krok 4: Replikace virtuálních počítačů**: Nakonfigurujte Site Recovery zdrojového a cílového prostředí, nastavení zásady replikace a spustit replikaci virtuálních počítačů do Azure storage.
> * **Krok 5: Migrace databáze**: nastavení migrace s využitím nástrojů MySQL.
> * **Krok 6: Migrace virtuálních počítačů pomocí Site Recovery**: Spusťte převzetí služeb při selhání testu Ujistěte se, že všechno funguje a pak spusťte úplné převzetí služeb při selhání k migraci virtuálních počítačů do Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Příprava Azure pro službu Site Recovery

Contoso potřebuje několika komponent Azure Site Recovery:

- Převzetí služeb při selhání virtuální síť, ve kterém jsou umístěné prostředky (Contoso použije produkční virtuální síť už nasazená).
- Nový účet úložiště Azure pro uložení replikovaná data. 
- Trezor služby Recovery Services v Azure.

Contoso již vytvořené virtuální síti během [nasazení infrastruktury Azure](contoso-migration-infrastructure.md), takže stačí vytvořit účet úložiště a trezoru.


1. Contoso vytvoří účet služby Azure storage (**contosovmsacc20180528**) v oblasti východní USA 2.

    - Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
    - Společnost Contoso využívá účet general purpose s storage úrovně standard a replikací LRS.

    ![Site Recovery úložiště](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Pomocí účtu sítě a úložiště na místě, vytvoří trezor (ContosoMigrationVault) společnosti Contoso a umístí jej do **ContosoFailoverRG** skupinu prostředků, v primární oblasti USA – východ 2.

    ![Trezor služby Recovery Services](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Příprava VMware v místním prostředí pro Site Recovery

Contoso připraví na místní infrastrukturu VMware následujícím způsobem:

- Vytvoří účet na serveru vCenter, pro automatizaci zjišťování virtuálních počítačů.
- Vytvoří účet, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware, který chcete replikovat.
- Připraví místních virtuálních počítačů, tak, aby se mohly připojit k virtuálním počítačům Azure po jejich vytvoření po migraci.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automatické zjišťování virtuálních počítačů. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Budete potřebovat účet, který může spouštět operace, jako je vytváření a odebírání disků a zapnutí na virtuálních počítačích.

Účet contoso nastaví takto:

1. Contoso vytvoří roli na úrovni vCenter.
2. Contoso poté přiřadí tuto roli požadovaná oprávnění.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na každém virtuálním počítači, který Contoso chce migrace musíte nainstalovat službu Mobility.

- Když povolíte replikaci pro virtuální počítače, může Site Recovery poskytnout automatickou nabízenou instalaci této součásti.
- U automatické instalace. Site Recovery potřebuje účet s oprávněními pro přístup k virtuálnímu počítači. 
- Podrobnosti o účtu se vstup během instalace replikace. 
- Účet může být doménový nebo místní účet, jako má oprávnění k instalaci.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání do Azure Contoso chce mít možnost se připojit k virtuálním počítačům Azure. Provedete to tak, že musí udělat několik věcí, které: 

- Přístup přes internet, umožňují SSH v Linuxu místní virtuální počítač před migrací.  Pro Ubuntu to můžete provést pomocí následujícího příkazu: **Sudo apt-get ssh, nainstalujte -y**.
- Po převzetí služeb při selhání, by měl zkontrolovat **Diagnostika spouštění** zobrazíte snímek obrazovky virtuálního počítače.
- Pokud to nepomůže, je potřeba ověřit, že virtuální počítač běží a přečtěte si tyto [tipy pro řešení potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) vytvoření a přiřazení role pro automatické zjišťování.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) vytvoření účtu pro nabízenou instalaci služby Mobility.


## <a name="step-3-provision-azure-database-for-mysql"></a>Krok 3: Zřízení Azure Database for MySQL

Contoso předpisy MySQL databáze instance v primární oblasti USA – východ 2.

1. Na webu Azure Portal vytváření serveru Azure Database for MySQL prostředků. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Přidejte název **contosoosticket** pro databázi Azure. Přidávají do skupiny prostředků produkční databázi **ContosoRG**a zadejte přihlašovací údaje pro něj.
3. V místní databázi MySQL je verzi 5.7, takže vyberou tuto verzi z důvodu kompatibility. Používají výchozí velikosti, které odpovídají jejich požadavky na databázi.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. Pro **možnosti redundance zálohy**, vybere Contoso použijte **geograficky redundantní**. Tato možnost umožňuje jejich obnovení databáze v jejich sekundární oblasti střed USA, pokud dojde k výpadku. Tato možnost mohou nakonfigurovat pouze při zřizování databáze.

     ![Redundance](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. V **připojení typu VNET-PROD-EUS2** sítě > **koncové body služby**, přidávají koncového bodu služby (databáze podsítě) pro službu SQL.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Po přidání podsítě, vytvoří pravidlo virtuální sítě, které umožňuje přístup z podsítě databáze v produkční síti.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Krok 4: Replikace místních virtuálních počítačů

Před webové virtuálního počítače, můžete migrovat do Azure, Contoso nastaví a umožňuje replikaci.

### <a name="set-a-protection-goal"></a>Nastavte cíl ochrany

1. V trezoru v části název_trezoru (ContosoVMVault) nastavují cíle replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu**.
2. Určí, že jsou jejich počítače nacházejí na místních, že jsou virtuální počítače VMware. proto, že chcete replikovat do Azure.

    ![Cíl replikace](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Chcete-li pokračovat, potvrzení, že jejich dokončili jste plánování nasazení tak, že vyberete **Ano, mám to hotové**. Contoso je pouze jeden virtuální počítač v tomto scénáři migrace které nevyžadují plánování nasazení.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Contoso je potřeba nakonfigurovat jejich zdrojového prostředí. Provedete to pomocí šablony OVF, nasazení konfiguračního serveru Site Recovery jako s vysokou dostupností, místní virtuální počítač VMware. Po vytvoření a spuštění je konfigurační server, aby se zaregistrovali v trezoru.

Konfigurační server běží komponent:

- Komponenta konfiguračního serveru, který koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- Zahájení převzetí služeb při selhání s jedním kliknutím na portálu, nebo můžete použít  Powershellu k aktivaci převzetí služeb při selhání. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.

Contoso následujícím způsobem proveďte tyto kroky:


1. Stáhněte šablonu OVF z **připravit infrastrukturu** > **zdroj** > **konfigurační Server**.
    
    ![Stáhněte si OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Import šablony do VMware. Tím vytvoříte požadovaný virtuální počítač a nasazení virtuálního počítače.

    ![Šablona OVF](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. Při jejich převodu na virtuálním počítači poprvé, se spustí do prostředí instalace Windows serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce. Při prvním přihlášení ve výchozím nastavení spustí nástroj pro konfiguraci Azure Site Recovery.
5. V nástroji, zadejte název pro účely registrace konfiguračního serveru v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure.
7. Po navázání připojení se přihlásit k předplatnému Azure. Přihlašovací údaje musí mít přístup k trezoru, ve kterém se bude registrace konfiguračního serveru.

    ![Registrace konfiguračního serveru](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
9. Přihlášení k počítači znovu, a automaticky spustí Průvodce správou konfiguračního serveru.
10. V průvodci, vyberte síťovou kartu pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení konfigurace.
11. Vyberte předplatné, skupinu prostředků a trezor, do kterého chcete konfigurační server zaregistrovat.

    ![Trezor](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. Nyní stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. 
13. Po ověření, zadejte plně kvalifikovaný název domény nebo IP adresa serveru vCenter nebo vSphere hostiteli. Ponechte výchozí port a zadejte popisný název pro vCenter server.
14. Zadejte účet, který je vytvořil pro automatické zjišťování a přihlašovací údaje, které Site Recovery se používají pro automatickou instalaci služby Mobility. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. Po dokončení registrace na webu Azure Portal, Contoso ověří, že konfigurační server a VMware server jsou uvedeny na **zdroj** stránky v trezoru. Zjišťování může trvat 15 minut nebo déle. 
15. Všechno v místě Site Recovery se připojí k serverům VMware a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavení cíle

Contoso vstupů teď cílových nastavení replikace.

1. V **připravit infrastrukturu** > **cílové**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a síť v zadané cílové.


### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Contoso se zdrojem a cílem nastavit, je připravená k vytvoření zásady replikace.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružit**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota cíle bodu obnovení**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí hodnotu 24 hodin. Tato hodnota určuje, jak dlouho je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímků konzistentní vzhledem k**. Výchozí jednu hodinu. Tato hodnota určuje frekvenci, s jakou se vytvořit snímky konzistentní s aplikací.
 
        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružte zásady replikace](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Potřebujete další pomoc?**

- Můžete si přečíst Úplný návod tohoto všechny tyto kroky v [nastavit zotavení po havárii pro virtuální počítače VMware v místním](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici při [zdrojové prostředí nastavíte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfiguračního serveru](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [konfigurace nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Další informace](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) o agenta hosta Azure pro Linux.

### <a name="enable-replication-for-the-web-vm"></a>Povolení replikace pro virtuální počítač Web

Nyní můžete Contoso zahájení replikace **OSTICKETWEB** virtuálního počítače.

1. V **replikovat aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Indikuje, že chtějí povolit virtuální počítače a vyberte nastavení zdroje, včetně vCenter serveru a konfiguračního serveru.

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Teď zadejte nastavení cíle. Patří mezi ně skupinu prostředků a síť, ve kterém virtuálním počítači Azure budou umístěné po převzetí služeb při selhání a účet úložiště, ve kterém se ukládají replikovaná data. 

     ![Povolení replikace](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Vybere contoso **OSTICKETWEB** pro replikaci. 

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. Ve vlastnostech virtuálního počítače, vyberte účet, který se má použít pro automatickou instalaci služby Mobility na virtuálním počítači.

     ![Služba Mobility](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. v **nastavení replikace** > **konfigurace nastavení replikace**, ověřte, zda je správná zásada replikace použít a vyberte **povolením replikace**. Služba Mobility se automaticky nainstaluje.
6.  Sledují průběh replikace v **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.


**Potřebujete další pomoc?**

Můžete si přečíst Úplný návod tohoto všechny tyto kroky v [povolit replikaci](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Krok 5: Migrace databáze

Contoso provede migraci databází pomocí zálohování a obnovení pomocí nástrojů MySQL. Jejich aplikaci MySQL Workbench můžete nainstalovat, proveďte zálohu databáze z OSTICKETMYSQL a jeho následnému obnovení – Azure Database for MySQL Server.

### <a name="install-mysql-workbench"></a>Instalace aplikace MySQL Workbench

1. Contoso kontroly [požadavky a soubory ke stažení aplikace MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Instalace aplikace MySQL Workbench pro Windows v souladu s maticí [pokyny k instalaci](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. V aplikaci MySQL Workbench vytvoří připojení k OSTICKETMYSQL MySQL. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. Jejich export databáze jako **osticket**, místní samostatný soubor.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Po databáze byly zálohovány místně, vytvoří připojení k Azure Database for MySQL – instance.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Soubor (obnovení) databáze v instanci Azure MySQL, místním obsažených teď můžete importovat Contoso. Nové schéma (osticket) je vytvořena instance.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Krok 6: Migrace virtuálních počítačů pomocí Site Recovery

Spustit rychlou contoso testovací převzetí služeb při selhání a potom migrujete virtuální počítač.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Spuštění testovacího převzetí služeb při pomáhá ověřit, že vše funguje podle očekávání, před migrací. 

1. Contoso spustí testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovaný**).
2. Vyberou **před spuštěním převzetí služeb při selhání vypnout počítač**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před aktivací převzetí služeb. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Spuštění testovacího převzetí služeb při selhání: 

    - Kontrola spuštění, abyste měli jistotu, že jsou všechny podmínky pro migraci na místě požadavků.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

3. Po dokončení převzetí služeb se zobrazí na webu Azure Portal repliku virtuálního počítače Azure. Contoso ověří, že je virtuální počítač odpovídající velikost, který je připojený ke správné síti a že je spuštěná. 
4. Po ověření, že vyčistit převzetí služeb při selhání a zaznamenejte a uložte jakékoli připomínky.

### <a name="migrate-the-vm"></a>Migrace virtuálního počítače

Migrovat virtuální počítač, Contoso vytvoří plán obnovení, která obsahuje virtuální počítač a selhání plánu přes do Azure.

1. Contoso vytvoří plán a přidá **OSTICKETWEB** k němu.

    ![Plán obnovení](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. Spuštění převzetí služeb při selhání v plánu. Vyberte nejnovější bod obnovení a určit, že Site Recovery se měli pokusit vypněte místní virtuální počítač před aktivací převzetí služeb. Průběh převzetí služeb při selhání, můžete postupovat podle **úlohy** stránky.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Během převzetí služeb při selhání systém vCenter Server vysílá příkazy Zastavit dva virtuální počítače běží na hostiteli ESXi.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Po převzetí služeb při selhání Contoso ověřuje, že se virtuální počítač Azure zobrazuje podle očekávání na webu Azure Portal.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Po vrácení virtuálního počítače, dokončení migrace. Tím se zastaví replikace virtuálního počítače a zastaví se fakturace služby Site Recovery pro virtuální počítač.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštění testovací převzetí služeb při selhání. 
- [Přečtěte si](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) převzetí služeb při selhání do Azure.


### <a name="connect-the-vm-to-the-database"></a>Virtuální počítač připojit k databázi

Posledním krokem v procesu migrace Contoso aktualizovat připojovací řetězec aplikace tak, aby odkazoval do služby Azure Database for MySQL. 

1. Vytvořte připojení SSH k virtuálnímu počítači OSTICKETWEB pomocí klienta Putty nebo jiného klienta SSH. Virtuální počítač je privátní, takže se připojit pomocí privátní IP adresu.

    ![připojení k databázi](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![připojení k databázi](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. Aktualizují nastavení tak, aby **OSTICKETWEB** virtuální počítač může komunikovat s **OSTICKETMYSQL** databáze. Konfigurace je aktuálně pevně zakódované 172.16.0.43 místní IP adresou.

    **Před aktualizací**
    
    ![Aktualizovat IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **Po aktualizaci**
    
    ![Aktualizovat IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![Aktualizovat IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. Restartu této služby s využitím **systemctl restartovat apache2**.

    ![Restartování](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Nakonec se aktualizovat záznamy DNS pro **OSTICKETWEB**, na jednom z řadičů domény Contoso.

    ![Aktualizovat DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Vyčištění po migraci

Pomocí migrace byla dokončena aplikačních vrstvách osTicket běží na virtuálních počítačích Azure.

Nyní Contoso potřebuje provést následující kroky: 
- Odebrat virtuální počítače VMware z inventáře vCenter
- Odebrání místních virtuálních počítačů z místní úlohy zálohování.
- Aktualizace interní dokumentaci ukazují novým umístěním a IP adresy. 
- Zkontrolujte všechny prostředky, které pracují s místní virtuální počítače a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.
- Contoso používá služba Azure Migrate se mapování závislostí k vyhodnocení **OSTICKETWEB** virtuálního počítače pro migraci. Teď odstraňte agenty (Microsoft Monitoring Agent agenta nebo závislosti) instalovány pro tento účel, z virtuálního počítače.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Nyní spuštěnou aplikaci Contoso nutné plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

Bezpečnostní tým Contoso zkontrolujte virtuálního počítače a databáze, chcete-li zjistit všechny problémy se zabezpečením.

- Zkontrolujte tyto skupiny zabezpečení sítě (Nsg) pro virtuální počítač pro řízení přístupu. Skupiny zabezpečení sítě se používají k zajištění, že lze předat pouze provoz povolený pro aplikaci.
- Považují za zabezpečení dat na discích virtuálních počítačů pomocí šifrování disku a Azure Key Vaultu.
- Komunikace mezi instance virtuálních počítačů a databáze není nakonfigurovaný pro protokol SSL. Se musí provést k zajištění, že nemůže být zneužity provozu databáze.

[Přečtěte si další](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupech zabezpečení pro virtuální počítače.

### <a name="backups"></a>Zálohování

- Contoso bude zálohovat data na virtuálním počítači pomocí služby Azure Backup. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Není potřeba konfigurace zálohování pro databázi. Azure Database for MySQL automaticky vytvoří serverových záloh a uloží. Vybrali použijte geografickou redundanci pro databáze, tak, aby byl odolný a připravené pro produkční prostředí.

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Po nasazení prostředků, Contoso přiřadí Azure značky, v souladu s rozhodnutí, která byly provedeny během [infrastruktury Azure](contoso-migration-infrastructure.md#set-up-tagging) nasazení.
- Nejsou žádné problémy s licencí pro servery se systémem Contoso Ubuntu.
- Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management.


## <a name="next-steps"></a>Další postup

V tomto scénáři jsme vám ukázali scénář finální metody opětovného hostování, Contoso si vyzkoušeli. Migrovat virtuální počítač v místním systému Linux osTicket aplikace front-endu na Virtuálním počítači Azure a migrovat databázi aplikace na instanci Azure MySQL.

V sadě další kurzy v této sérii migrace jsme teď ukazují, jak společnosti Contoso provést složitější sadu migrace, zahrnující refaktoring aplikace, místo jednoduchých migrace lift and shift.
