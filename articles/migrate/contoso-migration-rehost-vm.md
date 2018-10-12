---
title: Změna hostitele aplikace Contoso s migrací na virtuální počítače Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak metody opětovného hostování místní aplikaci pomocí lift and shift migrace místních počítačů do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 004ce399437ef581b319980c83f00cb1427b934b
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115834"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migrace Contoso: Změna hostitele místní aplikaci do virtuálních počítačů Azure


Tento článek ukazuje, jak společnosti Contoso ke kolizi místní aplikace SmartHotel360 v Azure, díky migraci aplikace, virtuální počítače na virtuální počítače Azure.


Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které znázorňují nastavování infrastruktury migrace, posuzování migrace místních prostředků a spouštění různé druhy migrace. Scénáře jejich složitost v. Přidáme další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Přehled řady článku, strategie migrace společnosti Contoso a ukázkové aplikace, které se používají v řadě. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články týkající se migrace z této série se používá stejnou infrastrukturu. | K dispozici.
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md)  | Contoso spuštění posouzení své místní aplikace SmartHotel360 běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí služby Azure Migrate a databáze aplikace SQL Server pomocí Data Migration Assistant. | K dispozici.
[Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso běží lift and shift migrace do Azure pro své místní aplikace SmartHotel360. Contoso migruje aplikace front-endového virtuálního počítače pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migraci databáze aplikace do Azure SQL Database Managed Instance pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.   
Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure | Contoso migruje jeho aplikace SmartHotel360 virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | V tomto článku
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikace SmartHotel360. Společnost Contoso využívá Site Recovery k migraci virtuálních počítačů aplikace. Jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn využívá službu Database Migration Service. | K dispozici. 
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Contoso dokončení migrace lift and shift aplikace osTicket Linux do virtuálních počítačů Azure pomocí Azure Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Azure Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Contoso migruje aplikace SmartHotel360 do webové aplikace Azure a migraci databáze aplikace do instance serveru SQL Azure pomocí Pomocníka s migrací databáze | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci na Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje osTicket své Linuxové aplikace do webové aplikace Azure v několika oblastech Azure pomocí Azure Traffic Manageru, integrovaná se službou GitHub pro průběžné doručování. Contoso migraci databáze aplikace do Azure Database for MySQL – instance. | K dispozici. 
[Článek 11: Refaktorovat TFS na službách Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migrovat své místní nasazení serveru Team Foundation Server ke službám Azure DevOps v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jeho SmartHotel aplikace do Azure. Potom rearchitects webové vrstvy aplikace jako kontejner Windows se spuštěnou v Azure Service Fabric a databázi Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB. | K dispozici.
[Článek 14: Škálování migrace do Azure](contoso-migration-scale.md) | Po vyzkoušení kombinace migrace, Contoso připraví škálovat na plné migraci do Azure. | K dispozici.


V tomto článku se bude migrovat Contoso dvouvrstvé Windows. NET SmartHotel360 aplikaci spuštěnou v prostředí virtuálních počítačů VMware do Azure. Pokud chcete používat tuto aplikaci, se poskytuje jako open source a můžete ji stáhnout [githubu](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzké spolupráci s obchodními partnery pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso roste a kvůli tomu je tlak na svých místních systémů a infrastruktury.
- **Omezit riziko**: The SmartHotel360 aplikace je velmi důležité pro firmy Contoso. Chce přesunout aplikace do Azure s nulovou rizika.
- **Rozšíření**: Contoso nechce upravit aplikaci, ale chce zajistit, že se jedná o stabilní.


## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci. Tyto cíle se používají k určení nejlepší metody migrace:

- Po dokončení migrace aplikace v Azure by měla mít stejné možnosti výkonu, stejně jako dnes ve službě VMware.  Aplikace zůstanou jako kritické v cloudu je místní. 
- Contoso nechce investovat do této aplikace.  Je důležité pro podnikání, ale v aktuální podobě Contoso jednoduše chce, aby se bezpečně přesunout do cloudu.
- Chcete-li změnit model ops pro tuto aplikaci nechce contoso. Contoso má s ním můžete pracovat v cloudu stejným způsobem, který teď dělají.
- Chcete-li změnit všechny funkce aplikace nechce contoso. Se změní pouze umístění aplikace.


## <a name="solution-design"></a>Návrh řešení

Připnutí zalomení dolů cíle a požadavky, návrhy společnosti Contoso, zkontrolujte nasazení řešení a identifikuje proces migrace, včetně služeb Azure, které Contoso použije pro migraci.

### <a name="current-app"></a>Aktuální aplikaci

- Aplikace je rozvrstvená na dva virtuální počítače (**WEBVM** a **SQLVM**).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5).
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).

### <a name="proposed-architecture"></a>Navrhované architektury

- Protože se aplikace a produkční úlohy, aplikace virtuální počítače v Azure budou umístěné ve skupině prostředků produkční ContosoRG.
- Aplikace virtuální počítače budou migrovat do primární oblasti Azure (východní USA 2) a umístí do produkční sítě (VNET-PROD-EUS2).
- Webový front-end virtuálního počítače se bude nacházet ve front-endové podsíti (PROD-FE – EUS2) v podnikové síti.
- Databáze, virtuální počítač se bude nacházet v podsíti (PROD-DB-EUS2) databáze v produkční síti.
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.

![Architektura scénáře](./media/contoso-migration-rehost-vm/architecture.png) 

### <a name="database-considerations"></a>Důležité informace o databázi

Jako součást procesu návrhu řešení nebyla Contoso porovnání funkcí mezi službou Azure SQL Database a SQL Server. Následující důležité informace o tom se rozhodnout pro SQL Server běžící na Virtuálním počítači Azure IaaS: 

- Použití virtuálního počítače Azure s SQL serverem se zdá být optimální řešení, pokud Contoso potřebuje k přizpůsobení operačního systému nebo databázový server, nebo může být vhodné umístit společně a spouštění aplikací třetích stran na jednom virtuálním počítači.
- S programem Software Assurance v budoucnu Contoso si mohou vyměňovat stávající licence pro zlevněné sazby SQL Database Managed Instance pomocí zvýhodněné hybridní využití Azure pro SQL Server. To můžete uložit až 30 % na Managed Instance.



### <a name="solution-review"></a>Kontrola řešení

Contoso vyhodnotí navrhovaných návrhu společně vložením seznam výhody a nevýhody.

**Posouzení** | **Podrobnosti**
--- | ---
**V oblasti IT** | Virtuální počítače s aplikace se přesunou do Azure beze změn, provedení migrace jednoduché.<br/><br/> Contoso používá lift and shift pro oba virtuální počítače s aplikací, nejsou potřeba žádné speciální konfigurace nebo migrace nástroje pro databázi aplikace.<br/><br/> Contoso můžou využívat své investice do Software Assurance, pomocí zvýhodněné hybridní využití Azure.<br/><br/> Contoso se zachovat plnou kontrolu nad ní virtuální počítače v Azure. 
**Nevýhody** | WEBVM a SQLVM se systémem Windows Server 2008 R2. Operační systém se nepodporuje v Azure pro konkrétní role (července 2018). [Další informace](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> Web a datovou vrstvu aplikace zůstanou jediný bod převzetí služeb při selhání.</br><br/> SQLVM se systémem SQL Server 2008 R2, který není v hlavní fáze technické podpory. Je však podporován pro virtuální počítače Azure (července 2018). [Další informace](https://support.microsoft.com/en-us/help/956893).<br/><br/> Contoso se potřebujete i nadále podporuje aplikace jako virtuální počítače Azure, spíše než Přesun do spravované služby, jako je například Azure App Service a Azure SQL Database.



### <a name="migration-process"></a>Proces migrace

Contoso se migrovat front-endu aplikace a databáze, virtuální počítače na virtuální počítače Azure pomocí Site Recovery:

- Jako první krok Contoso připraví a nastaví komponenty Azure Site Recovery a připraví na místní infrastrukturu VMware.
- Už máte [infrastruktury Azure](contoso-migration-infrastructure.md) na místě, proto Contoso stejně musí přidat několik komponent Azure speciálně pro Site Recovery.
- Všechno připravené můžete začít Contoso replikaci virtuálních počítačů.
- Po povolení replikace a práci, Contoso provede migraci virtuálního počítače pomocí převzetí služeb při selhání ho do Azure.

![Proces migrace](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migraci a zotavení po havárii pro virtuální počítače Azure a místních virtuálních počítačů a fyzických serverů.  | Při replikaci do Azure se účtují poplatky za úložiště Azure.  Virtuální počítače Azure jsou vytvářeny a účtovat poplatky, pokud dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) nákladů a ceny.


## <a name="prerequisites"></a>Požadavky

Zde je, co Contoso potřebuje ke spuštění tohoto scénáře.

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Vytvořit odběry v dřívějším článku v této sérii contoso. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.<br/><br/> Další informace o konkrétních [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) požadavky pro Site Recovery.
**Místní servery** | Pro místní servery vCenter server by měl být spuštěn verze 5.5, 6.0 nebo 6.5<br/><br/> Hostitelé ESXi by měl spustit verze 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware by měl běžet na hostiteli ESXi.
**Místní virtuální počítače** | Virtuální počítače musí splňovat [požadavky služby Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob spuštění migrace správce společnosti Contoso:

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

1. Nastavení sítě Contoso již nastavení sítě, které mohou být pro Site Recovery při jejich [nasadili infrastrukturu Azure](contoso-migration-infrastructure.md)

    - Aplikace SmartHotel360 je výrobní aplikace a virtuální počítače se budou migrovat do Azure produkční sítě (VNET-PROD-EUS2) v primární oblasti USA – východ 2.
    - Oba virtuální počítače budou umístěny do ContosoRG skupinu prostředků, která se používá pro produkční prostředky.
    - Front-endové aplikace virtuálního počítače (WEBVM) se migrují do front-endové podsítě (PROD-FE – EUS2), v produkční síti.
    - Databáze aplikace virtuálního počítače (SQLVM) se migrují do podsítě databáze (PROD-DB-EUS2), v produkční síti.

2. Vytvořte účet úložiště – Contoso vytvoří účet úložiště Azure (contosovmsacc20180528) v primární oblasti.
    - Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
    - Účet pro obecné účely používají úložiště úrovně standard a replikací LRS. 

    ![Site Recovery úložiště](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Vytvoření trezoru pomocí účtu sítě a úložiště na místě, Contoso nyní vytvoří trezor služby Recovery Services (ContosoMigrationVault) a umístí jej do skupiny prostředků ContosoFailoverRG v primární oblasti USA – východ 2.

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

Účet správce společnosti Contoso nastavte následujícím způsobem:

1. Vytvoří roli na úrovni vCenter.
2. Tato role se přiřadit požadovaná oprávnění.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na každém virtuálním počítači musí být nainstalovaná služba Mobility.

- Po povolení replikace virtuálních počítačů, může Site Recovery poskytnout automatickou nabízenou instalaci služby Mobility.
- Účet je vyžaduje, aby Site Recovery měla přístup k virtuálním počítačům pro nabízenou instalaci. Tento účet zadáte při nastavování replikace.
- Účet může být v doméně nebo místní s oprávněními k instalaci na virtuálních počítačích.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání Contoso chce připojit k virtuálním počítačům Azure. K tomuto účelu správce společnosti Contoso takto před migrací:

1. Pro přístup přes internet jsou:

 - Povolte protokol RDP na místním virtuálním počítači před převzetí služeb při selhání.
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

Správce společnosti Contoso mohli spustit migraci do Azure, musí nastavit a povolit replikaci.

### <a name="set-a-replication-goal"></a>Nastavení cíle replikace

1. V trezoru v části název_trezoru (ContosoVMVault), výběr cíle replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu** .
2. Určí, že jsou jejich počítače nacházejí na místních běžící ve VMware a replikací do Azure.

    ![Cíl replikace](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Chcete-li pokračovat, potvrzení, že nebude dokončena plánování nasazení tak, že vyberete **Ano, mám to hotové**. V tomto scénáři Contoso migrují jenom dva virtuální počítače a nemusíte plánování nasazení.


### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Správce společnosti Contoso je nutné konfigurovat zdrojové prostředí. K tomuto účelu, stáhněte šablonu OVF a použít k nasazení Site Recovery konfigurační server jako vysoce dostupný, místní virtuální počítač VMware. Po vytvoření a spuštění je konfigurační server, aby se zaregistrovali v trezoru.

Konfigurační server běží komponent:

- Komponenta konfiguračního serveru, který koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.



Správce společnosti Contoso následujícím způsobem proveďte tyto kroky:

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

7. Po dokončení registrace na webu Azure Portal, double, zkontrolujte, že konfigurační server a VMware server jsou uvedeny na **zdroj** stránky v trezoru. Zjišťování může trvat 15 minut nebo déle. 
8. Site Recovery pak připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavení cíle

Správce společnosti Contoso nyní zadáte nastavení replikace cíl.

1. V **připravit infrastrukturu** > **cílové**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a síť v cílové umístění.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Správce společnosti Contoso teď můžete vytvořit zásady replikace.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružit**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota cíle bodu obnovení**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí hodnotu 24 hodin. Tato hodnota určuje, jak dlouho je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímků konzistentní vzhledem k**. Výchozí jednu hodinu. Tato hodnota určuje frekvenci, s jakou se vytvořit snímky konzistentní s aplikací.

        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-vm/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružte zásady replikace](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Povolení replikace pro WEBVM

Všechno v místě správce společnosti Contoso teď můžete povolit replikaci pro virtuální počítače. Začněte WebVM.

1. V **replikovat aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Indikuje, že chtějí povolit virtuální počítače, vyberte vCenter server a konfigurační server.

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Výběrem nastavení cíle, včetně skupiny prostředků a síť Azure a účet úložiště.

     ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Vyberou **WebVM** pro replikaci, zkontrolovat zásady replikace a povolení replikace.

    - V této fázi se vybere pouze WEBVM protože musí být vybraná virtuální síť a podsíť, a aplikace, virtuální počítače budou umístěny v různých podsítích.
    - Site Recovery automaticky nainstaluje službu Mobility na virtuálním počítači, když je povolená replikace.

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Sledují průběh replikace v **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
6. V **Essentials** na webu Azure Portal, můžete zobrazit strukturu pro virtuální počítače, které se replikují do Azure.


### <a name="enable-replication-for-sqlvm"></a>Povolení replikace pro SQLVM

Správce společnosti Contoso můžete začít replikaci SQLVM počítače, pomocí stejných postupů, jak je uvedeno výše.

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

Správce společnosti Contoso spusťte rychlé testovací převzetí služeb a pak úplné převzetí služeb při selhání k migraci virtuálních počítačů.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Testovací převzetí služeb pomáhá zajistit, že vše funguje podle očekávání. 

1. Spustit testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovaný**).
2. Vyberou **před spuštěním převzetí služeb při selhání vypnout počítač**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před aktivací převzetí služeb. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Spuštění testovacího převzetí služeb při selhání: 

    - Kontrola spuštění, abyste měli jistotu, že jsou všechny podmínky pro migraci na místě požadavků.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.
    
3. Po dokončení převzetí služeb se zobrazí na webu Azure Portal repliku virtuálního počítače Azure. Zkontrolujte, zda virtuální počítač odpovídající velikost, připojený ke správné síti a běží. 
4. Po ověření testu převzetí služeb, jejich vyčištění převzetí služeb při selhání a zaznamenejte a uložte jakékoli připomínky. 

### <a name="create-and-customize-a-recovery-plan"></a>Vytvořit a přizpůsobit plán obnovení

 Po ověření, zda testovací převzetí služeb fungovala podle očekávání, správce společnosti Contoso vytvořit plán obnovení pro migraci. 

- Plán obnovení určuje pořadí, ve které převzetí služeb při selhání dojde k a určuje, jak se virtuální počítače Azure do online režimu v Azure.
- Protože aplikace je dvouvrstvá, jejich přizpůsobit plán obnovení tak, aby data virtuálního počítače (SQLVM) spustí před front-endu (WEBVM).

1. V **plány obnovení (služba Site Recovery)** > **+ plánu obnovení**, vytvoření plánu a přidejte do ní virtuální počítače.

    ![Plán obnovení](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Po vytvoření plánu, že ji přizpůsobit (**plány obnovení** > **SmartHotelMigrationPlan** > **vlastní**).
2.  Odstraňují WEBVM z **skupiny 1: Start**.  Tím se zajistí, že první akci spuštění ovlivňuje pouze SQLVM.
3.  V **+ skupina** > **přidat chráněné položky**, WEBVM přidávají do 2. skupina: Start.  Virtuální počítače musí být ve dvou různých skupin.


### <a name="migrate-the-vms"></a>Migrace virtuálních počítačů


Nyní spusťte Správce společnosti Contoso úplné převzetí služeb při selhání k dokončení migrace.

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

Pomocí migrace byla dokončena vrstvy aplikace SmartHotel360 běží na virtuálních počítačích Azure.

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

- K řízení přístupu, zkontroluje tým skupiny zabezpečení sítě (Nsg) pro virtuální počítače. Skupiny zabezpečení sítě se používají k zajištění, že pouze provoz povolený do aplikace k němu přistoupit.
- Tým také zvážit zabezpečení dat na disku pomocí Azure Disk Encryption a trezor klíčů.

[Přečtěte si další](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupech zabezpečení pro virtuální počítače.

## <a name="bcdr"></a>BCDR

Společnosti Contoso pro provozní kontinuitu a zotavení po havárii (BCDR), provede následující akce:

- Byla data v bezpečí: Contoso zálohuje data na virtuálních počítačích pomocí služby Azure Backup. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Udržujte aplikace rychle zprovoznit: Contoso replikuje aplikace virtuální počítače v Azure do sekundární oblasti vzdálené používání služby Site Recovery. [Další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).



### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

1. Contoso má existující licencování pro své virtuální počítače a bude využít zvýhodněné hybridní využití Azure.  Contoso se převést stávající virtuální počítače Azure, abyste mohli využívat Tento cenový model.
2. Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, která umožňuje využívat a spravovat Azure a další cloudové prostředky. [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management. 

## <a name="conclusion"></a>Závěr

V tomto článku Contoso rehosted SmartHotel360 aplikace v Azure díky migraci aplikace, virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. 


## <a name="next-steps"></a>Další postup

V [dalšímu článku](contoso-migration-rehost-vm-sql-ag.md) v této sérii, vám ukážeme, jak společnosti Contoso ke kolizi SmartHotel360 aplikace front-endový virtuální počítač na Virtuálním počítači Azure a migraci databáze do skupiny dostupnosti AlwaysOn serveru SQL v Azure.

