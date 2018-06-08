---
title: Opětovným hostováním aplikace Contoso a migrujte jej do skupiny dostupnosti AlwaysOn SQL serveru a virtuální počítače Azure | Microsoft Docs
description: Zjistěte, jak Contoso opětovným hostováním místní aplikace a migrujte jej do skupiny dostupnosti AlwaysOn SQL serveru a virtuální počítače Azure
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: raynew
ms.openlocfilehash: 97c8430ab5d4e08e52790b898051d5985c3df03c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839865"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-sql-server-alwayson-availability-group"></a>Migrace Contoso: opětovným hostováním místní aplikace do skupiny dostupnosti AlwaysOn SQL serveru a virtuální počítače Azure

Tento článek ukazuje, jak Contoso opětovným hostováním jejich SmartHotel aplikaci v Azure. Front-endu aplikace virtuálních počítačů se migrovat virtuální počítač Azure a databázi aplikace do Azure SQL serveru virtuálního počítače, s v clusteru s podporou převzetí služeb při selhání systému Windows Server se skupinami dostupnosti AlwaysOn serveru SQL.

Tento dokument je jedním z řady články, které ukazují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Řada obsahuje základní informace a scénáře, které ilustrují nastavení infrastruktury a migrace, vyhodnocování místních prostředků pro migraci a s určitými typy migrace. Scénáře růst v složitost a přidáme další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, řady článku a ukázkových aplikací, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak Contoso připraví jeho místní a infrastrukturu Azure pro migraci. Stejnou infrastrukturu se používá pro všechny články migrace. | K dispozici.
[Článek 3: Vyhodnocení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak Contoso spouští posouzení SmartHotel dvouvrstvá místní aplikace běžící ve VMware. Contoso vyhodnocuje aplikace virtuálních počítačů s [Azure migrovat](migrate-overview.md) služby a aplikace databáze systému SQL Server s [databáze migrace pomocníka](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Opětovným hostováním aplikace pro virtuální počítače Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak Contoso běží navýšení a shift migrace do Azure pro aplikaci SmartHotel. Contoso migruje virtuální počítač front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databázi aplikace do spravované Instance SQL, pomocí [služba migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.
[Článek 5: Opětovným hostováním aplikace pro virtuální počítače Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak migrovat Contoso aplikace SmartHotel virtuální počítače pouze pomocí Site Recovery.
Článek 6: Opětovným hostováním aplikace na virtuálních počítačích Azure a SQL Server vždy na skupiny dostupnosti (v tomto článku) | Ukazuje, jak Contoso migruje SmartHotel aplikace. Společnost Contoso využívá Site Recovery k migraci virtuálních počítačů a aplikace službu migrace databáze k databázi aplikace migraci na cluster serveru SQL Server, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
Článek 7: Opětovným hostováním Linux aplikace pro virtuální počítače Azure a Azure MySQL Server | Ukazuje, jak Contoso migruje aplikace osTicket Linux. Migrace virtuálního počítače pomocí Site Recovery front-endu a migrovat (zálohování a obnovení) databáze na instanci serveru MySQL Azure pomocí MySQL Workbench | Plánováno
Článek 8: Opětovným hostováním Linux aplikace pro virtuální počítače Azure | Ukazuje, jak Contoso nepodporuje migraci za navýšení a shift osTicket další aplikace virtuálních počítačů do Azure, pomocí Site Recovery | Plánováno


V tomto článku migrovat Contoso dvouvrstvá Windows. NET SmartHotel aplikace běžící na virtuálních počítačích VMware do Azure. Pokud chcete používat tuto aplikaci, je poskytována jako s otevřeným zdrojem a si můžete stáhnout z [Githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chtějí dosáhnout s této migrace:

- **Adresa růst podniku**: Contoso roste a výsledkem je tlak na svých místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso musí odebrat nepotřebné postupy a zefektivnit procesy pro vývojáře a uživatele.  Obchodním potřebám IT rychlá a není nakládání s čas nebo peníze, tím rychleji doručováním na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT musí být rychlejšího potřebám firmy. Musí být schopné reagovat rychleji, než změny v marketplace, chcete-li povolit úspěch v globálním hospodářství.  Nemůže získat způsobem, ani stát otevíraných oken firmy.
- **Škálování**: růstem firmy úspěšně Contoso IT musí poskytnout systémy, které se můžou růst stejným tempem.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudu Contoso má připnuli dolů cíle pro migraci. Tyto cíle se používaly určit nejlepší metody migrace:

- Po migraci aplikaci v Azure měli stejné možnosti výkonu jako dnes v prostředí VMWare.  Aplikace bude stále jako kritické v cloudu jako je na místě.
- Contoso nechce investovat do této aplikace.  Je důležité pro jejich podnikání, ale v současné podobě jednoduše chtějí bezpečně ho přesunout do cloudu.
- Problémy s dostupností má neprobíhala v místní databázi pro aplikaci. Jejich rádi viděli, že se nasadit v Azure jako cluster s podporou vysoká dostupnost s funkcí převzetí služeb při selhání.
- Contoso chce upgradovat ze své aktuální platformě SQL Server 2008 R2, na SQL serveru 2017.
- Contoso nechce používat Azure SQL Database pro tuto aplikaci, a hledá alternativy.

## <a name="proposed-architecture"></a>Navrhované architektura

V tomto scénáři:

- Aplikace je vrstvené mezi dva virtuální počítače (WEBVM a SQLVM).
- Virtuální počítače se nenachází v hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Spravuje prostředí VMware vCenter Server verze 6.5 (**vcenter.contoso.com**), spuštěné na virtuálním počítači.
- Contoso má místní datacenter (contoso-datacenter), s řadič domény místní (**contosodc1**).
- Místní virtuální počítače v datovém centru společnosti Contoso bude vyřazena z provozu, po dokončení migrace.

![Architektura scénáře](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Služba správy databáze](https://docs.microsoft.com/azure/dms/dms-overview) | Contoso bude migrovat aplikace datovou vrstvu pomocí DMS. Služba DMS se připojit k místní SQLVM počítač přes síť site-to-site VPN a migrovat DMS umožňuje bezproblémové migrace z více zdrojů databáze do platformy dat Azure s minimálními výpadky. | Další informace o [podporované oblasti](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) pro DMS a získání [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Contoso použije Site Recovery pro migraci za navýšení a shift front-endu aplikace virtuálních počítačů. Site Recovery orchestruje a spravuje migrace a zotavení po havárii pro virtuální počítače Azure a místní virtuální počítače a fyzické servery.  | Při replikaci do Azure se vám neúčtují poplatky za úložiště Azure.  Virtuální počítače Azure se vytvářejí a platit poplatky, když dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) o poplatky a ceny.

 

## <a name="migration-process"></a>Proces migrace

- Contoso bude migrovat aplikace virtuálních počítačů do Azure.
- Virtuální počítač front-endu jejich budete migrovat do virtuálního počítače Azure pomocí Site Recovery:
    - Jako první krok budou Příprava a nastavíte Azure komponenty a příprava místní infrastruktury VMware.
    - S všechno připravené můžou spustit replikaci virtuálního počítače.
    - Po povolení replikace a práci, se migrovat virtuální počítač po selhání do Azure.
- Databáze se budete migrovat do clusteru serveru SQL Server v Azure pomocí služby migrace dat (DMS).
    - Jako první krok se budete potřebovat ke zřízení virtuálních počítačů serveru SQL v Azure, nastavení clusteru a k nástroji pro vyrovnávání zatížení interní a konfigurace skupin dostupnosti AlwaysOn.
    - Pomocí této na místě se můžete migrovat databázi
- Po dokončení migrace umožňují ochranu AlwaysOn pro databázi.

![Proces migrace](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Požadavky

Tady je, co vám (a Contoso) musí spustit tento scénář:

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste již vytvořili předplatné Pokud jste provedli v první článek z této série hodnocení. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud používáte stávající předplatné a nejste správce, budete muset ve spolupráci s správcem můžete přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastrukturu Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.<br/><br/> Další informace o konkrétní [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) požadavky pro Site Recovery.
**Obnovení lokality (místní)** | Vaše místní vCenter server musí používat verzi 5.5, 6.0 nebo 6.5<br/><br/> Hostiteli ESXi verzí 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware na hostiteli ESXi spuštěna.<br/><br/> Virtuální počítače, musí splňovat [požadavky pro Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Podporované [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurace.<br/><br/> Virtuální počítače, které chcete replikovat, musí splňovat [požadavky pro Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).
**SLUŽBA DMS** | Služba DMS musíte [kompatibilní místní zařízení VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Musí umět nakonfigurovat místního zařízení VPN. Musí mít zvenčí veřejnou IPv4 adresu a adresu nesmí být umístěné za zařízením NAT.<br/><br/> Ujistěte se, že máte přístup k místní databázi systému SQL Server.<br/><br/> Brána Windows Firewall byste měli mít přístup k zdrojového databázového stroje. [Další informace](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Pokud je brána firewall před počítači pro databáze, přidejte pravidla povolit přístup k databázi a soubory přes SMB port 445.<br/><br/> Pověření použitá k připojení k systému SQL Server zdrojové a cílové spravované Instance musí být členy role serveru sysadmin.<br/><br/> Budete potřebovat síť sdílet v místní databázi, DMS můžete použít k zálohování zdrojové databáze.<br/><br/> Ujistěte se, zda má účet služby spuštění instance systému SQL Server zdrojové oprávnění k zápisu ve sdílené síťové složce.<br/><br/> Poznamenejte si uživatel systému Windows (a heslo), který má oprávnění Úplné řízení na sdílené síťové složce. Služba Azure databáze migrace zosobňuje tyto přihlašovací údaje uživatele pro nahrání záložní soubory do kontejneru úložiště Azure.<br/><br/> Proces instalace systému SQL Server Express nastaví protokolu TCP/IP na **zakázané** ve výchozím nastavení. Ujistěte se, že je povolena.


## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob Contoso spuštění migrace:

> [!div class="checklist"]
> * **Krok 1: Vytvoření virtuálních počítačů serveru SQL v Azure**: pro zajištění vysoké dostupnosti Contoso chcete nasadit clusterované databáze v Azure. Nasadí dva virtuální počítače SQL serveru a pro vyrovnávání zatížení Azure interní.
> * **Krok 2: Nasazení clusteru**: po nasazení virtuálních počítačů serveru SQL, že příprava clusteru služby Azure SQL Server.  Do této předem vytvořené clusteru se budete migrovat své databáze.
> * **Krok 3: Příprava DMS**: Příprava DMS jejich registraci zprostředkovatele migrace databáze, vytvořte instanci DMS a projekt. Nastavené sdílený přístupový podpis (SAS) identifikátor URI (Uniform Resource). DMS pomocí identifikátoru URI SA přístup ke kontejneru účtu úložiště, do které služba odesílá soubory zálohování systému SQL Server.
> * **Krok 4: Příprava Azure Site Recovery**: uživatel vytvořit účet úložiště Azure pro uložení replikovaná data a trezoru služeb zotavení.
> * **Krok 5: Příprava místní VMware Site Recovery**: Příprava účty pro virtuální počítač zjišťování a instalaci agenta a připravit místní virtuální počítače tak, aby mohli připojit k virtuálním počítačům Azure po převzetí služeb při selhání.
> * **Krok 6: Replikace virtuálních počítačů**: Konfigurace nastavení replikace a povolení replikace virtuálních počítačů.
> * **Krok 7: Migrace databáze s DMS**: jejich migrace databáze.
> * **Krok 8: Migrace virtuálních počítačů pomocí Site Recovery**: jejich prvním spuštění převzetí služeb při selhání a ujistěte se, všechno funguje, za nímž následuje úplné převzetí služeb při selhání k migraci virtuálního počítače.


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Krok 1: Příprava clusteru skupiny dostupnosti AlwaysOn serveru SQL

Contoso chce Plánujte dopředu, nasazením databázi v clusteru s podporou v Azure. Pak můžete použít tento cluster pro jiné databáze. 

- Virtuální počítače serveru SQL nasadí ve skupině prostředků ContosoRG (používá se pro produkční prostředky).
- Kromě jedinečné názvy oba virtuální počítače používat stejné nastavení.
- Nástroje pro vyrovnávání zatížení pro vnitřní nasadí v ContosoNetworkingRG (používá se pro síťové prostředky).
- Virtuální počítače se spustí Windows Server 2016 se SQL Server 2017 Enterprise Edition. Contoso nemá licence pro tento operační systém, budete používají bitovou kopii v Azure Marketplace poskytující licence jako náklady na jejich Azure EA závazků.

Zde je, jak Contoso nastavit clusteru:

1. Uživatel vytvořit dva virtuální počítače SQL Server výběrem image SQL serveru 2017 Enterprise systému Windows Server 2016 v Azure Marketplace. 

    ![SKU VIRTUÁLNÍHO POČÍTAČE SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. V **vytvoření virtuálního počítače průvodce** > **Základy**, je potřeba nakonfigurovat:

    - Názvy virtuálních počítačů: **SQLAOG1** a **SQLAOG2**.
    - Vzhledem k tomu, že jsou počítače důležitých, umožňují SSD pro typ disku virtuálního počítače.
    - Určí počítač přihlašovací údaje.
    - Nasadí virtuální počítače v primární oblasti VÝCHOD USA 2 oblast, ve skupině prostředků ContosoRG.

3. V **velikost**, jsou při spuštění systému D2s_V3 SKU pro oba virtuální počítače. Budete škálování později potřebují.
4. V **nastavení**, dělají následující:

    - Vzhledem k tomu, že tyto virtuální počítače jsou kritické databáze pro aplikaci, používají spravované disky.
    - Jejich umístit na počítače v produkční síti východní USA 2 primární oblasti (**virtuální sítě. PRODUKČNÍMU EUS2**), v podsíti databáze (**PRODUKČNÍMU-DB-EUS2**).
    - Vytvoří novou skupinu dostupnosti: **SQLAOGAVSET**, s dvou domén selhání a pět aktualizaci domény.

    ![VIRTUÁLNÍ POČÍTAČ SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. V **nastavení systému SQL Server**, že limit připojení SQL k virtuální síti (soukromý), výchozí port 1433. Pro ověřování používají stejné přihlašovací údaje, které používají dohlížející na bezpečný (**contosoadmin**).

    ![VIRTUÁLNÍ POČÍTAČ SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Potřebujete další pomoc?**

- [Získání nápovědy](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) zřizování virtuálního počítače s SQL Server.
- [Další informace o](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) konfigurace virtuálních počítačů pro různé identifikátory SKU serveru SQL.

## <a name="step-2-deploy-the-failover-cluster"></a>Krok 2: Nasazení clusteru převzetí služeb při selhání

Zde je, jak Contoso nastavit clusteru:

1. Účet úložiště Azure nastavené tak, aby fungoval jako určující cloudu.
2. Zvyšují virtuálním počítačům systému SQL Server k doméně služby Active Directory v datovém centru společnosti Contoso na místě.
3. Uživatel v Azure vytvořit cluster.
4. Je potřeba nakonfigurovat určující cloudu.
5. A konečně povolte skupiny dostupnosti SQL Always On.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Nastavit účet úložiště jako určující cloudu

Nastavení určujícího cloudu, musí Contoso účet úložiště Azure, který bude obsahovat soubor blob použitý pro arbitrážního clusteru. Stejný účet úložiště lze nastavit určující cloudu pro víc clusterů. 

Contoso vytvoří účet úložiště následujícím způsobem:

1. Určí zapamatovatelný název pro účet (**contosocloudwitness**).
2. Nasadí účet s LRS obecné všechny účely.
3. Účet se umístit v oblasti třetí - jihu USA. Jejich umístěte ho mimo oblast primární a sekundární tak, aby zůstal dostupný v případě selhání místní.
4. Jejich umístit ve své skupině prostředků, který obsahuje prostředků infrastruktury – **ContosoInfraRG**.

    ![Cloud s kopií clusteru](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Když uživatel vytvořit účet úložiště, primární a sekundární přístupové klíče jsou generovány pro ni. Potřebují primární přístupový klíč k vytvoření určujícího prvku cloudu. Klíč se zobrazí v části název účtu úložiště > **přístupové klíče**.

    ![Přístupový klíč](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Přidání virtuálním počítačům systému SQL Server do domény Contoso

1. Contoso přidá SQLAOG1 a SQLAOG2 do domény contoso.com.
2. Potom na každém virtuálním počítači instalaci funkce clusteru převzetí služeb při selhání systému Windows a nástroje.

## <a name="set-up-the-cluster"></a>Nastavení clusteru

Před nastavením clusteru, Contoso pořídí snímek disk operačního systému na každém počítači.

![Snímek](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. Potom budou spusťte skript, který jste se blokovat dohromady a vytvoří Cluster převzetí služeb při selhání ve Windows.

    ![Vytvoření clusteru](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. Po jejich vytvoření clusteru se ověřte, že virtuální počítače se objeví jako uzly clusteru.

     ![Vytvoření clusteru](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Konfigurace určujícího prvku cloudu

1. Contoso konfigurace určujícího prvku cloudu pomocí **Průvodce konfigurací kvora** ve Správci clusteru převzetí služeb při selhání.
2. V Průvodci vyberou vytvoření určujícího cloudu s účtem úložiště.
3. Po dokončení konfigurace určujícího prvku cloudu v zobrazí v modulu snap-in Správce clusteru převzetí služeb při selhání.

    ![Cloud s kopií clusteru](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

## <a name="enable-sql-server-always-on-availability-groups"></a>Povolte skupiny dostupnosti SQL serveru Always On

Contoso můžete teď povolit Always On:

1. Ve Správci konfigurace serveru SQL umožňují **skupiny dostupnosti AlwaysOn** pro **serveru SQL (MSSQLSERVER)** služby.

    ![Povolte technologii AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. Restartu služby, aby se projevily změny.

Contoso s povolit AlwaysOn, můžete nastavit skupiny dostupnosti AlwaysOn, který bude chránit databázi SmartHotel.

**Potřebujete další pomoc?**

- [Přečtěte si informace o](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) cloudu určující sdílené složky a nastavení účtu úložiště pro ni.
- [Získat pokyny](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) pro nastavení clusteru a vytvoření skupiny dostupnosti.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Krok 3: Nasaďte nástroj pro vyrovnávání zatížení Azure

Contoso je teď chcete nasadit interní zátěže, která se nachází před uzly clusteru. Nástroje pro vyrovnávání zatížení nenaslouchá provozu a přesměruje na příslušný uzel.

![Vyrovnávání zatížení](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

Nástroje pro vyrovnávání zatížení se vytvořit takto:

1. Na portálu Azure > **sítě** > **Vyrovnávání zatížení**, budou nastavení nové služby Vyrovnávání zatížení pro vnitřní: **ILB-PRODUKČNÍMU-DB-EUS2-SQLAOG**.
2. Nástroje pro vyrovnávání zatížení se umístit v produkční síti **virtuální sítě. PRODUKČNÍMU EUS2**, v podsíti databáze **PRODUKČNÍMU-DB-EUS2**.
3. Se ji přiřadit statickou IP adresu: 10.245.40.100.
4. Jako element sítě nasadí nástroj pro vyrovnávání zatížení ve skupině prostředků sítě **ContosoNetworkingRG**.

    ![Vyrovnávání zatížení](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

Po nasazení nástroje pro vyrovnávání zatížení pro vnitřní Contoso musí ho nastavit. Jejich vytvoření fondu adres back-end, nastavení kontroly stavu a konfiguraci pravidlo Vyrovnávání zatížení.

### <a name="add-a-backend-pool"></a>Přidejte fond back-end

Chcete-li distribuovat provoz na virtuální počítače v clusteru, Contoso Nastavte Back-endový fond adres, který obsahuje IP adresy síťové karty pro virtuální počítače, které budou přijímat síťový provoz z nástroje pro vyrovnávání zatížení.

1. V nastavení nástroje pro vyrovnávání zatížení na portálu společnosti Contoso přidat fond back-end: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. Spojení s skupinu dostupnosti SQLAOGAVSET fondu. Virtuální počítače v sadě (**SQLAOG1** a **SQLAOG2**) jsou přidány do fondu.

    ![Fond back-end](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Contoso vytvoří kontrolu stavu, aby nástroj pro vyrovnávání zatížení můžete monitorovat stav aplikace. Tato kontrola dynamicky přidá nebo odebere virtuálních počítačů z otáčení nástroje pro vyrovnávání zatížení založené na tom, jak budou reagovat na kontroly stavu.

Tato kontrola se vytvořit takto: 

1. V nastavení nástroje pro vyrovnávání zatížení na portálu společnosti Contoso vytvoří kontrolu stavu: **SQLAlwaysOnEndPointProbe**.
2. Nastavují test k monitorování virtuálních počítačů na portu TCP 59999.
3. Nastavují interval 5 sekund mezi testy paměti a prahová hodnota 2. Pokud dva sondy selže, virtuální počítač bude považovat za není v pořádku.

    ![Test](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Konfigurace pro vyrovnávání zatížení pro příjem dat


Nyní Contoso potřebám pravidlo Vyrovnávání zatížení do defins, jak se provoz rozděluje k virtuálním počítačům.

- IP adresu front-endu zpracovává příchozí provoz.
- Fond back-end IP přijímá provoz.

Uživatel vytvořit pravidlo následujícím způsobem:

1. V nastavení nástroje pro vyrovnávání zatížení portálu si přidat nové pravidlo Vyrovnávání zatížení: **SQLAlwaysOnEndPointListener**.
2. Contoso nastaví naslouchací proces front-endu přijímat příchozí provoz klienta SQL na portu TCP 1433.
3. Určí fond back-end, který má jaký provoz, budou směrovány a port, na kterém virtuální počítače naslouchat provoz.
4. Contoso umožňuje plovoucí IP (přímá odpověď ze serveru). To je vždy nutné SQL AlwaysOn.

    ![Test](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Potřebujete další pomoc?**

- [Přehled](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) nástroje pro vyrovnávání zatížení Azure.
- [Další informace o](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) vytváření Vyrovnávání zatížení.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Krok 4: Příprava Azure pro službu Site Recovery

Tady jsou Azure součásti Contoso nepotřebuje nasazení Site Recovery:

- Virtuální síť, ve kterém virtuální počítače budou umístěné při jejich vytváření během převzetí služeb při selhání.
- Účet úložiště Azure pro uložení replikovaná data. 
- Trezor služeb zotavení v Azure.

Nastavené tyto následujícím způsobem:

1.  Contoso, které jsou již vytvořeny sítě a podsítě, můžete použít pro obnovení lokality při jejich [nasadit infrastrukturu Azure](contoso-migration-rehost-vm-sql-ag.md).

    - Aplikace SmartHotel je produkční aplikace a WEBVM se budou migrovat do produkční sítě Azure (VNET-PRODUKČNÍMU-EUS2) v primární oblasti Východ US2.
    - WEBVM bude umístěna ve skupině prostředků ContosoRG, který se používá pro produkční prostředky, a v podsíti produkční (PRODUKČNÍMU-FE – EUS2).

2. Contoso vytvoří účet Azure storage (contosovmsacc20180528) v primární oblasti.

    - Účet pro obecné účely, používají se standardní úložiště a LRS replikace.
    - Účet musí být ve stejné oblasti jako trezor.

    ![Úložiště obnovení lokality](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Pomocí účtu sítě a úložiště na místě trezoru služeb zotavení nyní vytvoří (**ContosoMigrationVault**) a jeho umístění **ContosoFailoverRG** skupinu prostředků, v primární oblasti Východ USA 2 .

    ![Trezor služby Recovery Services](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Příprava místní VMware Site Recovery

Tady je co Contoso připraví na místě:

- Účet v systému vCenter server nebo vSphere ESXi hostiteli, a automatizovat zjišťování virtuálních počítačů.
- Účet, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware, který chcete replikovat.
- Místní nastavení virtuálního počítače tak, aby Contoso může připojit k replikovaný virtuální počítač Azure po převzetí služeb při selhání.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automaticky zjistit virtuální počítače. 
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení.
- Vyžaduje se alespoň účet jen pro čtení. Je třeba účet, který můžete spustit operací, jako je vytváření a odebrání disků a zapnutí virtuálních počítačů.

Účet contoso nastavuje následujícím způsobem:

1. Contoso vytvoří roli na úrovni vCenter.
2. Tato role contoso poté přiřadí požadovaná oprávnění.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Služba Mobility musí být nainstalovaná na každém virtuálním počítači.

- Site Recovery můžete provést automatické nabízené instalace této součásti, pokud je zapnutá replikace pro virtuální počítač.
- Je třeba účet, který Site Recovery můžete použít pro přístup k virtuálnímu počítači na nabízenou instalaci. Tento účet zadáte při nastavování replikace v konzole Azure.
- Účet může být v doméně nebo místní, s oprávněními k instalaci na virtuálním počítači.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání Contoso chce připojit k virtuálním počítačům Azure. K tomu, že udělejte před migrací:

1. Pro přístup přes internet se:

 - Povolení protokolu RDP pro virtuální počítač na místě před převzetí služeb při selhání
 - Ujistěte se, že pro přidána pravidla TCP a UDP **veřejné** profilu.
 - Zkontrolujte, zda je povolen protokol RDP v **brány Windows Firewall** > **povolené aplikace** pro všechny profily.
 
2. Pro přístup prostřednictvím sítě site-to-site VPN že:

 - Povolení protokolu RDP na místním počítači.
 - Povolit RDP v **brány Windows Firewall** -> **povolené aplikace a funkce**, pro **domény a privátní** sítě.
 - Nastavit zásada SAN operačního systému na virtuální počítač místní **OnlineAll**.

Kromě toho při jejich spuštění převzetí služeb při selhání je muset zkontrolujte následující:

- Měla by existovat žádné Windows aktualizace čekající na vyřízení ve virtuálním počítači při spuštění převzetí služeb při selhání. Pokud existují, nebudou moci přihlásit do virtuálního počítače, až do dokončení aktualizace.
- Po převzetí služeb při selhání, můžete zkontrolovat **spouštění diagnostiky** zobrazíte snímek virtuálního počítače. Pokud to nepomůže, by měl ověřit, že virtuální počítač používá a zkontrolujte tyto [tipy pro odstraňování potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) vytvoření a přiřazení role pro automatické zjišťování.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) vytváření účtu pro nabízenou instalaci služby Mobility.


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Krok 5: Replikovat místní virtuální počítače Azure pomocí Site Recovery

Ještě před jejich spuštěním migrace do Azure, Contoso muset nastavit a zapnout replikaci.

### <a name="set-a-replication-goal"></a>Nastavte cíle replikace

1. V úložišti, v části název trezoru (ContosoVMVault) vyberou cílem replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu** .
2. Určí, že jejich počítačů se nacházejí na místních, běžící ve VMware a replikaci do Azure.

    ![Cíl replikace](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrďte plánování nasazení

Chcete-li pokračovat, potřebují potvrďte, že se dokončila plánování nasazení tak, že vyberete **Ano, mám to hotové**. V tomto scénáři Contoso jsou pouze při migraci virtuálního počítače a nepotřebujete plánování nasazení.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Contoso je potřeba nakonfigurovat jejich zdrojové prostředí. K tomuto účelu se stáhnout šablonu OVF a použijte jej k nasazení Site Recovery konfigurační server jako vysoce dostupný, místní virtuální počítač VMware. Po konfiguraci serveru je spuštěný a funkční, se zaregistrovat v trezoru tuto.

Konfigurační server běží počet součástí:

- Součást konfigurace serveru, který koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.

Contoso následujícím způsobem proveďte tyto kroky:


1. V úložišti, stáhnou šablony OVF **Příprava infrastruktury** > **zdroj** > **konfigurační Server**.
    
    ![Stáhnout OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. Šablona se naimportovat do VMware vytvoření a nasazení virtuálního počítače.

    ![Šablona OVF](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. Při zapnutí virtuální počítač poprvé, spustí se až do instalace produktu Windows Server 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce. Při prvním přihlášení ve výchozím nastavení spustí nástroj Azure obnovení lokality konfigurace.
5. V nástroji určí název pro registraci konfigurační server v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání připojení k přihlášení k předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.

    ![Zaregistrujte konfigurační server](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Přihlášení k počítači znovu a automaticky spustí Průvodce konfigurací serveru správy.
9. V Průvodci vyberou síťový adaptér pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení své konfigurace.
10. Výběrem předplatné, skupinu prostředků a úložiště, ve kterém zaregistrujte konfigurační server.
        ![Trezoru](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. Se pak stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. 
11. Po ověření že zadejte plně kvalifikovaný název domény nebo IP adresa vCenter server nebo vSphere hostitele. Ponechte výchozí port a zadejte popisný název pro vCenter server.
12. Určí účet vytvořený pro automatické zjišťování a přihlašovací údaje, které slouží k můžete automaticky nainstalovat službu Mobility. Pro počítače s Windows že účet potřebuje oprávnění místního správce na virtuálních počítačích.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Po dokončení registrace na portálu Azure Contoso dvojité ověří, že konfigurační server a VMware server jsou uvedené na **zdroj** stránky v úložišti. Zjišťování může trvat 15 minut nebo déle. 
8. Site Recovery pak připojí k VMware servery pomocí zadané nastavení a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavit cíl

Nyní Contoso určuje nastavení cíle replikace.

1. V **připravit infrastrukturu** > **cíl**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a sítě v zadané cílové.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Contoso Ne, můžete vytvořit zásady replikace.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružení**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota RPO**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí 24 hodin. Tato hodnota určuje, jak dlouho je okno uchování pro každý bod obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímkování konzistentní aplikace vzhledem**. Výchozí jednu hodinu. Tato hodnota určuje četnost, v němž jsou vytvořen snímky konzistentní s aplikacemi.
 
        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružení zásady replikace](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Povolení replikace

Contoso nyní můžete spustit replikaci WebVM.

1. V **replikujte aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Indikuje, že chcete povolit virtuální počítače, vyberte vCenter server a konfigurační server.

    ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Nyní zadejte cíl nastavení, včetně skupiny prostředků a virtuální síť a účet úložiště, ve kterém se uloží replikovaná data.

     ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Contoso vybere WebVM pro replikaci, ověří zásady replikace a umožňuje replikace. Site Recovery nainstaluje služba Mobility na virtuálním počítači, pokud je zapnutá replikace.
 
    ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. Budou sledovat průběh replikace **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
5. V **Essentials** Contoso na portálu Azure můžete zobrazit strukturu pro virtuální počítače replikující se do Azure.

    ![Zobrazení infrastruktury](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Potřebujete další pomoc?**

- Můžete si přečíst úplné návod všechny tyto kroky v [nastavit zotavení po havárii pro místní virtuální počítače VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici vám pomohou [nastavení zdrojového prostředí](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfigurační server](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [nakonfigurujete nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Další informace o [povolení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-install-the-database-migration-assistant-dma"></a>Krok 5: Instalace asistent migrace databáze (DMA).

Contoso se migrovat databázi SmartHotel do virtuálního počítače Azure **SQLAOG1** pomocí DMA. Nastavené DMA následujícím způsobem:

1. Stáhnou nástroj z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) na virtuální počítač místní SQL Server (**SQLVM**).
2. Instalační program (DownloadMigrationAssistant.msi) běží na virtuální počítač.
3. Na **Dokončit** stránky, vyberou **spusťte Microsoft Data migrace pomocníka** před dokončením průvodce.

## <a name="step-6-migrate-the-database-with-dma"></a>Krok 6: Migrace databáze s přímý přístup do paměti

1. V DMA spuštění nové migrace - **SmartHotel**.
2. Vyberou **cíle typ serveru** jako **systému SQL Server na virtuálních počítačích Azure**. 

    ![PŘÍMÝ PŘÍSTUP DO PAMĚTI](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. V podrobnostech migrace přidat **SQLVM** jako zdrojový server, a **SQLAOG1** jako cíl. Určí pověření pro každý počítač.

     ![PŘÍMÝ PŘÍSTUP DO PAMĚTI](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Uživatel vytvořit místní sdílenou složkou informace databáze a konfigurace. Musí být přístupné s účtem služby SQL Service v SQLVM a SQLAOG1 oprávnění k zápisu.

    ![PŘÍMÝ PŘÍSTUP DO PAMĚTI](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso vybere přihlášení, které mají být migrovány a spustí migrace. Po dokončení DMA ukazuje migrace jako úspěšné.

    ![PŘÍMÝ PŘÍSTUP DO PAMĚTI](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Jejich ověřte, zda databáze je spuštěna **SQLAOG1**.

    ![PŘÍMÝ PŘÍSTUP DO PAMĚTI](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS připojení k virtuální počítač místní SQL Server přes připojení site-to-site VPN mezi Azure a Contoso datacenter a potom provede migraci databáze.

## <a name="step-7-protect-the-database"></a>Krok 7: Chránit databázi

S databází aplikace spuštěné v **SQLAOG1**, Contoso teď mohou chránit pomocí skupin dostupnosti AlwaysOn. Konfigurace Alwayson pomocí aplikace SQL Management Studio a pak mu přiřaďte naslouchací proces použití clusteringu Windows. 

### <a name="create-an-alwayson-availability-group"></a>Vytvořit skupinu dostupnosti AlwaysOn

1. V aplikaci SQL Management Studio, že klikněte pravým tlačítkem na **vždy na vysokou dostupnost** spustit **Průvodce novou skupinou dostupnosti**.
2. V **zadat možnosti**, že název skupiny dostupnosti **SHAOG**. V **vyberte databáze**, vyberou SmartHotel databáze.

    ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. V **zadejte repliky**, přidejte dva uzly SQL jako repliky dostupnosti a nakonfigurovat je pro automatické převzetí služeb při selhání poskytnout synchronním potvrzováním.

     ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. Je potřeba nakonfigurovat naslouchací proces pro skupinu (**SHAOG**) a portu. IP adresa služby Vyrovnávání zatížení pro vnitřní se přidá jako statickou IP adresu (10.245.40.100).

    ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. V **vybrat synchronizaci dat**, umožňují automatická synchronizace replik indexů. Pomocí této možnosti SQL serveru automaticky vytvoří sekundární repliky pro každou databázi ve skupině, takže Contoso nemusíte ručně zálohování a obnovení těchto. Po ověření vytváření skupiny dostupnosti.

    ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso narazili problém při vytváření skupiny. Nejsou Active Directory integrované zabezpečení systému Windows a proto bude třeba udělit oprávnění k přihlášení k SQL pro vytváření rolí clusteru převzetí služeb při selhání se systémem Windows.

    ![Skupiny dostupnosti AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. Po vytvoření skupiny společnosti Contoso můžete zobrazit v aplikaci SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Naslouchací proces nakonfigurovat na clusteru

Jako poslední krok v nastavení nasazení SQL společnosti Contoso nakonfiguruje nástroje pro vyrovnávání zatížení pro vnitřní jako naslouchací proces v clusteru a přináší naslouchací proces online. K tomu používají skript.

![Naslouchací proces clusteru](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Zkontrolujte konfiguraci

Všechna nastavení Contoso nyní mají skupiny funkční dostupnosti v Azure, která používá databázi migrované. Budou to ověřit připojením ke službě Vyrovnávání zatížení interní v SQL Management Studio.

![ILB připojení](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Potřebujete další pomoc?**
- Další informace o vytváření [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) a [naslouchací proces](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Ručně [nastavte cluster pro použití IP adresy služby Vyrovnávání zatížení](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- [Další informace](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2) o vytváření a použití SAS.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Krok 8: Migraci virtuálního počítače pomocí Site Recovery

Spustit rychlou contoso testovací převzetí služeb při selhání a potom migrovat virtuální počítač.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Spuštěná testovací převzetí služeb pomáhá zajistit, že vše funguje podle očekávání před migrací. 

1. Contoso spustí testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovat**).
2. Vyberou **vypnout počítač před zahájením převzetí služeb při selhání**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Testovací převzetí služeb při selhání spouští: 

    - Kontrola předpokladů postupné a zkontrolujte, zda jsou všechny podmínky, požadována pro migraci na místě.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

3. Po dokončení převzetí služeb při selhání se zobrazí na portálu Azure repliky virtuálního počítače Azure. Contoso kontroluje, zda je virtuální počítač odpovídající velikost, byl připojený k síti správné, a zda je spuštěna. 
4. Po ověření, Contoso vyčištění převzetí služeb při selhání a zaznamenejte a uložte jakékoli připomínky. 

### <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. Po ověření, že testovací převzetí služeb fungovala podle očekávání, Contoso vytvořit plán obnovení pro migraci a přidejte WEBVM do plánu.

     ![Plán obnovení](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. Spuštění převzetí služeb při selhání v plánu. Vyberte nejnovější bod obnovení a určit, že Site Recovery se pokuste vypnout virtuální počítač na místě před spuštěním převzetí služeb při selhání.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Po převzetí služeb při selhání budou ověřte, že virtuální počítač Azure zobrazuje podle očekávání na portálu Azure.

    ![Plán obnovení](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Po ověření virtuálního počítače v Azure, po dokončení migrace na dokončení procesu migrace, zastavení replikace pro virtuální počítač a ukončete fakturace Site Recovery pro virtuální počítač.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Aktualizovat připojovací řetězec

Jako poslední krok v procesu migrace Contoso aktualizovat připojovací řetězec aplikace tak, aby odkazoval na databázi migrované systémem SHAOG naslouchací proces. Tato konfigurace se změní na WEBVM nyní běží v Azure.  Tato konfigurace se nachází v souboru web.config aplikace ASP. 

1. Vyhledejte soubor v C:\inetpub\SmartHotelWeb\web.config.  Změnit název serveru, aby odrážela plně kvalifikovaný název domény AOG: shaog.contoso.com.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Po aktualizaci souboru a uložíte, budou restartujte službu IIS na WEBVM. Budou to provést pomocí IISRESET /RESTART z příkazový řádek.
2. Po restartování služby IIS, aplikace je nyní používat databázi systémem SQL MI.


**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštěná testovací převzetí služeb. 
- [Další informace](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) přebírání služeb při selhání do Azure.

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Po migraci SmartHotel aplikace běží ve virtuálním počítači Azure a SmartHotel databáze se nachází v clusteru Azure SQL.

Nyní Contoso potřebuje k dokončení těchto kroků čištění:  

- Odeberte místní virtuální počítače z inventáře vCenter.
- Odeberte virtuální počítače z místní úlohy zálohování.
- Aktualizujte interní dokumentaci zobrazíte nové umístění a IP adresy pro virtuální počítače.
- Zkontrolujte všechny prostředky, které pracují s virtuálními počítači, vyřazení a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.
- Přidejte že dva nové virtuální počítače (SQLAOG1 a SQLAOG2) musí být přidaní do produkční systémy sledování.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznit a zabezpečit jejich novou infrastrukturu.

### <a name="security"></a>Zabezpečení

Tým zabezpečení Contoso zkontroluje WEBVM virtuální počítače Azure, SQLAOG1 a SQLAOG2, chcete-li zjistit jakékoli problémy se zabezpečením. 

- Jejich Zkontrolujte skupiny zabezpečení sítě (Nsg) pro virtuální počítač k řízení přístupu. K zajištění, že pouze povolené k aplikaci přenos dat se používají skupiny Nsg.
- Zvažují zabezpečení dat na disku pomocí Azure Disk Encryption a KeyVault.
- Doporučujeme vyhodnotit transparentní šifrování dat (šifrování TDE) a pak ji povolit na databázi SmartHotel systémem nové AOG SQL. [Další informace](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[Další informace](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupy zabezpečení pro virtuální počítače.

### <a name="backups"></a>Zálohování

Contoso se bude zálohovat data na WEBVM, SQLAOG1 a SQLAOG2 pomocí služby zálohování Azure. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencování a cenově optimalizace

1. Contoso má existující licencování pro jejich WEBVM a bude využívat výhody hybridní Azure.  Převádějí budete existující virtuální počítače Azure využívat výhod této ceny.
2. Contoso povolíte Azure náklady na správu licencované Cloudyn, dceřiné společnosti Microsoft. Je řešení pro správu více cloudové náklady, který vám pomůže využívat a spravovat Azure a dalším prostředkům cloudu.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o Azure náklady na správu. 

## <a name="conclusion"></a>Závěr

V tomto článku Contoso rehosted SmartHotel aplikaci v Azure pomocí migrace front-endu aplikace virtuálních počítačů do Azure pomocí služby Site Recovery. Migrovat databázi aplikace na cluster serveru SQL Server zřízené v Azure a chráněné ve skupině dostupnosti AlwaysOn SQL serveru.

## <a name="next-steps"></a>Další postup

V další článek v řadě jsme zobrazí jak Contoso opětovným hostováním své služby podpory osTicket aplikace se systémem Linux a nasazovat s databázi MySQL.


