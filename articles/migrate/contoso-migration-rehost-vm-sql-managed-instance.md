---
title: Opětovným hostováním Contoso aplikaci v Azure pomocí migrace na virtuální počítač Azure a Azure SQL spravované Instance | Microsoft Docs
description: Zjistěte, jak Contoso opětovném hostování aplikace místně na virtuálních počítačích Azure a spravovat instanci SQL Azure
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: c7dc9e8406494739aa5d8f21397a606e0b74a617
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301248"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Migrace Contoso: opětovným hostováním místní aplikace na virtuálních počítačích Azure a spravovat instanci SQL Azure

Tento článek ukazuje, jak Contoso migruje jeho SmartHotel aplikace front-end virtuálních počítačů k virtuálním počítačům Azure pomocí služby Azure Site Recovery a databáze aplikace do Azure SQL spravované Instance.

> [!NOTE]
> Spravované Instance Azure SQL je aktuálně ve verzi preview.

Tento dokument je čtvrtý v řadě články, které dokumentů, jak fiktivní společnosti Contoso migruje jeho místních prostředků do cloudu Microsoft Azure. Řada obsahuje základní informace a řadu scénářů, které ukazují, jak nastavit infrastrukturu migrace a spustit různé typy migrace. Scénáře růst v složitost a jsme přidali další články v čase.


**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, řady článku a ukázkových aplikací, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak Contoso připraví jeho místní a infrastrukturu Azure pro migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
[Článek 3: Vyhodnocení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak Contoso spouští posouzení své místní aplikace SmartHotel dvouvrstvá běžící ve VMware. Jejich vyhodnocení aplikace virtuálních počítačů s [Azure migrovat](migrate-overview.md) služby a aplikace databáze systému SQL Server s [Azure databáze migrace pomocníka](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
Článek 4: Opětovným hostováním na virtuálních počítačích Azure a instanci SQL spravované (v tomto článku) | Ukazuje, jak Contoso migruje SmartHotel aplikace do Azure. Jejich migraci virtuálních počítačů front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace pomocí [migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview) služby migrovat do spravované Instance SQL. | K dispozici.
[Článek 5: Pro virtuální počítače Azure opětovným hostováním](contoso-migration-rehost-vm.md) | Ukazuje, jak Contoso migruje aplikace SmartHotel virtuální počítače pouze pomocí Site Recovery.
[Článek 6: Opětovným hostováním do Azure virtuálních počítačů a skupin dostupnosti SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace. Site Recovery používají k migraci aplikace virtuální počítače a migraci databáze službu, kterou chcete migrovat databázi aplikace do skupiny dostupnosti SQL Server. | K dispozici.
[Článek 7: Opětovným hostováním Linux aplikace pro virtuální počítače Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso migruje aplikace osTicket Linux k virtuálním počítačům Azure pomocí Site Recovery. | K dispozici.
[Článek 8: Opětovným hostováním Linux aplikace pro virtuální počítače Azure a Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak Contoso migruje aplikace osTicket Linux k virtuálním počítačům Azure pomocí Site Recovery a na instanci serveru MySQL Azure pomocí MySQL Workbench. | K dispozici.

Pokud chcete použít SmartHotel ukázková aplikace používá v tomto článku, si můžete stáhnout z [githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Architektura na místě

Zde je diagram zobrazující aktuální místní infrastrukturu společnosti Contoso.

![Architektura contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso má jeden hlavní datovém centru, které jsou umístěné v městě New York ve východní USA.
- Mají tři další místní pobočky v USA.
- Hlavní datového centra je připojený k Internetu s Fiber metro připojení k síti ethernet (500 MB/s).
- U každé větve je místně připojen k Internetu pomocí připojení třída firmy, tunelových propojení IPSec pro síť VPN zpět do hlavní datového centra. To umožňuje jejich celá síť být trvale připojené a optimalizuje připojení k Internetu.
- Hlavní datového centra je plně virtualizovaného s VMware. Mají dva virtualizace hostitele ESXi 6.5 spravovanou vCenter Server verze 6.5.
- Společnost Contoso využívá služby Active Directory pro správu identit a servery DNS v interní síti.
- Běží na virtuálních počítačích VMware řadiče domény v datovém centru. Řadiče domény v místní větví spustit na fyzických serverech.



## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chce firmy dosáhnout s této migrace:

- **Adresa růst podniku**: Contoso roste a výsledkem je tlak na svých místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso musí odebrat nepotřebné postupy a zefektivnit procesy pro jejich vývojářům a uživatelům.  Obchodním potřebám IT rychlá a není nakládání s čas nebo peníze, tím rychleji doručováním na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT musí být rychlejšího potřebám firmy. Musí být schopné reagovat rychleji, než změny v marketplace, chcete-li povolit úspěch v globálním hospodářství.  Nemůže získat způsobem, ani stát otevíraných oken firmy.
- **Škálování**: růstem firmy úspěšně Contoso IT musí poskytnout systémy, které se můžou růst stejným tempem.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudu Contoso má připnuli dolů cíle pro migraci. Tyto cíle se používají k určení nejlepší metody migrace:

- Po migraci aplikaci v Azure měli stejné možnosti výkonu jako dnes ve svém prostředí VMWare místně.  Přechodu do cloudu nebude znamenat, že je aplikace výkonu méně kritický.
- Contoso nechce investovat do této aplikace.  Je kritická a důležitá pro jejich podnikání, ale v současné podobě chtějí jednoduše přesunout ho jako je do cloudu.
- Úlohy správy databáze by měl být minimální po migraci aplikace.
- Contoso nechce používat Azure SQL Database pro tuto aplikaci a hledá alternativy.

## <a name="proposed-architecture"></a>Navrhované architektura

V tomto scénáři:

- Contoso chce migrovat své aplikace dvouvrstvá místní cesta.
- Víceúrovňová mezi dva virtuální počítače (WEBVM a SQLVM) a aplikace umístěný na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Spravuje prostředí VMware vCenter Server verze 6.5 (**vcenter.contoso.com**), spuštěné na virtuálním počítači.
- Databázi aplikace (SmartHotelDB) se migrovat do Azure SQL spravované instance.
- Se migrovat místní virtuální počítače VMware na virtuální počítač Azure.
- Contoso má místní datacenter (contoso-datacenter), s řadič domény místní (**contosodc1**).
- Místní virtuální počítače v datovém centru společnosti Contoso bude vyřazena z provozu, po dokončení migrace.

![Architektura scénáře](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Služba správy databáze](https://docs.microsoft.com/azure/dms/dms-overview) | Služba DMS umožňuje bezproblémové migrace z více zdrojů databáze do platformy dat Azure s minimálními výpadky. | Další informace o [podporované oblasti](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) pro DMS a získání [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/database-migration/).
[Instance spravované SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Spravované Instance je služba spravované databáze, která představuje plně spravované Instance systému SQL Server v cloudu Azure. Ji sdílí stejný kód s nejnovější verzi databázového stroje SQL Server a má nejnovější funkce, vylepšení výkonu a opravy zabezpečení. | Spuštění v Azure pomocí Azure SQL Database spravované instancí platit poplatky stanovené podle kapacity. [Další informace](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migrace a zotavení po havárii pro virtuální počítače Azure a místní virtuální počítače a fyzické servery.  | Při replikaci do Azure se vám neúčtují poplatky za úložiště Azure.  Virtuální počítače Azure se vytvářejí a platit poplatky, když dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) o poplatky a ceny.

 

## <a name="migration-process"></a>Proces migrace

Contoso bude migrovat data i webové vrstvy SmartHotel aplikace do Azure.

1. Contoso je již jeho infrastrukturu Azure na místě, tak jednoduše přidat pár konkrétní Azure součásti pro tento scénář.
2. Datová vrstva proběhne pomocí služby migrace dat (DMS).  DMS připojení k virtuální počítač místní SQL Server přes připojení site-to-site VPN mezi Azure a Contoso datacenter a potom provede migraci databáze.
3. Webová vrstva proběhne migrace navýšení a shift pomocí Azure Site Recovery. To vede přípravy prostředí VMware místně, nastavení a povolení replikace a migrace virtuálních počítačů pomocí jejich selhání do Azure.

     ![Architektura migrace](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>Požadavky

Zde je co Contoso (a) je pro tento scénář.

**Požadavky** | **Podrobnosti**
--- | ---
**Registrace ve verzi preview** | Budete muset být zaregistrovaná v SQL spravované Instance omezené verzi Public Preview. Potřebujete předplatné Azure za účelem [zaregistrovat](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Registrace může trvat několik dní k dokončení, proto se ujistěte, můžete provést před zahájením nasazení tohoto scénáře.
**Předplatné Azure** | Měli jste již vytvořili předplatné Pokud jste provedli v první článek z této série hodnocení. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud používáte stávající předplatné a nejste správce, budete muset ve spolupráci s správcem můžete přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Obnovení lokality (místní)** | Vaše místní vCenter server musí používat verzi 5.5, 6.0 nebo 6.5<br/><br/> Hostiteli ESXi verzí 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware na hostiteli ESXi spuštěna.<br/><br/> Virtuální počítače, musí splňovat [požadavky pro Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Podporované [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurace.
**SLUŽBA DMS** | Služba DMS musíte [kompatibilní místní zařízení VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Musí umět nakonfigurovat místního zařízení VPN. Musí mít zvenčí veřejnou IPv4 adresu a adresu nesmí být umístěné za zařízením NAT.<br/><br/> Ujistěte se, že máte přístup k místní databázi systému SQL Server.<br/><br/> Brána Windows Firewall byste měli mít přístup k zdrojového databázového stroje. [Další informace](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Pokud je brána firewall před počítači pro databáze, přidejte pravidla povolit přístup k databázi a soubory přes SMB port 445.<br/><br/> Pověření použitá k připojení k systému SQL Server zdrojové a cílové spravované Instance musí být členy role serveru sysadmin.<br/><br/> Budete potřebovat síť sdílet v místní databázi, DMS můžete použít k zálohování zdrojové databáze.<br/><br/> Ujistěte se, zda má účet služby spuštění instance systému SQL Server zdrojové oprávnění k zápisu ve sdílené síťové složce.<br/><br/> Poznamenejte si uživatel systému Windows (a heslo), který má oprávnění Úplné řízení na sdílené síťové složce. Služba Azure databáze migrace zosobňuje tyto přihlašovací údaje uživatele pro nahrání záložní soubory do kontejneru úložiště Azure.<br/><br/> Proces instalace systému SQL Server Express nastaví protokolu TCP/IP na **zakázané** ve výchozím nastavení. Ujistěte se, že je povolena.


## <a name="scenario-steps"></a>Kroky scénáře

Zde uvádíme jak Contoso bude nastavení nasazení:

> [!div class="checklist"]
> * **Krok 1: Nastavení spravované Instance SQL Azure**: potřebují předem vytvořené spravované instance, do kterého bude migrovat místní databázi systému SQL Server.
> * **Krok 2: Příprava DMS**: potřebují k registraci poskytovatele migrace databáze, vytvoření instance a pak vytvořte projekt DMS. Je třeba nastavit SA URI DMS. Sdílený přístupový podpis (SAS) identifikátor URI (Uniform Resource) poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště, takže můžete udělit omezená oprávnění k objektům úložiště. Nastavení se identifikátor URI SAS, aby DMS přístup kontejneru účtu úložiště, do které služba odesílá soubory zálohování systému SQL Server.
> * **Krok 3: Příprava Azure Site Recovery**: pro Site Recovery, se musí vytvořit účet úložiště Azure k ukládání replikovaných dat a vytvořit trezor služeb zotavení.
> * **Krok 4: Příprava místní VMware Site Recovery**: Contoso Příprava účty pro virtuální počítač zjišťování a instalaci agenta a příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání.
> * **Krok 5: Replikace virtuálních počítačů**: nastavení replikace, jejich konfigurace Site Recovery zdrojové a cílové prostředí, nastavte zásady replikace a zahájení replikace virtuálních počítačů do úložiště Azure.
> * **Krok 6: Migrace databáze s DMS**: teď se můžete migrovat databázi.
> * **Krok 7: Migrace virtuálních počítačů pomocí Site Recovery**: se spustit převzetí služeb při selhání a ujistěte se, vše funguje a pak spustit úplné převzetí služeb při selhání k migraci virtuálních počítačů do Azure.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>Krok 1: Příprava spravované Instance Azure SQL

Pokud chcete nastavit spravované Instance SQL Azure, potřebuje Contoso podsítě:

- Podsíť musí být vyhrazená. Musí být prázdný a neobsahuje žádné jiné cloudové služby, a nesmí být podsíť brány.
- Po vytvoření spravované instance nesmí přidat prostředky do ní.
- Podsítě nesmí mít skupina NSG s ním spojená.
- Podsíť musí mít uživatele trasy tabulky (UDR) s 0.0.0.0/0 Další směrování Internetu jako pouze trasy přiřazen. 
- Volitelné vlastní DNS: Pokud je zadán pro vlastní DNS virtuální sítě, musíte do seznamu přidat Azure rekurzivní překladače IP adresu (například 168.63.129.16). [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Podsítě nesmí mít koncového bodu služby (úložiště nebo SQL) s ním spojená. Koncové body služby by mělo být zakázáno ve virtuální síti.
- Podsíť musí mít minimálně 16 IP adres. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) o dimenzování podsíť spravované instance.
- V hybridním prostředí je potřeba vlastní nastavení DNS. Contoso se konfigurace nastavení DNS použít jeden nebo více serverů Azure DNS. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) o vlastním nastavení DNS.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Nastavit virtual network pro spravované instance

Virtuální síti contoso nastavuje následujícím způsobem: 

1. Contoso vytvoří novou virtuální síť (VNET-SQLMI-EU2) v primární oblasti Východ USA 2, ve skupině prostředků ContosoNetworkingRG.
2. Contoso přiřadí adresní prostor 10.235.0.0/24, zajistíte, že rozsah nepřekrývá s jinými sítěmi v podnikové síti Contoso.
2. Zvyšují dvě podsítě k síti:
    - SQLMI – DS-EUS2 (10.235.0.0.25)
    - SQLMI-PILA EUS2 (10.235.0.128/29). Tato podsíť se použije pro připojení adresáře k spravované instance (SQLMI).

    ![Spravované Instance sítě](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Po nasazení virtuální sítě a podsítě partnerský Contoso vztah sítě následujícím způsobem:

    - Partnerské uzly VNET-SQLMI-EUS2 s virtuální sítě-HUB-EUS2 (rozbočovač virtuální sítě pro východní USA 2).
    - Partnerské uzly VNET-SQLMI-EUS2 s VNET EUS2 PRODUKČNÍMU (produkční sítě).

    ![Partnerský vztah sítě](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso nastaví vlastní nastavení DNS. DNS bude bodu nejprve do svých řadičů domény Azure. Azure DNS bude sekundární. Řadiče domény Contoso Azure jsou umístěny následovně:

    - Umístěný v podsíti PRODUKČNÍMU. řadič domény EUS2, v produkčním prostředí východní USA 2 sítě (virtuální sítě EUS2 PRODUKČNÍMU).
    - Adresa CONTOSODC3: 10.245.42.4
    - Adresa CONTOSODC4: 10.245.42.5
    - Azure překladač služby DNS: 168.63.129.16

     ![Sítě DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**Potřebujete další pomoc?**

- [Přehled](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) spravované instancí SQL Azure.
- [Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) o vytvoření virtuální sítě pro spravované Instance systému SQL.
- [Další informace o](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) nastavení partnerského vztahu.
- [Další informace o](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) aktualizace nastavení Azure AD DNS.



### <a name="set-up-routing"></a>Nastavení směrování

Spravované Instance je umístěn ve virtuální privátní síti, takže Contoso musí směrovací tabulku mohla komunikovat se službou správy Azure. Pokud nemůže komunikovat se službou, která ho spravuje, bude nedostupná.

- Směrovací tabulka obsahuje sadu pravidel (směrování), která určuje, jak by měl směrovat pakety odeslané z spravované Instance ve virtuální síti.
- Směrovací tabulka je přidružen podsítě, ve kterých jsou nasazeny spravované instance. Zpracovává jednotlivých paketů z podsítě podle přidružené směrovací tabulka.
- Podsíť lze přidružit pouze k jedné směrovací tabulce.
- Neexistují žádné další poplatky za vytváření směrovací tabulky v Microsoft Azure.

1. Contoso vytvoří tabulku trasy definované uživatelem. Směrovací tabulka se vytvoří ve skupině prostředků ContosoNetworkingRG.

    ![Tabulka směrování](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Abyste dosáhli souladu s požadavky na spravované Instance, po nasazení směrovací tabulka (MIRouteTable), přidejte Contoso trasa se předponu adresy z 0.0.0.0/0, a **typ dalšího směrování** nastavena na **Internet**.

    ![Předpona trasy tabulky](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Směrovací tabulka je contoso přidružit podsítě SQLMI-DB-EUS2 (v síti VNET-SQLMI-EUS2). 

    ![Podsíť tabulky trasy](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) nastavení tras pro spravované instance.

### <a name="create-a-managed-instance"></a>Vytvoření spravované instance

Contoso teď můžete zřídit spravované Instance databáze SQL.

1. Vzhledem k tomu, že Instance spravované obsluhuje obchodní aplikace, Contoso ho nasadit v jejich primární oblasti Východ USA 2, ve skupině prostředků ContosoRG 
2. Vyberou cenovou úroveň a velikost výpočetních operací a úložiště pro instanci. [Další informace](https://azure.microsoft.com/pricing/details/sql-database/managed/) o cenách.

    ![Spravovaná instance](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Po nasazení spravované Instance objeví dvě nové prostředky ve skupině prostředků ContosoRG:

    - Virtuální cluster v případě, že máte více spravované instance
    - SQL Server spravované Instance. 

    ![Spravovaná instance](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) zřizování do spravované Instance.

## <a name="step-2-prepare-dms"></a>Krok 2: Příprava DMS

Příprava DMS Contoso potřeba udělat několik věcí:

- Registrace zprostředkovatele DMS v Azure
- Služba DMS poskytnout přístup k úložišti Azure pro nahrávání záložní soubory použít k migraci databáze. Vytvoří kontejner úložiště objektů blob a generovat sdíleného přístupového podpisu (SAS) identifikátor URI pro ni. 
- Vytvoření projektu DMS.


Proveďte následující kroky:

1. Contoso zaregistrujte zprostředkovatele databáze migrace v rámci svého předplatného.
    ![Služba DMS registrace](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Vytvořte kontejner úložiště objektů blob a generovat identifikátor URI SAS, se kterým můžete DMS.

    ![IDENTIFIKÁTOR URI PRO SAS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Nakonec vytvořte instanci DMS. 

    ![Služba DMS instance](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso umístí DMS instance PRODUKČNÍMU. řadič domény EUS2 podsíť virtuální sítě VNET PRODUKČNÍMU řadič domény EUS2.
    - Jejich umístit ji došlo protože musí být ve virtuální síti, kterým může přistupovat místní virtuální počítač SQL Server prostřednictvím brány VPN.
    - EUS2 PRODUKČNÍMU virtuální sítě je peered do virtuální sítě-HUB-EUS2 a může používat vzdálené brány.  Tím se zajistí, že služba DMS může komunikovat podle potřeby.

        ![Služba DMS sítě](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**Potřebujete další pomoc?**
- [Další informace o](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) nastavení DMS.
- [Další informace](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2) o vytváření a použití SAS.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Krok 3: Příprava Azure pro službu Site Recovery

Existuje řada Azure prvků, které Contoso potřebuje, aby nastavení obnovení lokality pro migraci jejich webovou vrstvu virtuálních počítačů (WEBMV)

- Převzetí služeb při selhání virtuální síť, ve kterém jsou umístěny prostředky.
- Účet úložiště Azure pro uložení replikovaná data. 
- Trezor služeb zotavení v Azure.

Nastavené Site Recovery následujícím způsobem:

1. Vzhledem k tomu, že je virtuální počítač front-end webové aplikace SmartHotel, že bude převzetí služeb při selhání virtuálního počítače ke stávající produkční síť (VNET-PRODUKČNÍMU-EUS2) a podsíti (PRODUKČNÍMU-FE – EUS2), v primární oblasti Východ USA 2. Nastavují této sítě se při jejich [nasadit infrastrukturu Azure](contoso-migration-infrastructure.md)
2. Uživatel vytvořit účet úložiště Azure (contosovmsacc20180528). Jejich pomocí standardního úložiště a replikace LRS používáte účet obecné účely.

    ![Úložiště obnovení lokality](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Pomocí účtu sítě a úložiště na místě Contoso teď můžete vytvořit trezor (ContosoMigrationVault) a umístěte ji ve skupině prostředků ContosoFailoverRG, v primární oblasti Východ USA 2.

    ![Trezor služby Recovery Services](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Příprava místní VMware Site Recovery

Příprava VMware pro Site Recovery, zde je, co Contoso musí udělat:

- Připravte účet v systému vCenter server nebo vSphere ESXi hostiteli, a automatizovat zjišťování virtuálních počítačů.
- Připravte účet, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware, který chcete replikovat.
- Příprava místní virtuální počítače, tak, aby se mohli přihlásit k virtuální počítače Azure, když jste vytvořili po převzetí služeb při selhání.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automaticky zjistit virtuální počítače. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Je třeba účet, který můžete spustit operací, jako je vytváření a odebrání disků a zapnutí virtuálních počítačů.

Účet contoso nastavuje následujícím způsobem:

1. Vytvoří roli na úrovni vCenter.
2. Přiřadí potřebná oprávnění k dané roli.

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) vytvoření a přiřazení role pro automatické zjišťování.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na virtuálním počítači, který chcete replikovat, musí být nainstalovaná služba Mobility.

- Site Recovery můžete provést automatické nabízené instalace této součásti, když povolíte replikaci pro virtuální počítač.
- Pro automatickou nabízenou instalaci je nutné připravit účet, který Site Recovery bude používat pro přístup k virtuálnímu počítači.
- Tento účet zadáte při nastavování replikace v konzole Azure.
- Musíte domény nebo místní účet s oprávněními k instalaci na virtuálním počítači.

**Potřebujete další pomoc**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) vytváření účtu pro nabízenou instalaci služby Mobility.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání do Azure Contoso chce být schopni se připojit k replikovaných virtuálních počítačů v Azure. K tomu je několik věcí, které potřebují do virtuálního počítače místní dřív, než spustí migrace: 

1. Pro přístup přes internet, povolení protokolu RDP pro virtuální počítač na místě před převzetí služeb při selhání a ujistěte se, že pro přidána pravidla TCP a UDP **veřejné** profilu a že je v povolené RDP **brány Windows Firewall**  >  **Povolené aplikace** pro všechny profily.
2. Pro přístup prostřednictvím své sítě site-to-site VPN, povolení protokolu RDP na místním počítači a povolit RDP v **brány Windows Firewall** -> **povolené aplikace a funkce** pro **domény a Privátní** sítě.
3. Nastavují zásada SAN operačního systému na virtuální počítač místní **OnlineAll**.

Kromě toho při jejich spuštění převzetí služeb při selhání je muset zkontrolujte následující:

- Měla by existovat žádné Windows aktualizace čekající na vyřízení ve virtuálním počítači při spuštění převzetí služeb při selhání. Pokud existují, nebudou moct přihlásit k virtuálnímu počítači, dokud se nedokončí aktualizace.
- Po převzetí služeb při selhání, by se mělo zjistit **spouštění diagnostiky** zobrazíte snímek virtuálního počítače. Pokud to nebude fungovat, měli zkontrolovat, že virtuální počítač používá a zkontrolujte tyto [tipy pro odstraňování potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Krok 5: Replikovat místní virtuální počítače Azure pomocí Site Recovery

Před spuštěním migrace do Azure, musí Contoso nastavení replikace a zapnout replikaci pro svoje místní virtuální počítač.

### <a name="set-a-replication-goal"></a>Nastavte cíle replikace

1. V úložišti, v části název trezoru (ContosoVMVault) nastavují cílem replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu**.
2. Určí, že jejich počítačů se nacházejí na místních, že jsou virtuální počítače VMware, a že se chcete replikovat do Azure.

    ![Cíl replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrďte plánování nasazení

Chcete-li pokračovat, potřebují potvrďte, že se dokončila plánování nasazení tak, že vyberete **Ano, mám to hotové**. V tomto nasazení Contoso jsou pouze při migraci jednoho virtuálního počítače a nepotřebujete plánování nasazení.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nyní budou muset nakonfigurovat svoje zdrojové prostředí. K tomu se stáhnout šablonu OVF a použijte jej k nasazení konfiguračního serveru a jeho přidružené komponenty jako vysoce dostupný, místní virtuální počítač VMware. Součásti na serveru zahrnují:

- Konfigurace serveru, který koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.
- Po konfiguraci serveru virtuálního počítače je vytvořena a spuštěna Contoso ji zaregistrovat v trezoru.


Contoso následujícím způsobem proveďte tyto kroky:

1. Stáhnou OVF šablonu z portálu Azure (**Příprava infrastruktury** > **zdroj** > **konfigurační Server**).
    
    ![Stáhnout OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Šablona se naimportovat do VMware vytvoření a nasazení virtuálního počítače.

    ![Šablona OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Při zapnutí virtuální počítač poprvé, spustí se až do instalace produktu Windows Server 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce. Při prvním přihlášení ve výchozím nastavení spustí nástroj Azure obnovení lokality konfigurace.
5. V nástroji určí název, který bude používat při registraci konfigurační server v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po připojení, vyberou **přihlášení**, k přihlášení k předplatnému Azure. Přihlašovací údaje musí mít přístup k úložišti, ve kterém se zaregistruje konfigurační server. 

    [Zaregistrujte konfigurační server](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Nástroj provede několik úloh konfigurace a pak restartuje počítač. Přihlášení k počítači znovu a automaticky spustí Průvodce konfigurací serveru správy.
8. V Průvodci vyberou síťový adaptér pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení své konfigurace.
9. Výběrem předplatné, skupinu prostředků a úložiště, ve kterém zaregistrujte konfigurační server.
        ![Trezoru](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. Se pak stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. Pak ověřte nastavení serveru.
11. Po ověření že zadejte plně kvalifikovaný název domény nebo IP adresa vCenter server nebo vSphere hostitele. Ponechte výchozí port a zadejte popisný název serveru vCenter server v Azure.
12. Je třeba zadat účet, který vytvořili dříve, tak, aby Site Recovery může automaticky zjistit virtuální počítače VMware, které jsou k dispozici pro replikaci. 
13. Určí pověření pro automaticky nainstalovat službu Mobility, pokud je zapnutá replikace. Pro počítače s Windows že účet potřebuje oprávnění místního správce na virtuálních počítačích. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Po dokončení registrace na portálu Azure Contoso dvojité ověří, že konfigurační server a VMware server jsou uvedené na **zdroj** stránky v úložišti. Zjišťování může trvat 15 minut nebo déle. 
8. Site Recovery pak připojí k VMware servery pomocí zadané nastavení a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavit cíl

Nyní Contoso musí nakonfigurovat cílové prostředí replikace.

1. V **připravit infrastrukturu** > **cíl**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a sítě v zadané cílové.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Po zdroje a cíle jsou nastavení, je připraven k vytvoření zásady replikace a přidružte ji k konfigurační server Contoso.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružení**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota RPO**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí 24 hodin. Tato hodnota určuje, jak dlouho je okno uchování pro každý bod obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímkování konzistentní aplikace vzhledem**. Výchozí jednu hodinu. Tato hodnota určuje četnost, v němž jsou vytvořen snímky konzistentní s aplikacemi.
 
        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružení zásady replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**Potřebujete další pomoc?**

- Můžete si přečíst úplné návod všechny tyto kroky v [nastavit zotavení po havárii pro místní virtuální počítače VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici vám pomohou [nastavení zdrojového prostředí](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfigurační server](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [nakonfigurujete nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Povolení replikace

Contoso nyní můžete spustit replikaci WebVM.

1. V **replikujte aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Indikuje, že chcete povolit virtuální počítače, vyberte vCenter server a konfigurační server.

 ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Nyní zadejte cíl nastavení, včetně skupiny prostředků a sítě, ve kterém virtuální počítač Azure budou umístěné po převzetí služeb při selhání a účet úložiště, ve kterém se uloží replikovaná data.

     ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso vybere WebVM pro replikaci. Obnovení lokality nainstaluje službu Mobility na každý virtuální počítač povolit pro něho replikaci. 

    ![Povolení replikace](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso zkontroluje, zda je vybrána zásady správné replikace a umožňuje replikace pro WEBVM. Budou sledovat průběh replikace **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
6. V **Essentials** Contoso na portálu Azure můžete zobrazit strukturu pro virtuální počítače replikující se do Azure.

    ![Zobrazení infrastruktury](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**Potřebujete další pomoc?**

Můžete si přečíst úplné návod všechny tyto kroky v [povolit replikaci](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>Krok 6: Migrace databáze s DMS

Contoso je potřeba vytvořit projekt DMS a migraci databáze.

### <a name="create-a-dms-project"></a>Vytvoření projektu DMS
1. Jejich vytvoření projektu DMS. Určí typ zdrojového serveru jako SQL Server a cíl jako spravované Instance serveru Azure SQL Database.

     ![Služba DMS projektu](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Po vytvoření projektu, otevře se Průvodce migrací.

### <a name="migrate-the-database"></a>Migrace databáze 

1. V Průvodci migrací Contoso určuje zdrojový virtuální počítač na kterém se nachází místní databázi a přihlašovací údaje k přístupu.

    ![Služba DMS zdroje](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Výběrem databáze pro migraci (SmartHotel.Registration).

    ![Služba DMS zdrojové databáze](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Jako cíl určí název Instance spravované ve službě Azure a přihlašovací údaje.

    ![Služba DMS cíl](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Potom v **+ nová aktivita** > **spuštění migrace**, jejich zadejte nastavení spuštění migrace:
    - Zdrojové a cílové přihlašovací údaje.
    - Databáze pro migraci.
    - Sdílené síťové složce vytvářely na místní počítač. Služba DMS trvá zálohy zdroje k této sdílené složce.
        - Účet služby spuštění zdroje systému SQL Server, že instance musí mít oprávnění k zápisu v této sdílené složce.
        - Zadejte plně kvalifikovaný název domény cestu ke sdílené složce.
    - Identifikátor URI SAS, která poskytuje přístup ke kontejneru účtu úložiště, ke které služba odesílá soubory zálohy pro migraci DMS.

        ![Služba DMS nastavení](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Uložit migrace a spusťte ji.
6. V **přehled**, je i sledování stavu migrace.

    ![Služba DMS monitorování](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Po dokončení migrace se ověřte, že cílovým databázím existují na spravované instance.

    ![Služba DMS monitorování](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>Krok 7: Migraci virtuálního počítače pomocí Site Recovery

Contoso se spouští rychlé testovací převzetí služeb a potom migraci virtuálního počítače.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Před migrací pomáhá testovací převzetí služeb WEBVM, ujistěte se, zda vše funguje podle očekávání. 

1. Spuštění testovací převzetí služeb při selhání, k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovat**).
2. Vyberou **vypnout počítač před zahájením převzetí služeb při selhání**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Testovací převzetí služeb při selhání spouští: 

    - Kontrola předpokladů postupné a zkontrolujte, zda jsou všechny podmínky, požadována pro migraci na místě.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.
3. Po dokončení převzetí služeb při selhání se zobrazí na portálu Azure repliky virtuálního počítače Azure. Jejich zkontrolujte, že všechno funguje správně. Virtuální počítač odpovídající velikost, je připojený k síti správné a je spuštěná. 
4. Po ověření testu převzetí služeb, Contoso vyčistí převzetí služeb při selhání a záznamy a uloží jakékoli připomínky. 

### <a name="migrate-the-vm"></a>Migraci virtuálního počítače

1. Po ověření, že testovací převzetí služeb fungovala podle očekávání, vytvoří Contoso plán obnovení pro migraci. WEBVM si přidají do plánu.

     ![Plán obnovení](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Potom spuštění převzetí služeb při selhání v plánu. Vyberte nejnovější bod obnovení a určit, že Site Recovery se pokuste vypnout virtuální počítač na místě před spuštěním převzetí služeb při selhání.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Po převzetí služeb při selhání Contoso ověřte, že virtuální počítač Azure zobrazuje podle očekávání na portálu Azure.

   ![Plán obnovení](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Po ověření virtuálního počítače v Azure, po dokončení migrace na dokončení procesu migrace, zastavení replikace pro virtuální počítač a ukončete fakturace Site Recovery pro virtuální počítač.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Aktualizovat připojovací řetězec

Contoso jako poslední krok v procesu migrace, aktualizuje připojovací řetězec aplikace tak, aby odkazoval na databázi migrované systémem jejich MI SQL.

1. Na portálu Azure najdou připojovací řetězec kliknutím **nastavení** > **připojovací řetězce**.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Řetězec se aktualizuje uživatelské jméno a heslo spravované instance SQL.
3. Po dokončení konfigurace řetězec nahrazují aktuálního připojovacího řetězce v souboru web.config své aplikace.
4. Po aktualizaci souboru a uložíte, budou restartujte službu IIS na WEBVM. To provést pomocí **IISRESET /RESTART** z příkazový řádek.
5. Po restartování služby IIS, aplikace budou používat databázi systémem spravované Instance SQL.
6. V tomto okamžiku Contoso vypnout SQLVM počítače na místní a migrace je kompletní.

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštěná testovací převzetí služeb. 
- [Další informace](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) přebírání služeb při selhání do Azure.

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Dokončení migrace SmartHotel aplikace běží ve virtuálním počítači Azure a SmartHotel databáze je k dispozici na spravované Instance SQL Azure.  

Teď je potřeba Contoso některé čištění, postupujte následovně:  

- Odeberte počítač WEBVM z inventáře vCenter.
- Odeberte počítač SQLVM z inventáře vCenter.
- Odeberte WEBVM a SQLVM z místní úlohy zálohování.
- Aktualizace dokumentace interní zobrazit nové umístění, IP adresu pro WEBVM.
- Odeberte SQLVM z dokumentace. Případně se může označte ji zobrazit, protože odstranit a už ve virtuálním počítači inventáře.
- Zkontrolujte všechny prostředky, které pracují s virtuálními počítači, vyřazení a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznit a zabezpečit jejich novou infrastrukturu.

### <a name="security"></a>Zabezpečení

Tým zabezpečení Contoso zkontroluje virtuálních počítačích Azure a spravovat Instance SQL, určení jakékoli problémy se zabezpečením s jeho implementace.

- Zkontrolujte že skupiny zabezpečení sítě (Nsg) pro virtuální počítač, abyste mohli řídit přístup. K zajištění, že pouze povolené na aplikaci přenos dat se používají skupiny Nsg.
- Zvažují také zabezpečení dat na disku pomocí Azure Disk Encryption a Azure KeyVault.
- Umožňují detekce hrozeb na SQL spravované Instance (SQLMI). Výstrahy se posílají do jejich zabezpečení týmu nebo služby podpory systému otevřete lístek, pokud je zjištěno ohrožení. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Zabezpečení spravované instance](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[Další informace](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupy zabezpečení pro virtuální počítače.

### <a name="backups"></a>Zálohování
Contoso se bude zálohovat data na WEBVM pomocí služby zálohování Azure. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencování a cenově optimalizace

1. Contoso má existující licencování pro WEBVM a bude využívat výhody hybridní Azure.  Převádějí existující virtuální počítač Azure využívat výhod této ceny.
2. Contoso povolíte Azure náklady na správu licencované Cloudyn, dceřiné společnosti Microsoft. Je řešení pro správu více cloudové náklady, která pomáhá využívat a spravovat Azure a dalším prostředkům cloudu.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o Azure náklady na správu. 


## <a name="conclusion"></a>Závěr

V tomto článku Contoso rehosted SmartHotel aplikaci v Azure pomocí migrace front-endu aplikace virtuálních počítačů do Azure pomocí služby Site Recovery. Proto se migrovat místní databázi do Azure SQL spravované Instance pomocí DMS.

## <a name="next-steps"></a>Další postup

V další článek v řadě ukážeme, jak Contoso opětovným hostováním aplikaci SmartHotel pro virtuální počítače Azure pomocí služby Azure Site Recovery.

