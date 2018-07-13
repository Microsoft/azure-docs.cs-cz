---
title: Změna hostitele aplikace Contoso v Azure díky migraci do spravované Instance Azure SQL a virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso ke kolizi místní aplikaci na virtuálních počítačích Azure a spravované Instance Azure SQL
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: raynew
ms.openlocfilehash: 99eda135161a228fde139458de30f5120af55153
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723942"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Migrace Contoso: Změna hostitele aplikace místní virtuální počítače Azure a spravované Instance Azure SQL

V tomto článku se dozvíte, jak společnosti Contoso migruje jeho front-end aplikace SmartHotel virtuálního počítače na virtuální počítače Azure pomocí služby Azure Site Recovery a database aplikace do spravované Instance Azure SQL.

> [!NOTE]
> Azure SQL Managed Instance je aktuálně ve verzi preview.

Tento dokument je čtvrtý série článků, které dokumentují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a řadu scénářů, které ukazují, jak nastavit infrastrukturu migrace a spustit různé druhy migrace. Scénáře jejich složitost v a budeme přidávat další články v čase.


**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
[Článek 3: Posouzení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak společnosti Contoso spuštění posouzení jejich místní dvouvrstvé SmartHotel aplikace spuštěné ve VMware. Jejich posouzení virtuálních počítačů aplikace s [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
Článek 4: Změna hostitele na virtuálních počítačích Azure a spravované instanci SQL (Tento článek) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do Azure. Migraci virtuálního počítače front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a aplikace pomocí databáze [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) služby k migraci do spravované Instance SQL. | K dispozici.
[Článek 5: Změna hostitele na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace pomocí služby Site Recovery pouze virtuální počítače.
[Článek 6: Změna hostitele na virtuálních počítačích Azure a skupiny dostupnosti SQL serveru](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace. Používají Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do skupiny dostupnosti SQL serveru. | K dispozici.
[Článek 7: Změna hostitele Linuxovou aplikaci do virtuálních počítačů Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Site Recovery. | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure serveru MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Site Recovery a na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.

Pokud jste chtěli použít SmartHotel ukázkové aplikace použité v tomto článku, můžete ji stáhnout [githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Místní architektury

Zde je diagram znázorňující aktuální místní infrastrukturou společnosti Contoso.

![Architektura contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso má jeden hlavní datové centrum, které jsou umístěné v města New York východní USA.
- Mají tři další místní větve ve Spojených státech amerických.
- Hlavní datové centrum je připojený k Internetu pomocí Fiber metro ethernetová připojení (500 MB/s).
- Každou větev je místně připojen k Internetu pomocí obchodní třídy připojení s IPSec VPN tunely zpět do hlavního datového centra. To umožňuje jejich celá síť trvale připojený k Internetu a optimalizuje připojení k Internetu.
- Hlavní datové centrum je plně virtualizovaný s VMware. Mají dva virtualizace hostitele ESXi 6.5 spravované přes vCenter Server verze 6.5.
- Contoso používá službu Active Directory pro správu identit a servery DNS v interní síti.
- Běží na virtuálních počítačích VMware řadiče domény v datovém centru. Řadiče domény v místní větve spustit na fyzických serverech.



## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co obchodní chce, aby k dosažení této migraci:

- **Adresa obchodní růst**: Contoso roste a kvůli tomu je tlak na svých místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso je potřeba odebrat nepotřebné postupy a zjednodušte procesy pro jejich vývojáře a uživatele.  Obchodní potřeby IT bude rychlé a ne produkovaného odpadu čas nebo peníze, tím rychlejší doručování na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku.  Nesmí získat způsobem nebo stát obchodní blokování.
- **Škálování**: růstem podniku zvládla úspěšně, musíte zadat Contoso IT systémy, které mohou rozšířit stejným tempem.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci. Tyto cíle se používají k určení nejlepší metody migrace:

- Po dokončení migrace aplikace v Azure by měla mít stejné možnosti výkonu stejně jako dnes v jejich místní prostředí VMWare.  Přesun do cloudu, neznamená, že výkon aplikace je méně kritické.
- Contoso nechce investovat do této aplikace.  Je kritická a důležitá pro podnik, ale v aktuální podobě chtějí jednoduše přesunout do cloudu je.
- Po migraci oznámení o aplikaci, byste měli minimalizovat úlohy správy databáze.
- Contoso nechce používat Azure SQL Database pro tuto aplikaci a hledá alternativy.

## <a name="proposed-architecture"></a>Navrhované architektury

V tomto scénáři:

- Contoso chce migrovat své dvouvrstvé místní cestovní aplikaci.
- Tato aplikace je rozvrstvená na dva virtuální počítače (WEBVM a SQLVM) a umístěný na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Jejich migrovat databázi aplikace (SmartHotelDB) do Azure SQL Managed instance.
- Jejich migraci místních virtuálních počítačů VMware na Virtuálním počítači Azure.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.

![Architektura scénáře](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Služba správy databáze](https://docs.microsoft.com/azure/dms/dms-overview) | DMS umožňuje bezproblémovou migraci z několika databázových zdrojů na platformu Azure data, s minimálními výpadky. | Další informace o [podporované oblasti](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) DMS a get [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance je spravovanou databázovou službu, která představuje plně spravovanou instanci SQL serveru v cloudu Azure. Sdílí stejný kód s nejnovější verzí služby databázového stroje SQL serveru a obsahuje nejnovější funkce, vylepšení výkonu a opravy zabezpečení. | V Azure pomocí Azure SQL Database Managed instance platit poplatky stanovené podle kapacity. [Další informace](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migraci a zotavení po havárii pro virtuální počítače Azure a místních virtuálních počítačů a fyzických serverů.  | Při replikaci do Azure se účtují poplatky za úložiště Azure.  Virtuální počítače Azure jsou vytvářeny a účtovat poplatky, pokud dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) nákladů a ceny.

 

## <a name="migration-process"></a>Proces migrace

Contoso se migrace web a data úrovní SmartHotel aplikace do Azure.

1. Contoso je již jeho infrastruktury Azure na místě, tak stačí přidat několik konkrétní komponenty Azure v tomto scénáři.
2. Datová vrstva proběhne pomocí Data Migration Service (DMS).  DMS připojuje k virtuálnímu počítači v místním SQL serveru přes připojení VPN typu site-to-site mezi Contoso datacentrem a Azure a potom provede migraci databáze.
3. Webové vrstvy se budou migrovat pomocí migrace lift and shift pomocí Azure Site Recovery. To bude znamenat Příprava VMware v místním prostředí, nastavení a povolení replikace a migrace virtuálních počítačů pomocí převzetí služeb při selhání je do Azure.

     ![Architektura migrace](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>Požadavky

Zde je, co Contoso (a) je pro tento scénář.

**Požadavky** | **Podrobnosti**
--- | ---
**Registrace ve verzi preview** | Budete muset být zaregistrovaná v SQL Managed Instance omezené veřejné verzi Preview. Budete potřebovat předplatné Azure za účelem [zaregistrovat](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Registrace může trvat několik dnů nebo dokončení, proto se ujistěte, můžete provést před zahájením nasazení tohoto scénáře.
**Předplatné Azure** | Měli jste už vytvořili odběr při provádění posouzení v první článku v této sérii. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site recovery (v místním prostředí)** | Vaše místní vCenter server by měl být spuštěn verze 5.5, 6.0 nebo 6.5<br/><br/> Hostitele ESXi verze 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware běží na hostiteli ESXi.<br/><br/> Virtuální počítače musí splňovat [požadavky služby Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Podporované [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurace.
**DMS** | Pro systém DMS, je nutné [kompatibilní na místním zařízení VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Musíte být schopni konfigurovat místní zařízení VPN. Musí mít veřejnou adresu IPv4 přístupnou zvenčí a adresa nesmí být umístěné za zařízením NAT a.<br/><br/> Ujistěte se, že budete mít přístup k místní databázi SQL serveru.<br/><br/> Brána Windows Firewall by měla moct přistupovat k source databázového stroje. [Další informace](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Pokud je brána firewall před počítači databáze, přidejte pravidla pro povolení přístupu k databázi a soubory prostřednictvím SMB portu 445.<br/><br/> Pověření používaná k připojení ke zdrojovému serveru SQL a cílit na Managed Instance musí být členy role serveru sysadmin.<br/><br/> Budete potřebovat síť sdílet v místní databázi, DMS můžete použít k zálohování zdrojové databáze.<br/><br/> Ujistěte se, zda má účet služby spuštění zdrojové instanci SQL serveru oprávnění k zápisu ve sdílené síťové složce.<br/><br/> Poznamenejte Windows uživatele (a heslo), který má oprávnění Úplné řízení na sdílené síťové složce. Azure Database Migration Service zosobňuje tyto přihlašovací údaje uživatele pro nahrání záložních souborů do kontejneru úložiště Azure.<br/><br/> Proces instalace systému SQL Server Express nastaví protokolu TCP/IP na **zakázané** ve výchozím nastavení. Ujistěte se, zda je povoleno.


## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob Contoso se to nastavení nasazení:

> [!div class="checklist"]
> * **Krok 1: Nastavení spravované Instance SQL Azure**: potřebují předem vytvořené managed instance, do kterého budete migrovat místní databázi systému SQL Server.
> * **Krok 2: Příprava DMS**: potřebují k registraci poskytovatele migrace databáze, vytvořte instanci a potom vytvořte projekt DMS. Je třeba nastavit SA identifikátor URI pro systém DMS. Sdílený přístupový podpis (SAS) identifikátor URI (Uniform Resource) poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště, takže můžete udělit omezená oprávnění k objektům úložiště. Nastavení se identifikátor URI SAS, aby DMS přístup kontejner účtu úložiště, do kterého služba nahraje soubory zálohování systému SQL Server.
> * **Krok 3: Příprava Azure Site Recovery**: pro Site Recovery, musí vytvořit účet Azure storage k ukládání replikovaných dat a vytvoříte trezor služby Recovery Services.
> * **Krok 4: Příprava VMware v místním prostředí pro Site Recovery**: Contoso připravit účty pro virtuální počítač zjišťování a instalaci agenta a připravte se připojit k virtuálním počítačům Azure po převzetí služeb při selhání.
> * **Krok 5: Replikace virtuálních počítačů**: nastavení replikace, jejich konfigurace zdrojového a cílového prostředí Site Recovery, nastavení zásady replikace a spustit replikaci virtuálních počítačů do Azure storage.
> * **Krok 6: Migrace databáze pomocí DMS**: Nyní můžete migraci databáze.
> * **Krok 7: Migrace virtuálních počítačů pomocí Site Recovery**: převzetí služeb při selhání testu Ujistěte se, že všechno funguje a potom spusťte úplnou převzetí služeb při selhání k migraci virtuálních počítačů do Azure.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>Krok 1: Příprava spravované Instance Azure SQL

Pokud chcete nastavit Azure SQL Managed Instance, Contoso potřebuje podsítě:

- Podsíť musí být vyhrazená. Musí být prázdný a nesmí obsahovat jiné cloudové služby, a nesmí se jednat o podsíť brány.
- Po vytvoření spravované instance nesmí přidat prostředky do ní.
- Podsítě nesmí obsahovat skupinu zabezpečení sítě s ním spojená.
- Podsíť musí mít uživatelem směrovací tabulky (UDR) 0.0.0.0/0 dalšího segmentu směrování Internetu jako pouze trasy přiřazenou. 
- Vlastní DNS nepovinné: Pokud je vlastní DNS zadaná ve virtuální síti, Azure rekurzivní překladače IP adresu (například adresy 168.63.129.16) musí být přidaný do seznamu. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Podsítě nesmí mít koncový bod služby (storage nebo SQL) s ním spojená. Koncové body služby je třeba zakázat ve virtuální síti.
- Podsíť musí mít minimálně 16 IP adres. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) o určení velikosti spravované instance podsítě.
- Ve svém hybridním prostředí je potřeba vlastním nastavení DNS. Contoso se konfigurace nastavení DNS použít jeden nebo více serverů Azure DNS. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) o vlastním nastavení DNS.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Nastavení virtuální sítě pro spravovanou instanci

Virtuální síť společnosti Contoso nastaví takto: 

1. Contoso vytvoří novou virtuální síť (VNET-SQLMI EU2) v primární oblasti USA – východ 2, ve skupině prostředků ContosoNetworkingRG.
2. Contoso přiřadí adresní prostor 10.235.0.0/24, zajištění, že se rozsah nepřekrývá s jinými sítěmi v podnikové síti Contoso.
2. Dvě podsítě, přidejte k síti:
    - SQLMI-DS-EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29). Tato podsíť se použije k připojení adresáře do spravované instance (SQLMI).

    ![Spravované Instance sítě](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Po nasazení virtuální sítě a podsítě Contoso partnerů sítě následujícím způsobem:

    - Partnerské virtuální sítě – SQLMI-EUS2 s virtuální sítí EUS2 centra (hub virtuální síť v oblasti východní USA 2).
    - Partnerské virtuální sítě – SQLMI-EUS2 s připojení typu VNET-PROD-EUS2 (produkční sítě).

    ![Vnet peering](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso nastaví vlastní nastavení služby DNS. DNS se nejprve přejděte na svých řadičích domény Azure. Azure DNS, bude sekundární. Řadiče domény Contoso Azure jsou umístěny následovně:

    - Umístěný v podsíti PROD. řadič domény EUS2, v oblasti východní USA 2 produkční sítě (VNET-PROD-EUS2).
    - Adresa CONTOSODC3: 10.245.42.4
    - Adresa CONTOSODC4: 10.245.42.5
    - Azure DNS překladač: 168.63.129.16

     ![Síť DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**Potřebujete další pomoc?**

- [Získejte přehled](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) spravované instance SQL Azure.
- [Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) o vytvoření virtuální sítě pro spravovanou instanci SQL.
- [Další informace o](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) nastavení partnerského vztahu.
- [Další informace o](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) aktualizace nastavení Azure AD DNS.



### <a name="set-up-routing"></a>Nastavení směrování

Managed Instance je umístěn v privátní virtuální sítě, proto Contoso potřebuje směrovací tabulku pro komunikaci s Azure Management Service. Pokud nemůže komunikovat se službou, která ho spravuje, přestane být přístupný.

- Směrovací tabulka obsahuje sadu pravidel (trasy), která určuje, jak se mají směrovat pakety odeslané z Managed Instance ve virtuální síti.
- Směrovací tabulka je spojené s podsítí, ve kterých jsou nasazené spravované instance. Jednotlivých paketů opuštění podsítě se určují na základě tabulky směrování přidružené.
- Podsíť může být pouze přidružený k jedné směrovací tabulce.
- Nejsou žádné další poplatky za vytvoření směrovací tabulky ve službě Microsoft Azure.

1. Contoso vytvoří tabulku trasy definované uživatelem. Směrovací tabulka se vytvoří ve skupině prostředků ContosoNetworkingRG.

    ![Tabulka směrování](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Chcete-li po nasazení směrovací tabulky (MIRouteTable) v souladu s požadavky Managed Instance, Contoso, přidejte trasu s předponou adresy 0.0.0.0/0, a **typem dalšího segmentu směrování** nastavena na **Internet**.

    ![Předpona trasy tabulky](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso směrovací tabulku přidružit podsíť SQLMI-DB-EUS2 (v síti VNET-SQLMI EUS2). 

    ![Směrovací tabulky podsítě](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) trasy pro spravovanou instanci nastavení.

### <a name="create-a-managed-instance"></a>Vytvoření spravované instance

Contoso můžete zřídit teď SQL Database Managed Instance.

1. Protože Managed Instance obsluhuje obchodní aplikace, Contoso jejím nasazením v jejich primární oblasti USA – východ 2, ve skupině prostředků ContosoRG 
2. Výběrem cenové úrovně a velikost výpočetních a úložiště pro instanci. [Další informace](https://azure.microsoft.com/pricing/details/sql-database/managed/) informace o cenách.

    ![Spravovaná instance](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Po nasazení je Managed Instance, se zobrazí dvě nové prostředky ve skupině prostředků ContosoRG:

    - Virtuální cluster v případě, že máte více spravované instance
    - SQL Server Managed Instance. 

    ![Spravovaná instance](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) zřizování Managed Instance.

## <a name="step-2-prepare-dms"></a>Krok 2: Příprava DMS

Příprava DMS Contoso je potřeba udělat několik věcí, které:

- Registrace poskytovatele DMS v Azure
- DMS poskytnout přístup k Azure storage pro nahrávání záložní soubory použít k migraci databáze. To udělat tak, že vytvoříte kontejner úložiště objektů blob a generovat sdílený přístupový podpis (SAS) identifikátor URI pro něj. 
- Vytvořte projekt DMS.


Proveďte následující kroky:

1. Společnosti Contoso, zaregistrujte poskytovatele migrace databáze v rámci svého předplatného.
    ![Zaregistrujte DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Vytvořte kontejner objektů blob storage a vygenerovat identifikátor URI SAS, tak, aby k němu mají přístup DMS.

    ![IDENTIFIKÁTOR URI SAS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Nakonec vytvořte instanci DMS. 

    ![DMS instance](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso umístí DMS instance PROD. řadič domény EUS2 podsítě virtuální sítě VNET-PROD-řadiče domény – EUS2.
    - Umístěte ho došlo protože musí být ve virtuální síti s přístupem k místní virtuální počítač SQL Server přes bránu VPN.
    - Připojení typu VNET-PROD-EUS2 partnerský vztah virtuální sítě-HUB-EUS2 a může používat vzdálené brány.  Tím se zajistí, že podle potřeby můžete komunikovat DMS.

        ![DMS sítě](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**Potřebujete další pomoc?**
- [Další informace o](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) nastavení DMS.
- [Další informace](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) o vytvoření a použití SAS.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Krok 3: Příprava Azure pro službu Site Recovery

Existuje mnoho Azure prvků, které Contoso potřebuje, aby nastavení Site Recovery k migraci jejich webová vrstva virtuálního počítače (WEBMV)

- Převzetí služeb při selhání virtuální síť, ve kterém jsou umístěny prostředky.
- Účet služby Azure storage k ukládání replikovaných dat. 
- Trezor služby Recovery Services v Azure.

Nastavené Site Recovery následujícím způsobem:

1. Protože virtuální počítač je webový front-end aplikace SmartHotel, bude instalace neúspěšná selhání virtuálního počítače do své existující produkční sítě (VNET-PROD-EUS2) a podsíti (PROD-FE – EUS2), v primární oblasti USA – východ 2. Nastavují tuto síť při jejich [nasadili infrastrukturu Azure](contoso-migration-infrastructure.md)
2. Vytvoří účet úložiště Azure (contosovmsacc20180528). Jejich používáte účet general purpose s storage úrovně standard a replikací LRS.

    ![Site Recovery úložiště](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Pomocí účtu sítě a úložiště na místě můžete Contoso teď vytvoříte trezor (ContosoMigrationVault) a jeho následné uložení do skupiny prostředků ContosoFailoverRG v primární oblasti USA – východ 2.

    ![Trezor služby Recovery Services](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Příprava VMware v místním prostředí pro Site Recovery

Příprava VMware pro Site Recovery, zde je, co Contoso je potřeba udělat:

- Příprava účtu na vCenter serveru nebo hostiteli vSphere ESXi, pro automatizaci zjišťování virtuálních počítačů.
- Příprava účtu, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware, který chcete replikovat.
- Příprava místních virtuálních počítačů, tak, aby se mohly připojit k virtuálním počítačům Azure po jejich vytvoření po převzetí služeb při selhání.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automatické zjišťování virtuálních počítačů. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Budete potřebovat účet, který může spouštět operace, jako je vytváření a odebírání disků a zapnutí na virtuálních počítačích.

Účet contoso nastaví takto:

1. Vytvoří roli na úrovni vCenter.
2. Přiřadí potřebná oprávnění k této roli.

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) vytvoření a přiřazení role pro automatické zjišťování.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na virtuálním počítači, který chcete replikovat, musí být nainstalovaná služba Mobility.

- Když povolíte replikaci pro virtuální počítač, může Site Recovery poskytnout automatickou nabízenou instalaci této součásti.
- Pro automatické nabízené instalace musíte připravit účet, který Site Recovery použije pro přístup k virtuálnímu počítači.
- Tento účet zadáte při nastavování replikace v konzole Azure.
- Budete potřebovat účet domény nebo místní účet s oprávněními k instalaci na virtuálním počítači.

**Potřebujete další pomoc**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) vytvoření účtu pro nabízenou instalaci služby Mobility.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání do Azure Contoso chce mít možnost se připojit k replikované virtuální počítače v Azure. K tomuto účelu se několik věcí, které potřebují dělat na místní virtuální počítač dřív, než spustí migrace: 

1. Pro přístup prostřednictvím Internetu povolte protokol RDP na místním virtuálním počítači před převzetí služeb při selhání a ujistěte se, že jsou přidaná pravidla TCP a UDP pro **veřejné** profil a že je povolený protokol RDP v **brány Windows Firewall**  >  **Povolené aplikace** pro všechny profily.
2. Pro přístup prostřednictvím své sítě VPN site-to-site, povolte na místním počítači protokol RDP a povolit RDP v **brány Windows Firewall** -> **povolené aplikace a funkce** pro **domény a Privátní** sítě.
3. Nastavují zásada SAN operačního systému na virtuálním počítači s místními **OnlineAll**.

Kromě toho při spuštění převzetí služeb při selhání potřebují zkontrolujte následující:

- Měla by existovat žádné čekající aktualizace Windows na virtuálním počítači při spuštění převzetí služeb při selhání. Pokud nejsou, nebudou moct přihlásit k virtuálnímu počítači, dokud se aktualizace nedokončí.
- Po převzetí služeb při selhání, by měl zkontrolovat **Diagnostika spouštění** zobrazíte snímek obrazovky virtuálního počítače. Pokud to nepomůže, by měl zkontrolovat, že je virtuální počítač spuštěný a přečtěte si tyto [tipy pro řešení potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Krok 5: Replikace místních virtuálních počítačů do Azure pomocí Site Recovery

Společnosti Contoso musí před spuštěním migrace do Azure, nastavení replikace a povolení replikace pro jejich místní virtuální počítač.

### <a name="set-a-replication-goal"></a>Nastavení cíle replikace

1. V trezoru v části název_trezoru (ContosoVMVault) nastavují cíle replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu**.
2. Určí, že jsou jejich počítače nacházejí na místních, že jsou virtuální počítače VMware. proto, že chcete replikovat do Azure.

    ![Cíl replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Chcete-li pokračovat, musí potvrdit, že nebude dokončena plánování nasazení tak, že vyberete **Ano, mám to hotové**. V tomto nasazení Contoso jsou pouze migrace jednoho virtuálního počítače které nevyžadují plánování nasazení.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nyní musí nakonfigurovat jejich zdrojového prostředí. Provedete to tak, stáhněte šablonu OVF a použít k nasazení konfiguračního serveru a jeho přidružených součásti jako vysoce dostupný, místní virtuální počítač VMware. Součásti na serveru zahrnují:

- Převzetí služeb při selhání neprobíhá automaticky.
- Zahájení převzetí služeb při selhání s jedním kliknutím na portálu, nebo můžete použít  Powershellu k aktivaci převzetí služeb při selhání. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.
- Po vytvoření a spuštění virtuálního počítače konfiguračního serveru Contoso můžete ho zaregistrovat v trezoru.


Contoso následujícím způsobem proveďte tyto kroky:

1. Šablona OVF stáhnou z webu Azure portal (**připravit infrastrukturu** > **zdroj** > **konfigurační Server**).
    
    ![Stáhněte si OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Jejich import šablony do VMware. Tím vytvoříte a nasadíte virtuální počítač.

    ![Šablona OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Při jejich převodu na virtuálním počítači poprvé, se spustí do prostředí instalace Windows serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce. Při prvním přihlášení ve výchozím nastavení spustí nástroj pro konfiguraci Azure Site Recovery.
5. V nástroji, zadejte název, který chcete použít při registraci konfiguračního serveru v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po připojení se naváže, vyberou **přihlášení**, přihlaste se k předplatnému Azure. Přihlašovací údaje musí mít přístup k trezoru, ve které bude konfigurační server zaregistrovaný. 

    [Registrace konfiguračního serveru](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Nástroj provede několik úloh konfigurace a pak restartuje počítač. Přihlášení k počítači znovu, a automaticky spustí Průvodce správou konfiguračního serveru.
8. V průvodci, vyberte síťovou kartu pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení konfigurace.
9. Vyberte předplatné, skupinu prostředků a trezor, do kterého chcete konfigurační server zaregistrovat.
        ![Trezor](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. Stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. Nakonec ověří nastavení serveru.
11. Po ověření, zadejte plně kvalifikovaný název domény nebo IP adresa serveru vCenter nebo vSphere hostiteli. Ponechte výchozí port a zadejte popisný název pro vCenter server v Azure.
12. Je třeba zadat účet, který vytvořili dříve, tak, aby Site Recovery může automaticky zjistit virtuální počítače VMware, které jsou dostupné pro replikaci. 
13. Určí pověření pro automatické instalaci služby Mobility po povolení replikace. Pro počítače s Windows účet potřebuje oprávnění místního správce na virtuálních počítačích. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Po dokončení registrace na webu Azure Portal, Contoso double ověří, že konfigurační server a VMware server jsou uvedeny na **zdroj** stránky v trezoru. Zjišťování může trvat 15 minut nebo déle. 
8. Site Recovery se pak připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavení cíle

Nyní Contoso potřebuje ke konfiguraci cílové prostředí replikace.

1. V **připravit infrastrukturu** > **cílové**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a síť v zadané cílové.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Po zdroje a cíle jsou nastavené, Contoso je připravená k vytvoření zásady replikace a přidružit konfigurační server.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružit**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota cíle bodu obnovení**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí hodnotu 24 hodin. Tato hodnota určuje, jak dlouho je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímků konzistentní vzhledem k**. Výchozí jednu hodinu. Tato hodnota určuje frekvenci, s jakou se vytvořit snímky konzistentní s aplikací.
 
        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružte zásady replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**Potřebujete další pomoc?**

- Můžete si přečíst Úplný návod tohoto všechny tyto kroky v [nastavit zotavení po havárii pro virtuální počítače VMware v místním](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici při [zdrojové prostředí nastavíte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfiguračního serveru](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [konfigurace nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Povolení replikace

Contoso nyní můžete spustit replikaci WebVM.

1. V **replikovat aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Indikuje, že chtějí povolit virtuální počítače, vyberte vCenter server a konfigurační server.

 ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Teď zadejte nastavení cíle, včetně skupiny prostředků a síť, ve kterém virtuálním počítači Azure budou umístěné po převzetí služeb při selhání a účet úložiště, ve kterém se ukládají replikovaná data.

     ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso vybere WebVM pro replikaci. Když povolíte replikaci pro něj Site Recovery nainstaluje službu Mobility na každém virtuálním počítači. 

    ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso ověří, že je vybraná správná zásada replikace a umožňuje replikaci pro WEBVM. Sledují průběh replikace v **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
6. V **Essentials** Contoso na webu Azure Portal, můžete zobrazit strukturu pro virtuální počítače, které se replikují do Azure.

    ![Zobrazení infrastruktury](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**Potřebujete další pomoc?**

Můžete si přečíst Úplný návod tohoto všechny tyto kroky v [povolit replikaci](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>Krok 6: Migrace databáze pomocí DMS

Contoso je potřeba vytvořit projekt DMS a migrovat databáze.

### <a name="create-a-dms-project"></a>Vytvoření projektu DMS
1. Vytvoří projekt DMS. Určí typ zdrojového serveru jako SQL Server a cíl jako Azure SQL Database Managed Instance.

     ![DMS projektu](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Po vytvoření projektu, otevře se Průvodce migrací.

### <a name="migrate-the-database"></a>Migrace databáze 

1. V Průvodci migrací určuje Contoso zdrojového virtuálního počítače, na kterém se nachází v místní databázi a přihlašovací údaje pro přístup k ní.

    ![DMS zdroje](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Výběrem databáze, kterou chcete migrovat (SmartHotel.Registration).

    ![DMS zdrojové databáze](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Jako cíl určí název spravované Instance v Azure a přihlašovací údaje pro přístup.

    ![Cíl DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Potom v **+ nová aktivita** > **spustit migraci**, určují nastavení pro spuštění migrace:
    - Zdroj a cíl přihlašovací údaje.
    - Databáze pro migraci.
    - Sdílené síťové složce, které vytvořili na místním virtuálním počítači. DMS přebírá zálohování zdroje k této sdílené složce.
        - Účet služby spuštění zdrojového systému SQL Server instance musí mít oprávnění k zápisu v této sdílené složce.
        - Zadejte plně kvalifikovaný název domény cestu ke sdílené složce.
    - Identifikátor URI SAS, který poskytuje přístup ke kontejneru účtu úložiště, ke kterému služba nahraje soubory zálohy pro migraci DMS.

        ![Nastavení DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Uložit migrace a spustíme ji.
6. V **přehled**, i sledování stavu migrace.

    ![Monitorování DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Po dokončení migrace, ověřte, že cílová databáze existují na spravované instanci.

    ![Monitorování DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>Krok 7: Migrace virtuálního počítače pomocí služby Site Recovery

Contoso spustí rychlé testovací převzetí služeb při selhání a potom migrujete virtuální počítač.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Před migrací WEBVM, testovací převzetí služeb pomáhá se ujistit, že vše funguje podle očekávání. 

1. Spuštění testovací převzetí služeb při selhání, k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovaný**).
2. Vyberou **před spuštěním převzetí služeb při selhání vypnout počítač**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před aktivací převzetí služeb. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Spuštění testovacího převzetí služeb při selhání: 

    - Kontrola spuštění, abyste měli jistotu, že jsou všechny podmínky pro migraci na místě požadavků.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.
3. Po dokončení převzetí služeb se zobrazí na webu Azure Portal repliku virtuálního počítače Azure. Přihlášením ke vše správně fungovalo. Je virtuální počítač odpovídající velikost, je připojený ke správné síti a je spuštěná. 
4. Po ověření testu převzetí služeb, Contoso vyčistí převzetí služeb při selhání a záznamy a uloží jakékoli připomínky. 

### <a name="migrate-the-vm"></a>Migrace virtuálního počítače

1. Po ověření, zda testovací převzetí služeb fungovala podle očekávání, vytvoří Contoso plán obnovení pro migraci. WEBVM si přidají do plánu.

     ![Plán obnovení](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Potom spuštění převzetí služeb při selhání v plánu. Vyberte nejnovější bod obnovení a určit, že Site Recovery se měli pokusit vypněte místní virtuální počítač před aktivací převzetí služeb.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Po převzetí služeb při selhání Contoso ověřte, že se virtuální počítač Azure zobrazuje podle očekávání na webu Azure Portal.

   ![Plán obnovení](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Po ověření virtuálních počítačů v Azure, provést migraci dokončit proces migrace zastavit replikaci pro virtuální počítač a zastavte fakturace služby Site Recovery pro virtuální počítač.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Aktualizujte připojovací řetězec

Posledním krokem v procesu migrace aktualizuje Contoso připojovací řetězec aplikace tak, aby odkazoval na migrované databáze spuštěná na jejich MI SQL.

1. Na webu Azure Portal, které se vyskytnout připojovací řetězec kliknutím **nastavení** > **připojovací řetězce**.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Řetězec, aktualizujte uživatelské jméno a heslo SQL Managed Instance.
3. Po dokončení konfigurace řetězec nahrazují aktuálního připojovacího řetězce v souboru web.config aplikace.
4. Po aktualizaci souboru a jeho uložení, restartujte službu IIS na WEBVM. Udělají se **IISRESET /RESTART** z příkazového řádku.
5. Po restartování služby IIS aplikace pomocí databáze spuštěná na spravované instanci SQL.
6. V tomto okamžiku můžete Contoso vypne SQLVM počítače místní a migrace je dokončena.

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštění testovací převzetí služeb při selhání. 
- [Přečtěte si](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) převzetí služeb při selhání do Azure.

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Migrace byla dokončena SmartHotel aplikace běží na Virtuálním počítači Azure a je k dispozici na spravované instanci Azure SQL SmartHotel database.  

Nyní Contoso je potřeba udělat trochu pořádek následujícím způsobem:  

- Odebrání počítače WEBVM z inventáře vCenter.
- Odeberte počítač SQLVM z inventáře vCenter.
- Odeberte WEBVM a SQLVM z místní úlohy zálohování.
- Aktualizace dokumentace interní zobrazit nové umístění, IP adresu pro WEBVM.
- Odeberte SQLVM dokumentaci ke službě. Případně se může označte ji zobrazit jako odstraněný a už ve virtuálním počítači inventáře.
- Zkontrolujte všechny prostředky, které pracují s Vyřazená z provozu virtuálních počítačů a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

Bezpečnostní tým Contoso Přehled virtuálních počítačů Azure a spravované instanci SQL, chcete-li zjistit všechny problémy se zabezpečením pomocí jeho implementace.

- Zkontrolujte tyto skupiny zabezpečení sítě (Nsg) pro virtuální počítač pro řízení přístupu. Skupiny zabezpečení sítě se používají k zajištění, že lze předat pouze provoz povolený do aplikace.
- Jsou také zvážení, zabezpečení dat na disku pomocí Azure Disk Encryption a Azure Key Vaultu.
- Umožňují detekce hrozeb na SQL spravované Instance (SQLMI). Se pošle oznámení na jejich zabezpečení týmu nebo služby podpory systému chcete otevřít lístek, pokud se zjistí hrozbu. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Zabezpečení spravované instance](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[Přečtěte si další](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupech zabezpečení pro virtuální počítače.

### <a name="backups"></a>Zálohování
Contoso se bude zálohovat data na WEBVM pomocí služby Azure Backup. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

1. Contoso má existující licencování pro WEBVM a bude využít zvýhodněné hybridní využití Azure.  Převádějí existujícím virtuálním počítači Azure využít tento cenový model.
2. Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení pro správu více cloudových nákladů, které pomáhá využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management. 


## <a name="conclusion"></a>Závěr

V tomto článku Contoso rehosted SmartHotel aplikace v Azure do Azure pomocí služby Site Recovery migrací aplikace front-endový virtuální počítač. Proto se migrovat místní databázi do Azure SQL Managed Instance pomocí DMS.

## <a name="next-steps"></a>Další postup

V další článek v sérii vám ukážeme jak Contoso opětovným hostováním SmartHotel aplikace na virtuální počítače Azure pomocí služby Azure Site Recovery pouze.

