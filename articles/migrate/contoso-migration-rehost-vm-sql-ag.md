---
title: Změna hostitele aplikace Contoso a migrujte je do skupiny dostupnosti virtuálních počítačů Azure a SQL Server AlwaysOn | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso opětovným hostováním místní aplikaci a migrujte je do skupiny dostupnosti virtuálních počítačů Azure a SQL Server AlwaysOn
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: cd7a5832faf0fbb15349edee8ed504c1f94d1aa9
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42054012"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-sql-server-alwayson-availability-group"></a>Migrace Contoso: Změna hostitele aplikace v místním na virtuálních počítačích Azure a skupiny dostupnosti AlwaysOn SQL serveru

Tento článek ukazuje, jak společnosti Contoso ke kolizi SmartHotel aplikace v Azure. Na Virtuálním počítači Azure a databáze aplikace Azure SQL Server VM spuštěného v clusteru převzetí služeb při selhání Windows serveru s gGroups dostupnosti AlwaysOn pro SQL Server, migrovat aplikace front-endový virtuální počítač.

Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které znázorňují nastavování infrastruktury migrace, posuzování migrace místních prostředků a spouštění různé druhy migrace. Scénáře jejich složitost v a přidáme další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny články týkající se migrace. | K dispozici.
[Článek 3: Posouzení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak společnosti Contoso spuštění posouzení místních dvouvrstvé SmartHotel aplikaci spuštěnou v prostředí VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Pomocníka s migrací databáze](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Změna hostitele aplikací na virtuálních počítačích Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso běží lift and shift migrace do Azure pro aplikaci SmartHotel. Contoso migruje virtuální počítač front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace do spravované Instance SQL, pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak migrovat aplikace SmartHotel virtuálních počítačů pomocí Site Recovery pouze Contoso. | K dispozici.
Článek 6: Změna hostitele aplikací na virtuálních počítačích Azure a skupina dostupnosti AlwaysOn SQL serveru | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace. Contoso využívá Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | V tomto článku
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso funguje lift and shift migrace aplikace osTicket Linux na virtuální počítače Azure pomocí služby Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure serveru MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci na Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje aplikace osTicket Linux do Azure Web Apps ve více lokalitách, integrovaná se službou GitHub pro průběžné doručování. Jejich migrovat databázi aplikace na instanci Azure MySQL. | K dispozici.
[Článek 11: Refaktorovat TFS na VSTS](contoso-migration-tfs-vsts.md) | Ukazuje, jak společnosti Contoso migruje svoje místní nasazení Team Foundation Server (TFS) a migrujte jej na Visual Studio Team Services (VSTS) v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci do Azure](contoso-migration-rebuild.md) | Ukazuje, jak společnosti Contoso znovu sestavit aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | K dispozici.



V tomto článku se migrovat Contoso dvouvrstvé aplikace Windows .NET SmartHotel běžící na virtuálních počítačích VMware do Azure. Pokud chcete používat tuto aplikaci, se poskytuje jako open source a můžete ji stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso roste a kvůli tomu je tlak na svých místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso musí odebrat nepotřebné postupy a zjednodušte procesy pro vývojáře a uživatele.  Obchodní potřeby IT bude rychlé a ne produkovaného odpadu čas nebo peníze, tím rychlejší doručování na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku.  Nesmí získat způsobem nebo stát obchodní blokování.
- **Škálování**: růstem podniku zvládla úspěšně, musíte zadat Contoso IT systémy, které mohou rozšířit stejným tempem.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci. Tyto cíle se používaly pro určení nejlepší metody migrace:

- Po dokončení migrace aplikace v Azure by měla mít stejné možnosti výkonu, stejně jako dnes ve službě VMWare.  Aplikace zůstanou jako kritické v cloudu je místní.
- Contoso nechce investovat do této aplikace.  Je důležité pro podnikání, ale v aktuální podobě chtějí jednoduše bezpečně přesunout do cloudu.
- Místní databáze aplikace měla problémy s dostupností. Jejich byste si rádi, že nasazené v Azure jako cluster s vysokou dostupnost, možnosti převzetí služeb při selhání.
- Contoso chce upgradovat z jejich aktuální platformě SQL Server 2008 R2, na SQL serveru 2017.
- Contoso nechce používat Azure SQL Database pro tuto aplikaci a hledá alternativy.

## <a name="proposed-architecture"></a>Navrhované architektury

V tomto scénáři:

- Aplikace je rozvrstvená na dva virtuální počítače (WEBVM a SQLVM).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.

![Architektura scénáře](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Služba Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | DMS umožňuje bezproblémovou migraci z několika databázových zdrojů na platformu Azure data, s minimálními výpadky. | Další informace o [podporované oblasti](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) DMS a get [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Site Recovery orchestruje a spravuje migraci a zotavení po havárii pro virtuální počítače Azure a místních virtuálních počítačů a fyzických serverů.  | Při replikaci do Azure se účtují poplatky za úložiště Azure.  Virtuální počítače Azure jsou vytvářeny a účtovat poplatky, pokud dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) nákladů a ceny.

 

## <a name="migration-process"></a>Proces migrace

- Contoso se migrace aplikací virtuálních počítačů do Azure.
- Front-endový virtuální počítač, budete migrovat do virtuálního počítače Azure pomocí Site Recovery:
    - Jako první krok se připraví a nastavíte komponenty Azure a příprava na místní infrastrukturu VMware.
    - Všechno připravené můžou spustit replikaci virtuálního počítače.
    - Po povolení replikace a práci, migraci virtuálního počítače pomocí převzetí služeb při selhání ho do Azure.
- Databáze, provedeme migraci na cluster systému SQL Server v Azure pomocí služby Data Migration Service (DMS).
    - Jako první krok, budete potřebovat ke zřízení virtuální počítače s SQL serverem v Azure, nastavení clusteru a interního nástroje load balancer a konfigurace skupin dostupnosti AlwaysOn.
    - Díky tomu na místě, můžete migrovat databáze
- Po dokončení migrace umožňují ochranu AlwaysOn pro databázi.

![Proces migrace](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Požadavky

Zde je můžete vy (a Contoso) potřebovat ke spuštění tohoto scénáře:

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste už vytvořili odběr při provádění posouzení v první článku v této sérii. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.<br/><br/> Další informace o konkrétních [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) požadavky pro Site Recovery.
**Site recovery (v místním prostředí)** | Vaše místní vCenter server by měl být spuštěn verze 5.5, 6.0 nebo 6.5<br/><br/> Hostitele ESXi verze 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware běží na hostiteli ESXi.<br/><br/> Virtuální počítače musí splňovat [požadavky služby Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Podporované [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurace.<br/><br/> Virtuální počítače, které chcete replikovat, musí splňovat [požadavky služby Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).
**DMS** | Pro systém DMS, je nutné [kompatibilní na místním zařízení VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Musíte být schopni konfigurovat místní zařízení VPN. Musí mít veřejnou adresu IPv4 přístupnou zvenčí a adresa nesmí být umístěné za zařízením NAT a.<br/><br/> Ujistěte se, že budete mít přístup k místní databázi SQL serveru.<br/><br/> Brána Windows Firewall by měla moct přistupovat k source databázového stroje. [Další informace](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Pokud je brána firewall před počítači databáze, přidejte pravidla pro povolení přístupu k databázi a soubory prostřednictvím SMB portu 445.<br/><br/> Pověření používaná k připojení ke zdrojovému serveru SQL a cílit na Managed Instance musí být členy role serveru sysadmin.<br/><br/> Budete potřebovat síť sdílet v místní databázi, DMS můžete použít k zálohování zdrojové databáze.<br/><br/> Ujistěte se, zda má účet služby spuštění zdrojové instanci SQL serveru oprávnění k zápisu ve sdílené síťové složce.<br/><br/> Poznamenejte Windows uživatele (a heslo), který má oprávnění Úplné řízení na sdílené síťové složce. Azure Database Migration Service zosobňuje tyto přihlašovací údaje uživatele pro nahrání záložních souborů do kontejneru úložiště Azure.<br/><br/> Proces instalace systému SQL Server Express nastaví protokolu TCP/IP na **zakázané** ve výchozím nastavení. Ujistěte se, zda je povoleno.


## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob spuštění migrace Contoso:

> [!div class="checklist"]
> * **Krok 1: Vytvoření virtuálních počítačů SQL serverem v Azure**: pro zajištění vysoké dostupnosti, Contoso chcete nasadit clusterované databáze v Azure. Nasadí dva virtuální počítače SQL serveru a Azure interního nástroje load balancer.
> * **Krok 2: Nasazení clusteru**: po nasazení virtuální počítače s SQL serverem, přípravě clusteru služby Azure SQL Server.  Do této předem vytvořený cluster, provedeme migraci jejich databáze.
> * **Krok 3: Příprava DMS**: Příprava DMS, aby se zaregistrovali poskytovateli migrace databáze, vytvořte instanci DMS a projekt. Nastavené sdíleného přístupového podpisu (SAS) identifikátor URI (Uniform Resource). DMS používá identifikátor URI SAS pro přístup ke kontejneru účtu úložiště, do kterého služba nahraje soubory zálohování systému SQL Server.
> * **Krok 4: Příprava Azure Site Recovery**: si vytvoří účet Azure storage k ukládání replikovaných dat a trezor služby Recovery Services.
> * **Krok 5: Příprava VMware v místním prostředí pro Site Recovery**: Příprava virtuálního počítače zjišťování a instalaci agenta účty a příprava místních virtuálních počítačů tak, aby se mohly připojit k virtuálním počítačům Azure po převzetí služeb při selhání.
> * **Krok 6: Replikace virtuálních počítačů**: Konfigurace nastavení replikace a povolení replikace virtuálních počítačů.
> * **Krok 7: Migrace databáze pomocí DMS**: jejich migrace databáze.
> * **Krok 8: Migrace virtuálních počítačů pomocí Site Recovery**: jejich prvním spuštění převzetí služeb při selhání testu Ujistěte se, že všechno funguje, za nímž následuje úplné převzetí služeb při selhání k migraci virtuálního počítače.


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Krok 1: Příprava clusteru skupiny dostupnosti AlwaysOn systému SQL Server

Contoso chce Plánujte dopředu nasazením databáze v clusteru v Azure. Tento cluster ten pak může použít pro ostatní databáze. 

- Ve skupině prostředků ContosoRG (používá se pro produkční prostředky) se nasadí virtuální počítače SQL serveru.
- Kromě jedinečné názvy oba virtuální počítače používat stejné nastavení.
- Interní služby load balancer se nasadí v ContosoNetworkingRG (používá se pro síťové prostředky).
- Virtuální počítače se spustí Windows serveru 2016 s SQL serverem 2017 Enterprise Edition. Contoso nemá licence pro tento operační systém, takže budou používat image v Tržišti Azure Marketplace, který poskytuje licenci jako náklady na jejich počáteční závazek EA na Azure.

Tady je nastavení, které Contoso clusteru:

1. Vytvoří dva virtuální počítače SQL serveru tak, že vyberete image SQL serveru 2017 Enterprise systému Windows Server 2016 na webu Azure Marketplace. 

    ![SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. V **vytvoření virtuálního počítače průvodce** > **Základy**, konfigurace:

    - Názvy virtuálních počítačů: **SQLAOG1** a **SQLAOG2**.
    - Protože jsou důležité obchodní informace počítače, umožňují SSD pro typ disku virtuálního počítače.
    - Určí pověření počítače.
    - Nasadí virtuální počítače v primární oblasti východní USA 2 oblasti, ve skupině prostředků ContosoRG.

3. V **velikost**, začínají D2s_V3 SKU pro oba virtuální počítače. Umožní škálování později, jak potřebují.
4. V **nastavení**, postupujte takto:

    - Protože tyto virtuální počítače jsou databáze nezbytné pro aplikaci, používají spravované disky.
    - Umístěte na počítače v podnikové síti USA – VÝCHOD 2 primární oblasti (**připojení typu VNET-PROD-EUS2**), v databázi podsítě (**PROD-DB-EUS2**).
    - Vytváření nové skupiny dostupnosti: **SQLAOGAVSET**s dvěma doménami selhání a pět aktualizačních domén.

    ![VIRTUÁLNÍ POČÍTAČ S SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. V **nastavení systému SQL Server**, omezují SQL připojení k virtuální síti (privátní) na výchozím portu 1433. K ověřování používají stejné přihlašovací údaje, které používají místní (**contosoadmin**).

    ![VIRTUÁLNÍ POČÍTAČ S SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Potřebujete další pomoc?**

- [Získejte pomoc](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) zřizování virtuálního počítače s SQL serverem.
- [Další informace o](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) konfigurace virtuálních počítačů pro různá SKU SQL serveru.

## <a name="step-2-deploy-the-failover-cluster"></a>Krok 2: Nasazení clusteru převzetí služeb při selhání

Tady je nastavení, které Contoso clusteru:

1. Účet služby Azure storage nastavené tak, aby fungoval jako disk s kopií cloudu.
2. Přidávají virtuální počítače SQL serveru k doméně služby Active Directory v místním datovém centru společnosti Contoso.
3. Vytvoří cluster v Azure.
4. Konfiguraci cloudové kopie clusteru.
5. A konečně povolte skupiny dostupnosti AlwaysOn SQL.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Nastavení účtu úložiště jako disk s kopií cloudu

Pokud chcete nastavit cloudové kopie clusteru, Contoso potřebuje účet služby Azure Storage, který bude obsahovat soubor objektu blob pro cluster ustanovení o rozhodčím řízení. Stejný účet úložiště je možné nastavit cloudovou kopii clusteru pro několik clusterů. 

Contoso vytvoří účet úložiště následujícím způsobem:

1. Určí zapamatovatelný název pro účet (**contosocloudwitness**).
2. Nasadí obecné Všestranná účet, při použití úložiště LRS.
3. Účet, umístěte do třetí oblast – střed USA – jih. Umístěte ho mimo oblast primárního a sekundárního tak, aby zůstal dostupný v případě regionálních selhání.
4. Umístěte ji ve své skupině prostředků, který obsahuje prostředky infrastruktury – **ContosoInfraRG**.

    ![Disk s kopií cloudu](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Při vytváření účtu úložiště, primární a sekundární přístupové klíče jsou pro ni vygenerován. Primární přístupový klíč k vytvoření disk s kopií cloudu potřebují. Klíč se zobrazí v části názvu účtu úložiště > **přístupové klíče**.

    ![Přístupový klíč](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Přidat virtuální počítače s SQL serverem k: doména Contoso

1. Contoso přidá SQLAOG1 a SQLAOG2 do domény contoso.com.
2. Potom na každém virtuálním počítači, nainstalujte funkci clusteru převzetí služeb při selhání Windows a nástroje.

## <a name="set-up-the-cluster"></a>Nastavení clusteru

Před nastavením clusteru, Contoso pořídí snímek disku s operačním systémem na každém počítači.

![snímek](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. Potom se spuštění skriptu jsme připravili dohromady a vytvoří Cluster převzetí služeb při selhání Windows.

    ![Vytvoření clusteru](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. Po jejich vytvoření clusteru, ověřte, že virtuální počítače jako uzly clusteru.

     ![Vytvoření clusteru](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Nakonfigurovat disk s kopií cloudu

1. Contoso nakonfigurovat pomocí disk s kopií cloudu **Průvodce konfigurací kvora** v modulu Správce clusteru převzetí služeb při selhání.
2. V Průvodci vyberou vytvořte disk s kopií cloudu s účtem úložiště.
3. Po konfiguraci cloudové kopie clusteru se v zobrazí v modulu snap-in Správce clusteru převzetí služeb při selhání.

    ![Disk s kopií cloudu](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

## <a name="enable-sql-server-always-on-availability-groups"></a>Povolte skupiny dostupnosti AlwaysOn SQL serveru

Teď můžete povolit contoso Always On:

1. V SQL Server Configuration Manager umožňují **skupin dostupnosti AlwaysOn** pro **SQL Server (MSSQLSERVER)** služby.

    ![Povolte skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. Restartu služby se změny projevily.

Contoso s povolit AlwaysOn, můžete nastavit skupiny dostupnosti AlwaysOn, který bude chránit databázi SmartHotel.

**Potřebujete další pomoc?**

- [Přečtěte si informace o](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) určující sdílené složky a nastavení účtu úložiště v cloudu.
- [Získejte pokyny](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) pro nastavení clusteru a vytvoření skupiny dostupnosti.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Krok 3: Nasazení nástroje pro vyrovnávání zatížení Azure

Contoso se teď chcete nasadit interní nástroj pro vyrovnávání zatížení, který je umístěný před uzly clusteru. Nástroje pro vyrovnávání zatížení naslouchá provozu a nasměruje na příslušný uzel.

![Vyrovnávání zatížení](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

Nástroje pro vyrovnávání zatížení, vytvořte následujícím způsobem:

1. Na webu Azure portal > **sítě** > **Load Balancer**, nastavené nové nástroje pro vyrovnávání zatížení interní: **ILB-PROD-DB-EUS2-SQLAOG**.
2. Nástroje pro vyrovnávání zatížení, umístěte do produkční sítě **připojení typu VNET-PROD-EUS2**, v podsíti databáze **PROD-DB-EUS2**.
3. Přiřadily se statickou IP adresu: 10.245.40.100.
4. Jako prvek sítě nasadí nástroj pro vyrovnávání zatížení ve skupině síťových prostředků **ContosoNetworkingRG**.

    ![Vyrovnávání zatížení](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

Po nasazení interního nástroje Contoso musí ho nastavit. Vytvořte back-endový fond adres, nastavení sondu stavu a nakonfigurovat pravidlo Vyrovnávání zatížení.

### <a name="add-a-backend-pool"></a>Přidat back-endový fond

Distribuce provozu do virtuálních počítačů v clusteru, nastavte Contoso back-endový fond adres, který obsahuje IP adresy síťové karty pro virtuální počítače, které budou přijímat síťový provoz z nástroje pro vyrovnávání zatížení.

1. V nastavení nástroje pro vyrovnávání zatížení na portálu společnosti Contoso přidat back-endového fondu: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. Jsou k fondu přidružit SQLAOGAVSET skupiny dostupnosti. Virtuální počítače v sadě (**SQLAOG1** a **SQLAOG2**) jsou přidány do fondu.

    ![Back-endový fond](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Contoso vytvoří sondu stavu tak, aby nástroj pro vyrovnávání zatížení můžete sledovat stav aplikace. Test paměti dynamicky přidá nebo odebere virtuální počítače z oběhu nástroje pro vyrovnávání zatížení, podle způsobu jejich reakce na kontroly stavu.

Sonda vytvářejí následujícím způsobem: 

1. V nastavení nástroje pro vyrovnávání zatížení na portálu společnosti Contoso vytvoří sondu stavu: **SQLAlwaysOnEndPointProbe**.
2. Nastavují testu a monitorovat virtuální počítače na portu TCP 59999.
3. Nastavují interval 5 sekund mezi sondy a prahová hodnota 2. Pokud selžou dva testy paměti virtuálního počítače se budou považovat za není v pořádku.

    ![Test paměti](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Konfigurace nástroje pro vyrovnávání zatížení pro příjem provozu


Nyní Contoso požadavků na pravidlo nástroje pro vyrovnávání zatížení, které defins způsobu distribuce provozu do virtuálních počítačů

- Front-endovou IP adresu zpracovává příchozí provoz.
- Back endového fondu IP adres přijímá provoz.

Pravidla vytvářejí následujícím způsobem:

1. V nastavení nástroje pro vyrovnávání zatížení na portálu, přidávají nová pravidla Vyrovnávání zatížení: **SQLAlwaysOnEndPointListener**.
2. Contoso nastaví front-endu naslouchací proces k přijímání příchozího provozu klienta SQL na portu TCP 1433.
3. Určí back-endový fond, do které přenosy budou směrovat a port, na kterém virtuální počítače naslouchají provoz.
4. Contoso povolí plovoucí IP (přímá odpověď ze serveru vrácené). Toto je vždy vyžadován pro SQL AlwaysOn.

    ![Test paměti](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Potřebujete další pomoc?**

- [Získejte přehled](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) nástroje pro vyrovnávání zatížení Azure.
- [Další informace o](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) vytvoření nástroje pro vyrovnávání zatížení.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Krok 4: Příprava Azure pro službu Site Recovery

Tady jsou komponenty Azure Contoso potřebuje k nasazení Site Recovery:

- Virtuální síť, ve kterém budou virtuální počítače umístěné při jejich vytváření během převzetí služeb při selhání.
- Účet služby Azure storage k ukládání replikovaných dat. 
- Trezor služby Recovery Services v Azure.

Jsou tyto wmm nastavit takto:

1.  Contoso už vytvořili síť/podsíť, můžete použít pro Site Recovery při jejich [nasadili infrastrukturu Azure](contoso-migration-rehost-vm-sql-ag.md).

    - Aplikace SmartHotel je produkční aplikace a WEBVM se budou migrovat do Azure produkční sítě (VNET-PROD-EUS2) v primární oblasti východní USA 2.
    - WEBVM bude umístěna ve skupině prostředků ContosoRG, který se používá pro produkční prostředky, a v produkčním prostředí podsítě (PROD-FE – EUS2).

2. Contoso se vytvoří účet úložiště Azure (contosovmsacc20180528) v primární oblasti.

    - Účet pro obecné účely používají úložiště úrovně standard a replikací LRS.
    - Účet musí být ve stejné oblasti jako trezor.

    ![Site Recovery úložiště](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Pomocí účtu sítě a úložiště na místě, se nyní vytvořit trezor služby Recovery Services (**ContosoMigrationVault**) a umístěte ho **ContosoFailoverRG** skupinu prostředků, v primární oblasti USA – východ 2 .

    ![Trezor služby Recovery Services](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Příprava VMware v místním prostředí pro Site Recovery

Tady je co Contoso připraví lokálně:

- Účet vCenter serveru nebo hostiteli vSphere ESXi, pro automatizaci zjišťování virtuálních počítačů.
- Účet, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware, který chcete replikovat.
- Místní nastavení virtuálního počítače tak, aby Contoso můžete připojit k replikovanému virtuálnímu počítači Azure po převzetí služeb při selhání.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automatické zjišťování virtuálních počítačů. 
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení.
- Vyžaduje se alespoň účet jen pro čtení. Budete potřebovat účet, který může spouštět operace, jako je vytváření a odebírání disků a zapnutí na virtuálních počítačích.

Účet contoso nastaví takto:

1. Contoso vytvoří roli na úrovni vCenter.
2. Contoso poté přiřadí tuto roli požadovaná oprávnění.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na každém virtuálním počítači musí být nainstalovaná služba Mobility.

- Po povolení replikace pro virtuální počítač, může Site Recovery poskytnout automatickou nabízenou instalaci této součásti.
- Budete potřebovat účet, pomocí Site Recovery můžete pro přístup k virtuálnímu počítači pro nabízenou instalaci. Tento účet zadáte při nastavování replikace v konzole Azure.
- Účet může být v doméně nebo místní s oprávněními k instalaci na virtuálním počítači.




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


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Krok 5: Replikace místních virtuálních počítačů do Azure pomocí Site Recovery

Před spuštěním migrace do Azure, Contoso muset nastavit a povolit replikaci.

### <a name="set-a-replication-goal"></a>Nastavení cíle replikace

1. V trezoru v části název_trezoru (ContosoVMVault), výběr cíle replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu** .
2. Určí, že jsou jejich počítače nacházejí na místních běžící ve VMware a replikací do Azure.

    ![Cíl replikace](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Chcete-li pokračovat, musí potvrdit, že nebude dokončena plánování nasazení tak, že vyberete **Ano, mám to hotové**. V tomto scénáři Contoso jsou pouze migraci virtuálního počítače které nevyžadují plánování nasazení.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Contoso je potřeba nakonfigurovat jejich zdrojového prostředí. K tomuto účelu, stáhněte šablonu OVF a použít k nasazení Site Recovery konfigurační server jako vysoce dostupný, místní virtuální počítač VMware. Po vytvoření a spuštění je konfigurační server, aby se zaregistrovali v trezoru.

Konfigurační server běží komponent:

- Komponenta konfiguračního serveru, který koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.

Contoso následujícím způsobem proveďte tyto kroky:


1. V trezoru, stáhněte šablonu OVF z **připravit infrastrukturu** > **zdroj** > **konfigurační Server**.
    
    ![Stáhněte si OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. Jejich import šablony do VMware. Tím vytvoříte a nasadíte virtuální počítač.

    ![Šablona OVF](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. Při jejich převodu na virtuálním počítači poprvé, se spustí do prostředí instalace Windows serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce. Při prvním přihlášení ve výchozím nastavení spustí nástroj pro konfiguraci Azure Site Recovery.
5. V nástroji, zadejte název pro účely registrace konfiguračního serveru v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání připojení se přihlásit k předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.

    ![Registrace konfiguračního serveru](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Přihlášení k počítači znovu, a automaticky spustí Průvodce správou konfiguračního serveru.
9. V průvodci, vyberte síťovou kartu pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení konfigurace.
10. Vyberte předplatné, skupinu prostředků a trezor, do kterého chcete konfigurační server zaregistrovat.
        ![Trezor](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. Stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. 
11. Po ověření, zadejte plně kvalifikovaný název domény nebo IP adresa serveru vCenter nebo vSphere hostiteli. Ponechte výchozí port a zadejte popisný název pro vCenter server.
12. Zadejte účet, který je vytvořil pro automatické zjišťování a přihlašovací údaje, které se používají pro automatickou instalaci služby Mobility. Pro počítače s Windows účet potřebuje oprávnění místního správce na virtuálních počítačích.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Po dokončení registrace na webu Azure Portal, Contoso double ověří, že konfigurační server a VMware server jsou uvedeny na **zdroj** stránky v trezoru. Zjišťování může trvat 15 minut nebo déle. 
8. Site Recovery pak připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavení cíle

Nyní Contoso určuje nastavení cíle replikace.

1. V **připravit infrastrukturu** > **cílové**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a síť v zadané cílové.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Contoso teď můžete vytvořit zásady replikace.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružit**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota cíle bodu obnovení**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí hodnotu 24 hodin. Tato hodnota určuje, jak dlouho je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímků konzistentní vzhledem k**. Výchozí jednu hodinu. Tato hodnota určuje frekvenci, s jakou se vytvořit snímky konzistentní s aplikací.
 
        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružte zásady replikace](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Povolení replikace

Contoso nyní můžete spustit replikaci WebVM.

1. V **replikovat aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Indikuje, že chtějí povolit virtuální počítače, vyberte vCenter server a konfigurační server.

    ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Teď zadejte nastavení cíle, včetně skupiny prostředků a virtuální síť a účet úložiště, ve kterém se ukládají replikovaná data.

     ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Contoso vybere WebVM pro replikaci, zkontroluje zásady replikace a umožňuje replikaci. Po povolení replikace Site Recovery nainstaluje službu Mobility na virtuálním počítači.
 
    ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. Sledují průběh replikace v **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
5. V **Essentials** Contoso na webu Azure Portal, můžete zobrazit strukturu pro virtuální počítače, které se replikují do Azure.

    ![Zobrazení infrastruktury](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Potřebujete další pomoc?**

- Můžete si přečíst Úplný návod tohoto všechny tyto kroky v [nastavit zotavení po havárii pro virtuální počítače VMware v místním](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici při [zdrojové prostředí nastavíte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfiguračního serveru](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [konfigurace nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Další informace o [povolení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-install-the-database-migration-assistant-dma"></a>Krok 5: Instalace Pomocníka s migrací databáze (DMA)

Contoso se migrace databáze SmartHotel do virtuálního počítače Azure **SQLAOG1** pomocí DMA. Nastavené DMA následujícím způsobem:

1. Stažení nástroje [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) k virtuálnímu počítači v místním SQL serveru (**SQLVM**).
2. Na virtuálním počítači, spusťte instalační program (DownloadMigrationAssistant.msi).
3. Na **Dokončit** stránky, vyberou **spusťte Microsoft Data Migration Assistant** před dokončením průvodce.

## <a name="step-6-migrate-the-database-with-dma"></a>Krok 6: Migrace databáze pomocí DMA

1. V DMA spouštějí novou migraci - **SmartHotel**.
2. Vyberou **cílový server typ** jako **systému SQL Server na virtuálních počítačích Azure**. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. V podrobnostech migrace přidat **SQLVM** jako na zdrojovém serveru a **SQLAOG1** jako cíl. Určí pověření pro každý počítač.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Vytvoří místní sdílenou složkou informace databáze a konfigurace. Musí být přístupné s účtem služby SQL na SQLVM a SQLAOG1 přístup pro zápis.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso vybere přihlašovací údaje, které by se měly migrovat a zahajuje migraci. Po dokončení zobrazuje DMA migrace jako úspěšný.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Ověřují, že databáze je spuštěna **SQLAOG1**.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS připojuje k virtuálnímu počítači v místním SQL serveru přes připojení VPN typu site-to-site mezi Contoso datacentrem a Azure a potom provede migraci databáze.

## <a name="step-7-protect-the-database"></a>Krok 7: Ochrana databáze

S databází aplikace běžící na **SQLAOG1**, Contoso teď mohou chránit pomocí skupin dostupnosti AlwaysOn. Konfigurace AlwaysOn pomocí aplikace SQL Management Studio a pak přiřaďte naslouchací proces použití clusteringu s Windows. 

### <a name="create-an-alwayson-availability-group"></a>Vytvořit skupinu dostupnosti AlwaysOn

1. V SQL Management Studio, klikněte pravým tlačítkem na **vždy na vysokou dostupnost** spustit **Průvodce novou skupinou dostupnosti**.
2. V **zadat možnosti**, jejich název skupiny dostupnosti **SHAOG**. V **vybrat databáze**, vyberou SmartHotel databáze.

    ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. V **zadejte repliky**, přidejte dva uzly SQL jako repliky dostupnosti a nakonfigurujete je, aby poskytnout synchronním potvrzováním automatické převzetí služeb při selhání.

     ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. Konfigurace naslouchacího procesu pro skupinu (**SHAOG**) a portu. IP adresa interní služby load balancer se přidá jako statickou IP adresu (10.245.40.100).

    ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. V **vybrat synchronizaci dat**, umožňují automatická synchronizace replik indexů. Pomocí této možnosti systému SQL Server automaticky vytvoří sekundární repliky pro každou databázi ve skupině, takže Contoso nemusíte ručně zálohovat a obnovit tyto. Po ověření je vytváření skupiny dostupnosti.

    ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Při vytváření skupiny společnosti Contoso narazili na problém. Nejsou pomocí Active Directory Windows Integrated security a tedy nutné udělovat oprávnění do přihlášení serveru SQL k vytvoření rolí clusteru převzetí služeb při selhání Windows.

    ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. Po vytvoření skupiny společnosti Contoso je můžou zobrazit v aplikaci SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Konfigurace naslouchacího procesu v clusteru

Jako poslední krok v nastavení nasazení SQL společnosti Contoso nakonfiguruje interní služby load balancer jako naslouchací proces v clusteru a přináší naslouchacího procesu online. Chcete-li to provést pomocí skriptu.

![Naslouchací proces clusteru](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Zkontrolujte konfiguraci

Contoso se všechna nastavení, teď máte skupinu funkční dostupnosti v Azure, která používá migrovaná databáze. Jsou to ověřit tak připojení do interního nástroje v sadě SQL Management Studio.

![ILB připojení](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Potřebujete další pomoc?**
- Další informace o vytváření [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) a [naslouchací proces](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Ručně [nastavení clusteru použít IP adresu nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- [Další informace](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) o vytvoření a použití SAS.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Krok 8: Migrace virtuálního počítače pomocí služby Site Recovery

Spustit rychlou contoso testovací převzetí služeb při selhání a potom migrujete virtuální počítač.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Spuštění testovacího převzetí služeb při pomáhá zajistit, že všechno funguje podle očekávání před migrací. 

1. Contoso spustí testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovaný**).
2. Vyberou **před spuštěním převzetí služeb při selhání vypnout počítač**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před aktivací převzetí služeb. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Spuštění testovacího převzetí služeb při selhání: 

    - Kontrola spuštění, abyste měli jistotu, že jsou všechny podmínky pro migraci na místě požadavků.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

3. Po dokončení převzetí služeb se zobrazí na webu Azure Portal repliku virtuálního počítače Azure. Contoso ověří, že je virtuální počítač odpovídající velikost, který je připojený ke správné síti a že je spuštěná. 
4. Po ověření, Contoso vyčištění převzetí služeb při selhání a zaznamenejte a uložte jakékoli připomínky. 

### <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. Po ověření, zda testovací převzetí služeb fungovala podle očekávání, Contoso vytvořit plán obnovení pro migraci a WEBVM přidat do plánu.

     ![Plán obnovení](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. Spuštění převzetí služeb při selhání v plánu. Vyberte nejnovější bod obnovení a určit, že Site Recovery se měli pokusit vypněte místní virtuální počítač před aktivací převzetí služeb.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Po převzetí služeb při selhání ověřují, že se virtuální počítač Azure zobrazuje podle očekávání na webu Azure Portal.

    ![Plán obnovení](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Po ověření virtuálních počítačů v Azure, provést migraci dokončit proces migrace zastavit replikaci pro virtuální počítač a zastavte fakturace služby Site Recovery pro virtuální počítač.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Aktualizujte připojovací řetězec

Posledním krokem v procesu migrace Contoso aktualizovat připojovací řetězec aplikace tak, aby odkazoval migrované databáze SHAOG naslouchacího procesu. Tato konfigurace se změní na WEBVM nyní běží v Azure.  Tato konfigurace se nachází v souboru web.config aplikace ASP. 

1. Vyhledejte soubor za C:\inetpub\SmartHotelWeb\web.config.  Změnit název serveru tak, aby odrážely plně kvalifikovaný název domény AOG: shaog.contoso.com.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Po aktualizaci souboru a jeho uložení, restartujte službu IIS na WEBVM. Jsou to provést pomocí IISRESET /RESTART z příkazového řádku.
2. Po restartování služby IIS aplikace teď používá databázi a systémem SQL MI.


**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštění testovací převzetí služeb při selhání. 
- [Přečtěte si](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) převzetí služeb při selhání do Azure.

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Po dokončení migrace SmartHotel aplikace běží na Virtuálním počítači Azure a SmartHotel databáze nachází v clusteru Azure SQL.

Nyní Contoso potřebuje k dokončení těchto kroků vyčištění:  

- Odebrání místních virtuálních počítačů z inventáře vCenter.
- Odeberte virtuální počítače z místní úlohy zálohování.
- Aktualizujte interní dokumentaci zobrazíte novým umístěním a IP adresy pro virtuální počítače.
- Zkontrolujte všechny prostředky, které pracují s Vyřazená z provozu virtuálních počítačů a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.
- Přidání že dvou nových virtuálních počítačů (SQLAOG1 a SQLAOG2) měla být přidána do produkční systémy pro monitorování.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

Bezpečnostní tým Contoso kontroly WEBVM virtuální počítače Azure, SQLAOG1 a SQLAOG2, chcete-li zjistit všechny problémy se zabezpečením. 

- Skupiny zabezpečení sítě (Nsg), zkontrolujte pro virtuální počítač pro řízení přístupu. Skupiny zabezpečení sítě se používají k zajištění, že lze předat pouze provoz povolený pro aplikaci.
- Zvažují zabezpečení dat na disku pomocí Azure Disk Encryption a trezor klíčů.
- By měla vyhodnotit transparentní šifrování dat (TDE) a pak ji povolit na databázi SmartHotel běžící na nové AOG SQL. [Další informace](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[Přečtěte si další](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupech zabezpečení pro virtuální počítače.

### <a name="backups"></a>Zálohování

Contoso se bude zálohovat data na WEBVM SQLAOG1 a SQLAOG2 pomocí služby Azure Backup. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

1. Contoso má existující licencování pro jejich WEBVM a bude využít zvýhodněné hybridní využití Azure.  Budete převodu stávajících virtuálních počítačích Azure využívat Tento cenový model.
2. Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management. 

## <a name="conclusion"></a>Závěr

V tomto článku Contoso rehosted SmartHotel aplikace v Azure do Azure pomocí služby Site Recovery migrací aplikace front-endový virtuální počítač. Migrovat databázi aplikace do clusteru systému SQL Server zřízenou ve službě Azure a chráněné ve skupině dostupnosti AlwaysOn systému SQL Server.

## <a name="next-steps"></a>Další postup

V dalším článku v této sérii jsme ukážeme, jak společnosti Contoso opětovným hostováním aplikací osTicket helpdesku začlenit služby spuštěné v Linuxu a nasazení s databází MySQL.


