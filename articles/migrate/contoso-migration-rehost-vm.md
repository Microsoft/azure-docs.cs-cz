---
title: Změna hostitele aplikace Contoso s migrací na virtuální počítače Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak metody opětovného hostování místní aplikaci pomocí lift and shift migrace místních počítačů do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 11859beb3d7bf0d0b0b801328c6570d274f1ea68
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42060075"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migrace Contoso: Změna hostitele místní aplikaci do virtuálních počítačů Azure


Tento článek ukazuje, jak společnosti Contoso ke kolizi místní SmartHotel aplikaci v Azure, díky migraci aplikace, virtuální počítače na virtuální počítače Azure.


Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migruje n místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které znázorňují nastavování infrastruktury migrace, posuzování migrace místních prostředků a spouštění různé druhy migrace. Scénáře jejich složitost v a přidáme další články v čase.


**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny články týkající se migrace. | K dispozici.
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md)  | Ukazuje, jak společnosti Contoso spuštění posouzení místních dvouvrstvé SmartHotel aplikaci spuštěnou v prostředí VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Pomocníka s migrací databáze](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Změna hostitele aplikací na virtuálních počítačích Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso běží lift and shift migrace do Azure pro místní SmartHotel aplikaci. Contoso migruje virtuální počítač front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace do spravované Instance SQL, pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.
Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure | Ukazuje, jak společnosti Contoso aplikaci SmartHotel virtuální počítače migrovat do virtuálních počítačů Azure pomocí služby Site Recovery. | Tento článek.
[Článek 6: Změna hostitele aplikací na virtuálních počítačích Azure a skupina dostupnosti AlwaysOn SQL serveru](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace. Contoso využívá Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso funguje lift and shift migrace aplikace osTicket Linux na virtuální počítače Azure pomocí služby Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci na Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje aplikace osTicket Linux do Azure Web Apps ve více lokalitách, integrovaná se službou GitHub pro průběžné doručování. Jejich migrovat databázi aplikace na instanci Azure MySQL. | K dispozici.
[Článek 11: Refaktorovat TFS na VSTS](contoso-migration-tfs-vsts.md) | Ukazuje, jak společnosti Contoso migruje svoje místní nasazení Team Foundation Server (TFS) a migrujte jej na Visual Studio Team Services (VSTS) v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | K dispozici.



V tomto článku se bude migrovat Contoso dvouvrstvé Windows. NET SmartHotel aplikaci spuštěnou v prostředí virtuálních počítačů VMware do Azure. Pokud chcete používat tuto aplikaci, se poskytuje jako open source a můžete ji stáhnout [githubu](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzké spolupráci s obchodními partnery pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso roste a kvůli tomu je tlak na svých místních systémů a infrastruktury.
- **Omezit riziko**: The SmartHotel aplikace je velmi důležité pro firmy Contoso. Chtějí se přesunout do Azure s nulovou rizika.
- **Rozšíření**: Contoso nechce upravit aplikaci. Jednoduše chtějí se ujistěte, že se jedná o stabilní.


## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci. Tyto cíle se používají k určení nejlepší metody migrace:

- Po dokončení migrace aplikace v Azure by měla mít stejné možnosti výkonu, stejně jako dnes ve službě VMware.  Aplikace zůstanou jako kritické v cloudu je místní. 
- Contoso nechce investovat do této aplikace.  Je důležité pro podnikání, ale v aktuální podobě chtějí jednoduše bezpečně přesunout do cloudu.
- Chcete-li změnit model ops pro tuto aplikaci nechce contoso. Chtějí se s ní pracovat v cloudu stejným způsobem, který teď dělají.
- Chcete-li změnit všechny funkce aplikace nechce contoso. Se změní pouze umístění aplikace.

## <a name="proposed-architecture"></a>Navrhované architektury

Tady je v aktuálním prostředí

- Aplikace je rozvrstvená na dva virtuální počítače (**WEBVM** a **SQLVM**).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5).
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.

![Architektura scénáře](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>Proces migrace

Contoso se migrovat front-endu aplikace a databáze, virtuální počítače na virtuální počítače Azure pomocí Site Recovery:

- Jako první krok jejich Připravíme a nastavit komponenty Azure Site Recovery a příprava na místní infrastrukturu VMware.
- Už mají jejich [infrastruktury Azure](contoso-migration-infrastructure.md) na místě, takže stačí musí přidat několika komponent Azure speciálně pro Site Recovery.
- Všechno připravené můžou spustit replikaci virtuálních počítačů.
– Po povolení replikace a práci, migraci virtuálního počítače pomocí převzetí služeb při selhání ho do Azure.

![Proces migrace](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migraci a zotavení po havárii pro virtuální počítače Azure a místních virtuálních počítačů a fyzických serverů.  | Při replikaci do Azure se účtují poplatky za úložiště Azure.  Virtuální počítače Azure jsou vytvářeny a účtovat poplatky, pokud dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) nákladů a ceny.


## <a name="prerequisites"></a>Požadavky

Tady je můžete vy (a Contoso) potřebovat ke spuštění tohoto scénáře.

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste již vytvořili předplatné během dřívější články v této sérii. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.<br/><br/> Další informace o konkrétních [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) požadavky pro Site Recovery.
**Místní servery** | Pro místní servery vCenter server by měl být spuštěn verze 5.5, 6.0 nebo 6.5<br/><br/> Hostitelé ESXi by měl spustit verze 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware by měl běžet na hostiteli ESXi.
**Místní virtuální počítače** | Virtuální počítače musí splňovat [požadavky služby Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob spuštění migrace Contoso:

> [!div class="checklist"]
> * **Krok 1: Příprava Azure Site Recovery**: si vytvoří účet Azure storage k ukládání replikovaných dat a trezor služby Recovery Services.
> * **Krok 2: Příprava VMware v místním prostředí pro Site Recovery**: Příprava virtuálního počítače zjišťování a instalaci agenta účty a příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání.
> * **Krok 3: Replikace virtuálních počítačů**: nastavení replikace a spustit replikaci virtuálních počítačů do Azure storage.
> * **Krok 4: Migrace virtuálních počítačů pomocí Site Recovery**: převzetí služeb při selhání testu Ujistěte se, že všechno funguje a potom spusťte úplnou převzetí služeb při selhání k migraci virtuálních počítačů do Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Příprava Azure pro službu Site Recovery

Tady jsou komponenty Azure Contoso je potřeba migrovat virtuální počítače do Azure:

- Virtuální síť, ve kterém budou virtuální počítače umístěné po jejich vytvoření při převzetí služeb při selhání.
- Účet služby Azure storage k ukládání replikovaných dat. 
- Trezor služby Recovery Services v Azure.

Jsou tyto wmm nastavit takto:

1. Contoso již nastavení sítě můžete použít pro Site Recovery při jejich [nasadili infrastrukturu Azure](contoso-migration-infrastructure.md)

    - Aplikace SmartHotel je produkční aplikace a virtuální počítače se budou migrovat do Azure produkční sítě (VNET-PROD-EUS2) v primární oblasti USA – východ 2.
    - Oba virtuální počítače budou umístěny do ContosoRG skupinu prostředků, která se používá pro produkční prostředky.
    - Front-endové aplikace virtuálního počítače (WEBVM) se migrují do front-endové podsítě (PROD-FE – EUS2), v produkční síti.
    - Databáze aplikace virtuálního počítače (SQLVM) se migrují do podsítě databáze (PROD-DB-EUS2), v produkční síti.

2. Contoso se vytvoří účet úložiště Azure (contosovmsacc20180528) v primární oblasti.
    - Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
    - Účet pro obecné účely používají úložiště úrovně standard a replikací LRS. 

    ![Site Recovery úložiště](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Pomocí účtu sítě a úložiště na místě Contoso nyní vytvoří trezor služby Recovery Services (ContosoMigrationVault) a umístí jej ve skupině prostředků ContosoFailoverRG v primární oblasti USA – východ 2.

    ![Trezor služby Recovery Services](./media/contoso-migration-rehost-vm/asr-vault.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Příprava VMware v místním prostředí pro Site Recovery

Tady je co Contoso připraví lokálně:

- Účet vCenter serveru nebo hostiteli vSphere ESXi, pro automatizaci zjišťování virtuálních počítačů.
- Účet, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware. 
- Místní nastavení virtuálního počítače, tak aby Contoso připojit replikované virtuální počítače Azure po převzetí služeb při selhání.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automatické zjišťování virtuálních počítačů. 
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení pro virtuální počítače.
- Vyžaduje se alespoň účet jen pro čtení. Účet by měl být možné spouštět operace, jako je vytváření a odebírání disků a zapnutí na virtuálních počítačích.

Účet contoso nastaví takto:

1. Vytvoří roli na úrovni vCenter.
2. Tato role se přiřadit požadovaná oprávnění.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na každém virtuálním počítači musí být nainstalovaná služba Mobility.

- Po povolení replikace virtuálních počítačů, může Site Recovery poskytnout automatickou nabízenou instalaci služby Mobility.
- Účet je vyžaduje, aby Site Recovery měla přístup k virtuálním počítačům pro nabízenou instalaci. Tento účet zadáte při nastavování replikace.
- Účet může být v doméně nebo místní s oprávněními k instalaci na virtuálních počítačích.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání Contoso chce připojit k virtuálním počítačům Azure. Provedete to tak, že takto před migrací:

1. Pro přístup přes internet jsou:

 - Povolte protokol RDP na místním virtuálním počítači před převzetí služeb při selhání
 - Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **veřejné** profilu.
 - Zkontrolujte, jestli je povolený protokol RDP v **brány Windows Firewall** > **povolené aplikace** pro všechny profily.
 
2. Pro přístup přes síť VPN typu site-to-site jsou:

 - Povolte na místním počítači protokol RDP.
 - Povolit RDP v **brány Windows Firewall** -> **povolené aplikace a funkce**, pro **doménovou a privátní** sítě.
 - Nastavit zásada SAN operačního systému na virtuálním počítači s místními **OnlineAll**.

Kromě toho při spuštění převzetí služeb při selhání potřebují zkontrolujte následující:

- Měla by existovat žádné čekající aktualizace Windows na virtuálním počítači při spuštění převzetí služeb při selhání. Pokud nejsou, nebudou moct přihlásit k virtuálnímu počítači, dokud se nedokončí aktualizace.
- Po převzetí služeb při selhání, můžete zkontrolovat **Diagnostika spouštění** zobrazíte snímek obrazovky virtuálního počítače. Pokud to nepomůže, by měl ověřit, že virtuální počítač spuštěný a najdete v těchto [tipy pro řešení potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) vytvoření a přiřazení role pro automatické zjišťování.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) vytvoření účtu pro nabízenou instalaci služby Mobility.


## <a name="step-3-replicate-the-on-premises-vms"></a>Krok 3: Replikace místních virtuálních počítačů

Před spuštěním migrace do Azure, Contoso je potřeba nastavit a povolit replikaci.

### <a name="set-a-replication-goal"></a>Nastavení cíle replikace

1. V trezoru v části název_trezoru (ContosoVMVault), výběr cíle replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu** .
2. Určí, že jsou jejich počítače nacházejí na místních běžící ve VMware a replikací do Azure.

    ![Cíl replikace](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Chcete-li pokračovat, potvrzení, že nebude dokončena plánování nasazení tak, že vyberete **Ano, mám to hotové**. V tomto scénáři Contoso migrují jenom dva virtuální počítače a nemusíte plánování nasazení.


### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Contoso je potřeba nakonfigurovat jejich zdrojového prostředí. K tomuto účelu, stáhněte šablonu OVF a použít k nasazení Site Recovery konfigurační server jako vysoce dostupný, místní virtuální počítač VMware. Po vytvoření a spuštění je konfigurační server, aby se zaregistrovali v trezoru.

Konfigurační server běží komponent:

- Komponenta konfiguračního serveru, který koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.



Contoso provádí tyto kroky následujícím způsobem:

1. V trezoru, stáhněte šablonu OVF z **připravit infrastrukturu** > **zdroj** > **konfigurační Server**.
    
    ![Stáhněte si OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. Jejich import šablony do VMware. Tím vytvoříte a nasadíte virtuální počítač.

    ![Šablona OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  Při jejich převodu na virtuálním počítači poprvé, se spustí do prostředí instalace Windows serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce. Při prvním přihlášení ve výchozím nastavení spustí nástroj pro konfiguraci Azure Site Recovery.
5. V nástroji určí název zaregistrujte konfigurační server v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání připojení se přihlásit k předplatnému Azure. Přihlašovací údaje musí mít přístup k trezoru, ve kterém se bude registrace konfiguračního serveru.

    ![Registrace konfiguračního serveru](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Přihlášení k počítači znovu, a automaticky spustí Průvodce správou konfiguračního serveru.
9. V průvodci, vyberte síťovou kartu pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení konfigurace.
10. Vyberte předplatné, skupinu prostředků a trezor, do kterého chcete konfigurační server zaregistrovat.
        ![Trezor](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. Stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. 
11. Po ověření, zadejte plně kvalifikovaný název domény nebo IP adresa serveru vCenter nebo vSphere hostiteli. Ponechte výchozí port a zadejte popisný název serveru v Azure.
12. Zadejte účet, který je vytvořil pro automatické zjišťování a přihlašovací údaje, které se používají pro automatickou instalaci služby Mobility. Pro počítače s Windows účet potřebuje oprávnění místního správce na virtuálních počítačích.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Po dokončení registrace na webu Azure Portal, Contoso double ověří, že konfigurační server a VMware server jsou uvedeny na **zdroj** stránky v trezoru. Zjišťování může trvat 15 minut nebo déle. 
8. Site Recovery pak připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavení cíle

Nyní Contoso určuje nastavení cíle replikace.

1. V **připravit infrastrukturu** > **cílové**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a síť v cílové umístění.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Contoso teď můžete vytvořit zásady replikace.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružit**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota cíle bodu obnovení**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí hodnotu 24 hodin. Tato hodnota určuje, jak dlouho je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímků konzistentní vzhledem k**. Výchozí jednu hodinu. Tato hodnota určuje frekvenci, s jakou se vytvořit snímky konzistentní s aplikací.

        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-vm/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružte zásady replikace](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Povolení replikace pro WEBVM

Všechno v místě společnosti Contoso můžete povolit replikaci pro virtuální počítače. Začněte WebVM.

1. V **replikovat aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Indikuje, že chtějí povolit virtuální počítače, vyberte vCenter server a konfigurační server.

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Výběrem nastavení cíle, včetně skupiny prostředků a síť Azure a účet úložiště.

     ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Vybere contoso **WebVM** pro replikaci, ověří zásady replikace a umožňuje replikaci.

    - V této fázi Contoso pouze vybere WEBVM, protože virtuální síť a podsíť musí být vybrána a společnosti Contoso je uvádění aplikací virtuálních počítačů v různých podsítích.
    - Site Recovery automaticky nainstaluje službu Mobility na virtuálním počítači, když je povolená replikace.

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Sledují průběh replikace v **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
6. V **Essentials** Contoso na webu Azure Portal, můžete zobrazit strukturu pro virtuální počítače, které se replikují do Azure.


### <a name="enable-replication-for-sqlvm"></a>Povolení replikace pro SQLVM

Contoso můžete začít replikaci SQLVM počítače, pomocí stejných postupů, jak je uvedeno výše.

1. Výběrem nastavení zdroje.

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Poté určí nastavení cíle.

     ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Vyberou SQLVM pro replikaci. 

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Použití stejných zásad replikace, která byla použita pro WEBVM a povolení replikace.

    ![Zobrazení infrastruktury](./media/contoso-migration-rehost-vm/essentials.png)

**Potřebujete další pomoc?**

- Můžete si přečíst Úplný návod tohoto všechny tyto kroky v [nastavit zotavení po havárii pro virtuální počítače VMware v místním](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici při [zdrojové prostředí nastavíte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfiguračního serveru](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [konfigurace nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Další informace o [povolení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Krok 4: Migrace virtuálních počítačů 

Contoso spustí rychlé testovací převzetí služeb při selhání a poté úplná převzetí služeb při selhání k migraci virtuálních počítačů.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Testovací převzetí služeb pomáhá zajistit, že vše funguje podle očekávání. 

1. Contoso spustí testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovaný**).
2. Vyberou **před spuštěním převzetí služeb při selhání vypnout počítač**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před aktivací převzetí služeb. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Spuštění testovacího převzetí služeb při selhání: 

    - Kontrola spuštění, abyste měli jistotu, že jsou všechny podmínky pro migraci na místě požadavků.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.
    
3. Po dokončení převzetí služeb se zobrazí na webu Azure Portal repliku virtuálního počítače Azure. Contoso kontroluje, zda virtuální počítač odpovídající velikost, připojený ke správné síti a běží. 
4. Po ověření testu převzetí služeb, jejich vyčištění převzetí služeb při selhání a zaznamenejte a uložte jakékoli připomínky. 

### <a name="create-and-customize-a-recovery-plan"></a>Vytvořit a přizpůsobit plán obnovení

 Po ověření, zda testovací převzetí služeb fungovala podle očekávání, vytvoří Contoso plán obnovení pro migraci. 

- Plán obnovení určuje pořadí, ve které převzetí služeb při selhání dojde k a určuje, jak se virtuální počítače Azure do online režimu v Azure.
- Protože aplikace je dvouvrstvá, jejich přizpůsobit plán obnovení tak, aby data virtuálního počítače (SQLVM) spustí před front-endu (WEBVM).

1. V **plány obnovení (služba Site Recovery)** > **+ plánu obnovení**, vytvoření plánu a přidejte do ní virtuální počítače.

    ![Plán obnovení](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Po vytvoření plánu, že ji přizpůsobit (**plány obnovení** > **SmartHotelMigrationPlan** > **vlastní**).
2.  Odstraňují WEBVM z **skupiny 1: Start**.  Tím se zajistí, že první akci spuštění ovlivňuje pouze SQLVM.
3.  V **+ skupina** > **přidat chráněné položky**, WEBVM přidávají do 2. skupina: Start.  Virtuální počítače musí být ve dvou různých skupin.


### <a name="migrate-the-vms"></a>Migrace virtuálních počítačů


Contoso nyní můžete spustit úplné převzetí služeb při selhání k dokončení migrace.

1. Vyberou plánu obnovení > **převzetí služeb při selhání**.
2. Výběru se převzetí služeb při selhání do nejnovějšího bodu obnovení a obnovení lokality se měli pokusit vypněte místní virtuální počítač před aktivací převzetí služeb. Průběh převzetí služeb při selhání, můžete postupovat podle **úlohy** stránky.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm/failover1.png)


3. Po převzetí služeb při selhání ověřují, že se virtuální počítač Azure zobrazuje podle očekávání na webu Azure Portal.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm/failover2.png)  

3. Po ověření že dokončení migrace pro každý virtuální počítač. Tím se zastaví replikace virtuálního počítače a zastaví se fakturace služby Site Recovery pro něj.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm/failover3.png)

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštění testovací převzetí služeb při selhání. 
- [Přečtěte si](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) převzetí služeb při selhání do Azure.

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Pomocí migrace byla dokončena aplikačních vrstvách SmartHotel běží na virtuálních počítačích Azure.

Nyní Contoso potřebuje k dokončení těchto kroků vyčištění:  

- Odebrání počítače WEBVM z inventáře vCenter.
- Odeberte počítač SQLVM z inventáře vCenter.
- Odeberte WEBVM a SQLVM z místní úlohy zálohování.
- Aktualizujte interní dokumentaci zobrazíte nové umístění a IP adresy pro virtuální počítače.
- Zkontrolujte všechny prostředky, které pracují s virtuálními počítači a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

V aplikaci aktuálně spuštěno Contoso teď je potřeba plně zprovoznění a zabezpečení v Azure.

### <a name="security"></a>Zabezpečení

Bezpečnostní tým Contoso kontroly virtuální počítače Azure, chcete-li zjistit všechny problémy se zabezpečením.

- Řízení přístupu, najdete v tématu skupiny zabezpečení sítě (Nsg) pro virtuální počítače. Skupiny zabezpečení sítě se používají k zajištění, že pouze provoz povolený do aplikace k němu přistoupit.
- Také považují za zabezpečení dat na disku pomocí Azure Disk Encryption a trezor klíčů.

[Přečtěte si další](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupech zabezpečení pro virtuální počítače.

### <a name="backups"></a>Zálohování

Contoso se bude zálohovat data na virtuálních počítačích pomocí služby Azure Backup. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

1. Contoso má existující licencování pro své virtuální počítače a bude využít zvýhodněné hybridní využití Azure.  Bude převodu existujících virtuálních počítačů Azure, abyste mohli využívat Tento cenový model.
2. Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, která umožňuje využívat a spravovat Azure a další cloudové prostředky. [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management. 

## <a name="conclusion"></a>Závěr

V tomto článku Contoso rehosted SmartHotel aplikace v Azure díky migraci aplikace, virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. 


## <a name="next-steps"></a>Další postup

V [dalšímu článku](contoso-migration-rehost-vm-sql-ag.md) v této sérii, vám ukážeme, jak společnosti Contoso ke kolizi SmartHotel aplikace front-endový virtuální počítač na Virtuálním počítači Azure a migraci databáze do skupiny dostupnosti AlwaysOn serveru SQL v Azure.

