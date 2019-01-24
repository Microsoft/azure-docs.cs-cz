---
title: Změna hostitele místní aplikaci Contoso migrací virtuálních počítačů Azure a Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso ke kolizi místní aplikaci na virtuálních počítačích Azure a s využitím Azure SQL Database Managed Instance.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 6e15d4376fae576f615cac7c094f53dfcc22618e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54826611"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Migrace Contoso: Změna hostitele aplikace on-premises na virtuálním počítači Azure a SQL Database, spravované Instance

V tomto článku se migruje Contoso jeho aplikace SmartHotel360 front-endového virtuálního počítače na Virtuálním počítači Azure pomocí služby Azure Site Recovery. Contoso také migraci databáze aplikace do Azure SQL Database Managed Instance.

> [!NOTE]
> Azure SQL Database Managed Instance je aktuálně ve verzi preview.

Tento článek je jednou z řady článků, který popisuje, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a řadu scénářů, které ukazují, jak nastavit infrastrukturu migrace a spustit různé druhy migrace. Scénáře jejich složitost v. V čase se přidají do série článků.


**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které se používají v řadě. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články týkající se migrace z této série se používá stejnou infrastrukturu. | K dispozici.
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md) | Contoso spuštění posouzení jeho místní dvouvrstvé SmartHotel aplikaci běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů s použitím [Azure Migrate](migrate-overview.md) služby. Contoso vyhodnocuje databáze aplikace SQL serveru s použitím [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance | Contoso běží lift and shift migrace do Azure pro své místní SmartHotel aplikaci. Contoso migruje aplikace front-endového virtuálního počítače pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migraci databáze aplikace do Azure SQL Database Managed Instance pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | V tomto článku
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Contoso migruje svou aplikaci SmartHotel virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje SmartHotel aplikace. Společnost Contoso využívá Site Recovery k migraci virtuálních počítačů aplikace. Jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn využívá službu Database Migration Service. | K dispozici.
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Contoso dokončení migrace lift and shift jeho Linux osTicket aplikace na virtuální počítače Azure pomocí Site Recovery. | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje svou aplikaci osTicket Linux do virtuálních počítačů Azure pomocí Site Recovery. Migrace databáze aplikace ke službě Azure Database for MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorovat aplikace do webové aplikace Azure a Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migruje jeho SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure. | K dispozici.
[Článek 10: Refaktorovat Linuxovou aplikaci v Azure webové aplikace a služby Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje jeho Linuxovou aplikaci osTicket do webové aplikace Azure v několika lokalitách. Webová aplikace je integrovaná se službou GitHub pro průběžné doručování. Contoso migraci databáze aplikace do Azure Database for MySQL – instance. | K dispozici.
[Článek 11: Refaktorovat a Team Foundation Server na službách Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migruje své místní nasazení serveru Team Foundation Server a migrujte ho ke službám Azure DevOps v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jeho SmartHotel aplikace do Azure a potom rearchitects aplikace. Contoso rearchitects webové vrstvy aplikace jako kontejner Windows a rearchitects databázi aplikace s využitím Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service, Azure Functions, Azure Cognitive Services a Azure Cosmos DB. | K dispozici.
[Článek 14: Škálování migrace do Azure](contoso-migration-scale.md) | Po vyzkoušení kombinace migrace, Contoso připraví škálovat na plné migraci do Azure. | K dispozici.




Ukázkové aplikace SmartHotel360, který se používá v tomto článku si můžete stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Obchodní faktory

Společnosti Contoso vedení IT úzké spolupráci s obchodními partnery společnosti pochopit, co obchodní chce, aby k dosažení této migraci:

- **Adresa obchodní růst**: Contoso roste. V důsledku toho přetížení zvýšil na místních systémech a infrastrukturu vaší společnosti.
- **Zvýšení efektivity**: Contoso potřebuje odebrat nepotřebné postupy a zjednodušte procesy pro její vývojáře a uživatele. Obchodní potřeby IT být rychlé a k není produkovaného odpadu čas a peníze, takže společnost doručovat rychleji na požadavky zákazníků.
- **Zvýšení flexibility**:  Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změny, ke kterým dochází v marketplace pro společnost v globální ekonomiku proběhla úspěšně. IT ve společnosti Contoso nesmí získat způsobem, nebo se Staňte obchodní blokování.
- **Škálování**: S růstem společnosti úspěšně, musíte zadat Contoso IT systémy, které můžou růst stejným tempem.

## <a name="migration-goals"></a>Cíle migrace

Tým cloudových Contoso zjistila cíle pro tuto migraci. Společnost používá cílů migrace určit nejlepší metody migrace.

- Po dokončení migrace aplikace v Azure by měl mít stejné možnosti výkonu, které k němu má aplikace ještě dnes v prostředí VMWare v místním společnosti Contoso. Přesun do cloudu, neznamená, že výkon aplikace je méně kritické.
- Contoso nechce investovat do aplikace. Aplikace je kritická a důležitá pro podnik, ale Contoso chce jednoduše přesunout do cloudu aplikaci v aktuální podobě.
- Po migraci oznámení o aplikaci, byste měli minimalizovat administrativní úlohy.
- Contoso nechce používat Azure SQL Database pro tuto aplikaci. Hledá alternativy.


## <a name="solution-design"></a>Návrh řešení

Po Připnutí dolů své cíle a požadavky, Contoso navrhuje a kontrol řešení nasazení a identifikuje proces migrace, včetně služeb Azure, které budete používat pro migraci.

### <a name="current-architecture"></a>Aktuální architektura 

- Contoso má jeden hlavní datacentrum (**contoso-datacenter**). Datového centra se nachází v města New York východní USA.
- Contoso má tři další místní větve ve Spojených státech amerických.
- Hlavní datové centrum je připojený k Internetu pomocí fiber připojení k síti Ethernet Metro (500 MB/s).
- Každá větev je místně připojen k Internetu pomocí podnikové třídy připojení s IPsec VPN tunely zpět do hlavního datového centra. Instalační program umožňuje společnosti Contoso celá síť trvale připojený k Internetu a optimalizuje připojení k Internetu.
- Hlavní datové centrum je plně virtualizovaný s VMware. Contoso má dva hostitele ESXi 6.5 virtualizace, které jsou spravované přes vCenter Server verze 6.5.
- Contoso používá službu Active Directory pro správu identit. Contoso používá servery DNS v interní síti.
- Contoso má řadič domény v místním (**contosodc1**).
- Běží na virtuálních počítačích VMware řadiče domény. Řadiče domény v místní větve spustit na fyzických serverech.
- Aplikace SmartHotel360 je rozvrstvená na dva virtuální počítače (**WEBVM** a **SQLVM**), které jsou umístěné na hostiteli VMware ESXi verze 6.5 (**contosohost1.contoso.com**). 
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**) běžící na virtuálním počítači.

![Aktuální architektura Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

### <a name="proposed-architecture"></a>Navrhované architektury

V tomto scénáři Contoso chce migrovat své dvouvrstvé místní cestovní aplikaci následujícím způsobem:

- Migrace databáze aplikace (**SmartHotelDB**) do Azure SQL Database Managed Instance.
- Migrace front-endu **WebVM** na Virtuálním počítači Azure.
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.

![Architektura scénáře](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="database-considerations"></a>Důležité informace o databázi

Jako součást procesu návrhu řešení nebyla Contoso porovnání funkcí mezi službou Azure SQL Database a spravované instanci SQL serveru. Následující důležité informace o tom se rozhodnout pro Managed Instance.

- Managed Instance cílí na poskytování téměř 100 % kompatibilitu s nejnovější verzí systému SQL Server v místním. Microsoft doporučuje Managed instance pro zákazníky, kteří používají místní SQL Server na virtuálním počítači IaaS, který chcete migrovat svoje aplikace na plně spravovaná služba s minimálním návrh změny.
- Contoso je plánují migraci velkého počtu aplikací z místního na IaaS. Mnoho z nich jsou k dispozici nezávislých výrobců softwaru. Contoso si uvědomuje, že použití spravované Instance vám pomůže zajistit kompatibilitu databáze pro tyto aplikace, nikoli pomocí SQL Database, která nemusí být podporován.
- Contoso můžete jednoduše provést lift and shift migraci do spravované Instance pomocí plně automatizované Data Migration Service (DMS). Pomocí této služby na místě Contoso ho můžete znovu použít pro budoucí databázi migrace.
- Spravovaná Instance SQL podporuje SQL Server Agent, který je důležitý problém pro aplikace SmartHotel360. Contoso potřebuje tato kompatibilita, jinak bude nutné změnit návrh plány údržby potřebné aplikace.
- S programem Software Assurance si mohou vyměňovat Contoso svoje stávající licence pro zlevněné sazby SQL Database Managed Instance pomocí zvýhodněné hybridní využití Azure pro SQL Server. To umožňuje společnosti Contoso a ušetřete až 30 % na Managed Instance.
- Spravované Instance je plně obsažen ve virtuální síti, takže poskytuje vysokou úroveň izolace a zabezpečení dat společnosti Contoso. Contoso můžete získat výhody veřejného cloudu, a zajistit přitom ochranu prostředí izolované z veřejného Internetu.
- Managed Instance podporuje mnoho funkcí zabezpečení, včetně maskování dat vždy šifrována a dynamické zabezpečení na úrovní řádků a detekce hrozeb.


### <a name="solution-review"></a>Kontrola řešení

Contoso vyhodnotí navrhovaných návrhu společně vložením seznam výhody a nevýhody.

**Posouzení** | **Podrobnosti**
--- | ---
**V oblasti IT** |  WEBVM bude přesunuta do Azure beze změny, provedení migrace jednoduché.<br/><br/> Spravovaná Instance SQL podporuje cíle a technické požadavky společnosti Contoso.<br/><br/> Spravovaná Instance bude poskytovat 100 % kompatibilitu s jejich aktuální nasazení, při přesouvání mimo systém SQL Server 2008 R2.<br/><br/>  Můžete využít své investice do Software Assurance a použití programu zvýhodněné hybridní využití Azure forSQL Server a Windows Server.<br/><br/> Můžete znovu použít službu Database Migration Service pro další migrace ve budoucí.<br/><br/> Spravovaná Instance SQL má integrované odolnost proti chybám, které Contoso nemusí konfiguruje. Tím se zajistí, že datová vrstva již není jediným bodem převzetí služeb při selhání.
**Nevýhody** | WEBVM běží Windows Server 2008 R2.  Tento operační systém se nepodporuje v Azure, není již podporovanou platformu. [Další informace](https://support.microsoft.com/en-us/help/956893).<br/><br/> Webová vrstva zůstává jediný bod převzetí služeb při selhání jenom WEBVM poskytující služby.<br/><br/> Contoso se potřebujete i nadále podporuje úroveň webové aplikace jako virtuální počítač, spíše než přesun spravované služby, jako je Azure App Service.<br/><br/> Pro datovou vrstvu Managed Instance nemusí být nejlepším řešením, pokud chcete vlastní nastavení operačního systému nebo databázový server Contoso, nebo pokud chce spouštět aplikace třetích stran spolu s SQL Server. Díky této flexibilitě poskytnout systémem SQL Server na Virtuálním počítači IaaS. 

### <a name="migration-process"></a>Proces migrace

Contoso se web a data úrovní, jeho aplikace SmartHotel360 migrace do Azure pomocí těchto kroků:

1. Contoso je již jeho infrastrukturu Azure na místě, proto potřebuje pouze přidat několik konkrétní komponenty Azure v tomto scénáři.
2. Datová vrstva bude možné migrovat pomocí Data Migration Service. Data Migration Service se připojuje k virtuálnímu počítači SQL serveru v místním přes připojení VPN typu site-to-site mezi Contoso datacentrem a Azure. Potom Data Migration Service migruje databáze.
3. Webová vrstva bude možné migrovat pomocí migrace lift and shift pomocí Site Recovery. Proces zahrnuje Příprava VMware v místním prostředí, nastavení a povolení replikace a migrace virtuálních počítačů pomocí převzetí služeb při selhání je do Azure.

     ![Architektura migrace](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

### <a name="azure-services"></a>Služby Azure

Služba | Popis | Náklady
--- | --- | ---
[Služba Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Služba Database Migration Service umožňuje zajistí tak bezproblémovou migraci z několika databázových zdrojů na platformu Azure dat s minimálními prostoji. | Další informace o [podporované oblasti](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) a [ceny služby Database Migration Service](https://azure.microsoft.com/pricing/details/database-migration/).
[Spravovaná Instance Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance je spravovanou databázovou službu, která představuje plně spravovanou instanci SQL serveru v cloudu Azure. Používá stejný kód jako nejnovější verzi databázového stroje SQL serveru a obsahuje nejnovější funkce, vylepšení výkonu a opravy zabezpečení. | Použití SQL Database Managed Instance spuštěné v Azure se účtují poplatky na základě kapacity. Další informace o [ceny Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba Site Recovery orchestruje a spravuje migraci a zotavení po havárii pro virtuální počítače Azure a místních virtuálních počítačů a fyzických serverů.  | Při replikaci do Azure se účtují poplatky za úložiště Azure.  Virtuální počítače Azure jsou vytvářeny a účtovat poplatky, pokud dojde k převzetí služeb při selhání. Další informace o [poplatky za Site Recovery a ceny](https://azure.microsoft.com/pricing/details/site-recovery/).

 

## <a name="prerequisites"></a>Požadavky

Contoso a ostatní uživatelé musí splňovat následující požadavky pro tento scénář:

Požadavky | Podrobnosti
--- | ---
**Registrace ve verzi preview pro Managed Instance** | Musí být zaregistrované v SQL Database Managed Instance omezené veřejné verzi preview. Budete potřebovat předplatné Azure k [zaregistrovat](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Registrace může trvat několik dnů nebo dokončení, tak zaregistrovat, než zahájíte nasazení tohoto scénáře.
**Předplatné Azure** | Měli jste už vytvořili odběr při provádění posouzení v první článku v této sérii. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce předplatného, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [použití řízení přístupu na základě rolí ke správě přístupu Site Recovery](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site Recovery (v místním prostředí)** | Vaše instance místní vCenter Server by měl být spuštěn verze 5.5, 6.0 nebo 6.5<br/><br/> Hostitele ESXi verze 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware běží na hostiteli ESXi.<br/><br/> Virtuální počítače musí splňovat [požadavky služby Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Podporované [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurace.
**Služba Database Migration Service** | Pro službu Database Migration Service, musíte [kompatibilní na místním zařízení VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Musíte být schopni konfigurovat místní zařízení VPN. Musí mít veřejnou IPv4 adresu externího. Adresa nesmí být umístěné za zařízením NAT a.<br/><br/> Ujistěte se, že budete mít přístup k místní databázi SQL serveru.<br/><br/> Brána Windows Firewall by měla moct přistupovat k source databázového stroje. Zjistěte, jak [konfigurace brány Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Pokud je brána firewall před počítači databáze, přidáte pravidla pro povolení přístupu k databázi a soubory prostřednictvím SMB portu 445.<br/><br/> Přihlašovací údaje, které se používají k připojení ke zdrojové instanci SQL serveru a cíle Managed Instance musí být členy role serveru sysadmin.<br/><br/> Budete potřebovat síť sdílet v místní databázi, službu Database Migration Service můžete použít k zálohování zdrojové databáze.<br/><br/> Ujistěte se, zda má účet služby spuštění zdrojové instanci SQL serveru oprávnění k zápisu ve sdílené síťové složce.<br/><br/> Poznamenejte si Windows uživatele a heslo, které mají oprávnění k úplnému řízení na sdílené síťové složce. Služba Database Migration Service zosobňuje tyto přihlašovací údaje uživatele pro nahrání záložních souborů do kontejneru služby Azure Storage.<br/><br/> Proces instalace systému SQL Server Express nastaví protokolu TCP/IP na **zakázané** ve výchozím nastavení. Ujistěte se, zda je povoleno.

## <a name="scenario-steps"></a>Kroky scénáře

Zde je, jak společnosti Contoso má v plánu nastavení nasazení:

> [!div class="checklist"]
> * **Krok 1: Nastavení SQL Database Managed Instance**: Contoso potřebuje předem vytvořené Managed Instance do kterého budete migrovat místní databázi systému SQL Server.
> * **Krok 2: Připravte službu Database Migration Service**: Contoso musí zaregistrovat poskytovatele migrace databáze, vytvoření instance a potom vytvořte projekt Database Migration Service. Contoso také musíte nastavit sdílený přístupový podpis (SAS) identifikátor URI (Uniform Resource) pro službu Database Migration Service. Identifikátor URI SAS poskytuje Delegovaný přístup k prostředkům v účtu úložiště společnosti Contoso, společnost Contoso může udělit omezená oprávnění k objektům úložiště. Contoso nastaví identifikátor URI SAS, tak služba Database Migration Service můžete přístup ke kontejneru účtu úložiště, ke kterému službu nahraje soubory zálohy systému SQL Server.
> * **Krok 3: Příprava Azure Site Recovery**: Společnosti Contoso musí vytvořit účet úložiště k ukládání replikovaných dat pro Site Recovery. Také musí vytvořit trezor služby Azure Recovery Services.
> * **Krok 4: Příprava VMware v místním prostředí pro Site Recovery**: Contoso připravíte účty pro virtuální počítač zjišťování a instalaci agenta pro připojení k virtuálním počítačům Azure po převzetí služeb při selhání.
> * **Krok 5: Replikace virtuálních počítačů**: Pro nastavení replikace, nakonfigurujte Site Recovery zdrojové a cílové prostředí Contoso, nastaví zásady replikace a spustí replikaci virtuálních počítačů do Azure Storage.
> * **Krok 6: Migrace databáze pomocí službu Database Migration Service**: Contoso migruje databáze.
> * **Krok 7: Migrace virtuálních počítačů pomocí Site Recovery**: Contoso běží převzetí služeb při selhání testu Ujistěte se, že všechno funguje. Pak spustí Contoso úplné převzetí služeb při selhání k migraci virtuálních počítačů do Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Krok 1: Příprava spravovaná Instance SQL Database

Contoso potřebuje nastavit Azure SQL Database Managed Instance, podsíť, která splňuje následující požadavky:

- Podsíť musí být vyhrazená. Musí být prázdný a nesmí obsahovat jiné cloudové služby. Podsítě nesmí být podsíť brány.
- Po vytvoření Managed Instance je Contoso, neměli byste přidávat prostředky do podsítě.
- Podsíť nemůže mít skupinu zabezpečení sítě spojenou s ním.
- Podsíť musí obsahovat tabulku uživatelem definované trasy směrování (UDR). By měla být přiřazena pouze trasy 0.0.0.0/0 směrování Internetu. 
- Volitelné vlastní server DNS: Pokud vlastní DNS je zadána ve službě Azure virtual network, Azure rekurzivní překladače IP adresu (například adresy 168.63.129.16) musí být přidaný do seznamu. Zjistěte, jak [konfigurace vlastního DNS pro spravovanou instanci](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Podsítě nesmí mít koncový bod služby (storage nebo SQL) s ním spojená. Koncové body služby je třeba zakázat ve virtuální síti.
- Podsíť musí mít minimálně 16 IP adres. Zjistěte, jak [velikost podsíť Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances).
- V hybridním prostředí společnosti Contoso jsou vyžadovány vlastní nastavení služby DNS. Contoso nakonfiguruje nastavení DNS použít jeden nebo více serverů Azure DNS vaší společnosti. Další informace o [DNS přizpůsobení](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Nastavení virtuální sítě pro Managed Instance

Správce společnosti Contoso ve virtuální síti wmm nastavit takto: 

1. Vytvoří novou virtuální síť (**připojení typu VNET-SQLMI EU2**) v primární oblasti USA – východ 2. Přidá virtuální sítě, abyste **ContosoNetworkingRG** skupinu prostředků.
2. Adresní prostor 10.235.0.0/24 přiřadí. Zajišťují, že se rozsah nepřekrývá s jinými sítěmi v jeho organizace.
3. Dvě podsítě, přidejte k síti:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Tato podsíť se používá pro připojení k Managed Instance adresáře.

      ![Spravovaná Instance – vytvoření virtuální sítě](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. Po nasazení virtuální sítě a podsítě, navázání partnerského vztahu mezi sítěmi následujícím způsobem:

    - Partnerské uzly **připojení typu VNET-SQLMI EUS2** s **připojení typu VNET-HUB-EUS2** (centrální virtuální síti v oblasti východní USA 2).
    - Partnerské uzly **připojení typu VNET-SQLMI EUS2** s **připojení typu VNET-PROD-EUS2** (produkční sítě).

      ![Vnet peering](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. Nastavují vlastní nastavení služby DNS. DNS body nejprve k řadičům domény Azure společnosti Contoso. Azure DNS je sekundární. Řadiče domény Contoso Azure jsou umístěny následovně:

    - Umístěný v **PROD. řadič domény EUS2** podsítě, v oblasti východní USA 2 produkční sítě (**připojení typu VNET-PROD-EUS2**)
    - **CONTOSODC3** adresa: 10.245.42.4
    - **CONTOSODC4** adresa: 10.245.42.5
    - Překladače Azure DNS: 168.63.129.16

      ![Servery DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*Potřebujete další pomoc?*

- Získejte přehled o [SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Zjistěte, jak [vytvoření virtuální sítě pro SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances).
- Zjistěte, jak [nastavení partnerského vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Zjistěte, jak [aktualizace nastavení DNS služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Nastavení směrování

Managed Instance je umístěn v privátní virtuální síť. Contoso potřebuje směrovací tabulku pro virtuální síť, ke komunikaci s Azure Management Service. Pokud virtuální síť nemůže komunikovat se službou, která ho spravuje, přestane být přístupný ve virtuální síti.

Contoso bere v úvahu tyto faktory:

- Směrovací tabulka obsahuje sadu pravidel (trasy), které určují, jak se mají směrovat pakety odeslané z Managed Instance ve virtuální síti.
- Směrovací tabulka je spojené s podsítí, ve kterých jsou nasazené spravované instance. Jednotlivé pakety, které se zasílají podsítě se určují podle přidružené směrovací tabulky.
- Podsíť lze přidružit pouze jednu směrovací tabulku.
- Nejsou žádné další poplatky za vytvoření směrovací tabulky ve službě Microsoft Azure.

 Nastavení směrování Contoso správci takto:

1. Vytváření tabulky směrování definovaného uživatelem (trasy) v **ContosoNetworkingRG** skupinu prostředků.

    ![Tabulka směrování](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Pro dosažení souladu s požadavky na Managed Instance, za tabulkou směrování (**MIRouteTable**) je nasazen, přidejte trasu, která má předponu adresy 0.0.0.0/0. **Typem dalšího segmentu směrování** je možnost nastavená na **Internet**.

    ![Předpona trasy tabulky](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. jejich přidružení směrovací tabulky s **SQLMI-DB-EUS2** podsítě (v **připojení typu VNET-SQLMI EUS2** sítě). 

    ![Směrovací tabulky podsítě](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Potřebujete další pomoc?*

Zjistěte, jak [nastavit trasy pro Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

### <a name="create-a-managed-instance"></a>Vytvoření Managed Instance

Správce společnosti Contoso, teď můžete zřídit SQL Database Managed Instance:

1. Protože Managed Instance poskytuje obchodní aplikace, nasazují Managed Instance v primární oblasti USA – východ 2 vaší společnosti. Přidání Managed Instance pro **ContosoRG** skupinu prostředků.
2. Výběrem cenové úrovně, velikost výpočetních a úložiště pro instanci. Další informace o [ceny Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![MI](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Po nasazení Managed Instance je dvě nové prostředky joinkind **ContosoRG** skupina prostředků:

    - Virtuální cluster v případě Contoso má několik spravovaných instancí.
    - SQL Server Database Managed Instance. 

      ![MI](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Potřebujete další pomoc?*

Zjistěte, jak [zřízení spravované Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-migration-service"></a>Krok 2: Připravte službu Database Migration Service

Připravit službu Database Migration Service, třeba správce společnosti Contoso provést pár věcí:

- V Azure, zaregistrujte poskytovatele služby Database Migration Service.
- Poskytnout službu Database Migration Service přístup ke službě Azure Storage pro ukládání záložních souborů, které se používají k migraci databáze. Pokud chcete poskytnout přístup ke službě Azure Storage, vytvoří kontejner úložiště objektů Blob v Azure. Která generují identifikátor URI SAS pro kontejner úložiště objektů Blob. 
- Vytvořte projekt Database Migration Service.

Potom proveďte následující kroky:

1. Aby se zaregistrovali poskytovatele migrace databáze v rámci svého předplatného.
    ![Služba Database Migration Service – registr](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Vytvoří kontejner úložiště objektů Blob. Contoso vygeneruje identifikátor URI SAS, tak, aby služba Database Migration Service k němu přístup.

    ![Database Migration Service – vygenerovat identifikátor URI SAS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Vytvoří instanci služby Database Migration Service. 

    ![Database Migration Service – vytvoření instance](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Vložení instanci služby Database Migration Service ve **PROD. řadič domény EUS2** podsíti **připojení typu VNET-PROD-řadiče domény – EUS2** virtuální sítě.
    - Služba Database Migration Service je tady umístit, protože služba musí být ve virtuální síti s přístupem k místní virtuální počítač SQL Server přes bránu VPN.
    - **Připojení typu VNET-PROD-EUS2** je v partnerském vztahu k **připojení typu VNET-HUB-EUS2** a může používat vzdálené brány. **Používat vzdálené brány** možnost zajišťuje, že služba Database Migration Service může komunikovat podle potřeby.

        ![Database Migration Service – konfigurace sítě](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Potřebujete další pomoc?*

- Zjistěte, jak [nastavit službu Database Migration Service](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Zjistěte, jak [vytvoření a použití SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Krok 3: Příprava Azure pro službu Site Recovery

Několik prvků Azure jsou požadovány pro Contoso nastavení Site Recovery pro migraci jeho webové vrstvy virtuálního počítače (**WEBMV**):

- Virtuální síť, ve kterém jsou umístěny prostředky převzetím služeb při selhání.
- Účet úložiště k ukládání replikovaných dat. 
- Trezor služby Recovery Services v Azure.

Správce společnosti Contoso nastavení Site Recovery následujícím způsobem:

1. Vzhledem k tomu, že je virtuální počítač webového front-endu do aplikace SmartHotel360, Contoso převezme při selhání virtuálního počítače do jeho existující produkční sítě (**připojení typu VNET-PROD-EUS2**) a podsíti (**PROD-FE – EUS2**). Síť a podsíť, jsou umístěny v primární oblasti USA – východ 2. Nastavit síť společnosti Contoso při jeho [nasadili infrastrukturu Azure](contoso-migration-infrastructure.md).
2. Vytváření účtu úložiště (**contosovmsacc20180528**). Společnost Contoso využívá účet pro obecné účely. Contoso vybere úložiště úrovně standard a místně redundantního úložiště replikace.

    ![Site Recovery – vytvoření účtu úložiště](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Pomocí účtu sítě a úložiště na místě, vytvořte trezor (**ContosoMigrationVault**). Umístí trezoru ve společnosti Contoso **ContosoFailoverRG** skupinu prostředků, v primární oblasti USA – východ 2.

    ![Recovery Services – vytvoření trezoru](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Potřebujete další pomoc?*

Zjistěte, jak [nastavení Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Příprava VMware v místním prostředí pro Site Recovery

Příprava VMware pro Site Recovery, musí správce společnosti Contoso dokončení těchto úloh:

- Příprava účtu na vCenter serveru instance nebo hostiteli vSphere ESXi. Účet automatizuje zjišťování virtuálních počítačů.
- Příprava účtu, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware, který Contoso chce replikovat.
- Příprava místních virtuálních počítačů pro připojení k virtuálním počítačům Azure při jejich vytvoření po převzetí služeb při selhání.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automatické zjišťování virtuálních počítačů. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Contoso potřebuje účet, který může spouštět operace, jako je například vytváření a odebírání disků a zapnutí na virtuálních počítačích.

Správce společnosti Contoso nastavit účet po dokončení těchto úloh:

1. Vytvoří roli na úrovni vCenter.
2. Přiřadí potřebná oprávnění k této roli.

*Potřebujete další pomoc?*

Zjistěte, jak [vytvořit a přiřadit role pro automatické zjišťování](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Musíte nainstalovat službu Mobility na virtuálním počítači, který Contoso chce replikovat. Contoso bere v úvahu tyto faktory týkající se služby Mobility:

- Site Recovery můžete provést automatickou nabízenou instalaci této součásti, pokud Contoso povolí replikaci příslušného virtuálního počítače.
- Pro automatické nabízené instalace Contoso musíte připravit účet, který Site Recovery používá pro přístup k virtuálnímu počítači.
- Tento účet je zadán, pokud je nakonfigurovaná replikace v konzole Azure.
- Společnosti Contoso musí mít účet domény nebo místní účet s oprávněními k instalaci na virtuálním počítači.

*Potřebujete další pomoc*

Zjistěte, jak [vytvořit účet pro nabízenou instalaci služby Mobility](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání do Azure Contoso chce mít možnost se připojit k replikované virtuální počítače v Azure. Pro připojení k replikované virtuální počítače v Azure, musí správce společnosti Contoso dokončit několik úkolů na místní virtuální počítač před migrací: 

1. Přístup přes internet umožňují RDP na místním virtuálním počítači před převzetí služeb při selhání. Zajišťují, že jsou přidaná pravidla TCP a UDP pro **veřejné** profil a že je povolený protokol RDP v **brány Windows Firewall** > **povolené aplikace** pro všechny profily.
2. Přístup přes síť VPN typu site-to-site společnosti Contoso umožňují na místním počítači protokol RDP. Povolit RDP v **brány Windows Firewall** > **povolené aplikace a funkce** pro **doménovou a privátní** sítě.
3. Nastavují zásada SAN operačního systému na virtuálním počítači s místními **OnlineAll**.

Správce společnosti Contoso se také muset zkontrolovat tyto položky při spuštění převzetí služeb při selhání:

- Měla by existovat žádné čekající aktualizace Windows na virtuálním počítači při aktivaci převzetí služeb při selhání. Pokud jsou čekající aktualizace Windows, uživatelé Contoso nemůže přihlásit k virtuálnímu počítači až po dokončení aktualizace.
- Po převzetí služeb při selhání, musí správce zkontrolovat **Diagnostika spouštění** zobrazíte snímek obrazovky virtuálního počítače. Pokud nemůže zobrazit diagnostiku spouštění, by měl zkontrolovat, že je virtuální počítač spuštěný a poté zkontrolovat [tipy pro řešení potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure"></a>Krok 5: Replikace místních virtuálních počítačů do Azure

Před spuštěním migrace do Azure, správce společnosti Contoso muset nastavit a povolení replikace pro místní virtuální počítač.

### <a name="set-a-replication-goal"></a>Nastavení cíle replikace

1. V trezoru v části název_trezoru (**ContosoVMVault**), nastavují cíle replikace (**Začínáme** > **Site Recovery**  >   **Příprava infrastruktury**).
2. Určí, že jsou počítače umístěny v místním, že jsou virtuální počítače VMware, které se replikují do Azure.

    ![Příprava infrastruktury – cíl ochrany](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Chcete-li pokračovat, správce společnosti Contoso potvrďte, že jejich dokončili jste plánování nasazení. Vyberou **Ano, mám to hotové**. V tomto nasazení Contoso migruje pouze jeden virtuální počítač, plánování nasazení není potřeba.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nyní správce společnosti Contoso konfigurace zdrojového prostředí. K jeho zdrojové prostředí nastavíte, jejich stáhněte šablonu OVF a použít k nasazení konfiguračního serveru a jeho přidružených součásti jako vysoce dostupný, místní virtuální počítač VMware. Součásti na serveru zahrnují:

- Konfigurační server koordinuje komunikaci mezi místní infrastruktuře i v Azure. Konfigurační server spravuje replikační data.
- Procesový server, který funguje jako replikační brána. Procesový server:
    - Přijímá data replikace.
    - Data replikace, optimalizuje pomocí ukládání do mezipaměti, komprese a šifrování.
    - Data replikace odešle do služby Azure Storage.
- Procesní server nainstaluje služba Mobility na virtuálních počítačích, které se budou replikovat. Procesový server provádí automatické zjišťování místních virtuálních počítačů VMware.
- Po vytvoření a spuštění virtuálního počítače konfiguračního serveru Contoso zaregistruje server v trezoru.

Nastavit zdrojové prostředí správce společnosti Contoso takto:

1. Šablona OVF stáhnou z webu Azure portal (**připravit infrastrukturu** > **zdroj** > **konfigurační Server**).
    
    ![Přidání konfiguračního serveru](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Jejich import šablony do VMware. Tím vytvoříte a nasadíte virtuální počítač.

    ![Nasazení šablony OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Po jejich převodu na virtuálním počítači poprvé, spustí se v prostředí instalace Windows serveru 2016. Přijměte licenční smlouvu a zadání hesla správce.
4. Po dokončení instalace se přihlášení k virtuálnímu počítači jako správce. Při první čas přihlášení automaticky spustí konfigurační nástroj Azure Site Recovery.
5. V nástroji pro konfiguraci obnovení lokality, zadejte název pro použijte k registraci konfiguračního serveru v trezoru.
6. Nástroj zkontroluje připojení Virtuálního počítače do Azure. Po připojení se naváže, vyberou **přihlášení** pro přihlášení k předplatnému Azure. Přihlašovací údaje musí mít přístup k trezoru, ve které je zaregistrovaný konfigurační server. 

    ![Registrace konfiguračního serveru](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Nástroj provede několik konfiguračních úloh a pak restartuje počítač. Přihlášení k počítači znovu. Automaticky se spustí Průvodce správou konfiguračního serveru.
8. V průvodci, vyberte síťovou kartu pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení konfigurace.
9. Vyberte předplatné, skupinu prostředků a trezor služby Recovery Services, ve kterém zaregistrujte konfigurační server.

    ![Vyberte trezor služby Recovery Services](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Stáhněte si a nainstaluje MySQL Server a VMWare PowerCLI. Nakonec ověří nastavení serveru.
11. Po ověření, zadejte plně kvalifikovaný název domény nebo IP adresa serveru vCenter hostiteli vSphere nebo instance. Ponechte výchozí port a zadejte zobrazovaný název pro instanci serveru vCenter v Azure.
12. Zadejte účet, který jste vytvořili dříve tak, aby Site Recovery může automaticky zjistit virtuální počítače VMware, které jsou dostupné pro replikaci. 
13. Zadání přihlašovacích údajů, tak po povolení replikace se automaticky nainstaluje službu Mobility. Pro počítače s Windows účet potřebuje oprávnění místního správce na virtuálních počítačích. 

    ![Konfigurace systému vCenter Server](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Po dokončení registrace, na webu Azure Portal, se znova ověříte, že konfigurační server a VMware server jsou uvedeny na **zdroj** stránky v trezoru. Zjišťování může trvat 15 minut nebo déle. 
8. Site Recovery se připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavení cíle

Správce společnosti Contoso, nakonfigurujte cílové prostředí replikace:

1. V **připravit infrastrukturu** > **cílové**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště a síť do zadaného cíle.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Pokud zdroj a cíl jsou nastavena tak, Contoso správci vytvořit zásady replikace a zásadu přidruží k konfigurační server:

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružit**, vytvoří **ContosoMigrationPolicy** zásad.
2. Používají výchozí nastavení:
    - **Prahová hodnota cíle bodu obnovení**: Výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**: Výchozí hodnotu 24 hodin. Tato hodnota určuje, jak dlouho je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímků konzistentní vzhledem k**: Výchozí hodnotu 1 hodina. Tato hodnota určuje frekvenci, s jakou se vytvořit snímky konzistentní s aplikací.
 
    ![Zásady replikace – vytvořit](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Zásady replikace - přidružení](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Potřebujete další pomoc?*

- Úplný návod tohoto těchto kroků si můžete přečíst [nastavit zotavení po havárii pro virtuální počítače VMware v místním](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici při [zdrojové prostředí nastavíte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfiguračního serveru](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [konfigurace nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Povolení replikace

Správce společnosti Contoso teď můžete spustit replikaci WebVM.

1. V **replikovat aplikaci** > **zdroj** > **replikovat**, vyberou nastavení zdroje.
2. Indikuje, že chtějí povolit virtuální počítače, vybrat instanci serveru vCenter a nastavit konfigurační server.

    ![Povolení replikace - zdroje](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Určí nastavení cíle, včetně skupiny prostředků a síť, ve kterém virtuálním počítači Azure budou umístěné po převzetí služeb při selhání. Zadejte účet úložiště, ve kterém se ukládají replikovaná data.

    ![Povolení replikace - cíl](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Vyberou **WebVM** pro replikaci. Po povolení replikace Site Recovery nainstaluje službu Mobility na každém virtuálním počítači. 

    ![Povolení replikace – vyberte virtuální počítač](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Zkontrolujte, že je vybraná správná zásada replikace a povolení replikace pro **WEBVM**. Sledují průběh replikace v **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
6. V **Essentials** na webu Azure Portal, můžete zobrazit stav pro virtuální počítače, které replikujete do Azure:

    ![Zobrazení infrastruktury](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Potřebujete další pomoc?*

Úplný návod tohoto těchto kroků si můžete přečíst [povolit replikaci](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database"></a>Krok 6: Migrace databáze 

Správce společnosti Contoso je potřeba vytvořit projekt Database Migration Service a potom databázi migrujte.

### <a name="create-a-database-migration-service-project"></a>Vytvořte projekt Database Migration Service

1. Vytvoří projekt Database Migration Service. Vyberou **systému SQL Server** serveru typ zdroje a **Azure SQL Database Managed Instance** jako cíl.

     ![Služba Database Migration Service – nový projekt migrace](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Otevře se Průvodce migrací.

### <a name="migrate-the-database"></a>Migrace databáze 

1. V Průvodci migrací určí zdrojového virtuálního počítače, na kterém se nachází v místní databázi. Zadání přihlašovacích údajů pro přístup k databázi.

    ![Služba Database Migration Service – podrobnosti zdroje](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Výběrem databáze, kterou chcete migrovat (**SmartHotel.Registration**):

    ![Služba Database Migration Service – vyberte zdrojové databáze](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Pro cíl že zadejte název Managed Instance v Azure a přihlašovací údaje pro přístup.

    ![Služba Database Migration Service – podrobnosti o cíli](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. V **novou aktivitu** > **spustit migraci**, určují nastavení pro spuštění migrace:
    - Zdroj a cíl přihlašovací údaje.
    - Databáze, kterou chcete migrovat.
    - Síťové sdílené složky vytvořené v místní virtuální počítač. Služba Database Migration Service přebírá zálohování zdroje k této sdílené složce. 
        - Účet služby, na kterém běží instance systému SQL Server zdroje musí mít oprávnění k zápisu v této sdílené složce.
        - Musíte použít plně kvalifikovaný název domény cesta ke sdílené složce.
    - Identifikátor URI SAS, který poskytuje službu Database Migration Service přístup ke kontejneru účtu úložiště, ke kterému služba nahraje soubory zálohy pro migraci.

        ![Database Migration Service – konfigurace nastavení migrace](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Uložte nastavení migrace a potom spusťte migrace.
6. V **přehled**, i sledování stavu migrace.

    ![Služba Database Migration Service – monitorování](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Po dokončení migrace, ověřte, že cílová databáze existují na Managed Instance.

    ![Database Migration Service: ověření migrace databáze](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm"></a>Krok 7: Migrace virtuálního počítače

Spustit rychlou správce společnosti Contoso testovací převzetí služeb při selhání a potom migrovat virtuální počítač.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Před migrací WEBVM, testovací převzetí služeb zajišťuje, že vše funguje podle očekávání. Správcům, aby provedli následující kroky:

1. Spustit testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovaný**).
2. Vyberou **před spuštěním převzetí služeb při selhání vypnout počítač**. Tato možnost aktivní Site Recovery se pokusí před aktivuje převzetí služeb při selhání vypnout zdrojový virtuální počítač. Převzetí služeb při selhání bude pokračovat, i když se vypnutí nepovede. 
3. Spuštění testovacího převzetí služeb při selhání: 
    1. Spuštění na ověřit, že jsou splněné všechny podmínky, které jsou požadována pro migraci kontrol předpokladů.
    2. Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud je vybrán poslední bod obnovení, se z dat vytvoří bod obnovení.
    3.  Vytvoření virtuálního počítače Azure s použitím dat zpracovaných v předchozím kroku.
3. Po dokončení převzetí služeb se zobrazí na webu Azure Portal repliku virtuálního počítače Azure. Po ověření, že vše správně fungovalo: je virtuální počítač odpovídající velikost, je připojený ke správné síti a je spuštěná. 
4. Po ověření testu převzetí služeb, jejich vyčištění převzetí služeb při selhání a zaznamenejte jakékoli připomínky. 

### <a name="migrate-the-vm"></a>Migrace virtuálního počítače

1. Po ověření, zda testovací převzetí služeb fungovala podle očekávání, správce společnosti Contoso vytvořit plán obnovení pro migraci a WEBVM přidat do plánu:

     ![Vytvoření plánu obnovení – vyberte položky, které](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Spuštění převzetí služeb při selhání v plánu výběrem posledního bodu obnovení. Určí, že měl pokusit vypněte místní virtuální počítač předtím, než se aktivuje převzetí služeb při selhání Site Recovery.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Po převzetí služeb při selhání ověřují, že se virtuální počítač Azure zobrazuje podle očekávání na webu Azure Portal.

   ![Podrobnosti o plánu obnovení](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Po ověření, že po dokončení migrace na dokončení procesu migrace, zastavuje se replikace pro virtuální počítač a ukončit účtování virtuálního počítače Site Recovery.

    ![Převzetí služeb při selhání – dokončení migrace](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Aktualizujte připojovací řetězec

Správce společnosti Contoso jako poslední krok v procesu migrace, aktualizovat připojovací řetězec aplikace tak, aby odkazoval na migrované databáze, na kterém běží na spravované instanci společnosti Contoso.

1. Na webu Azure Portal, které se vyskytnout připojovací řetězec tak, že vyberete **nastavení** > **připojovací řetězce**.

    ![Připojovací řetězce](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Řetězec, aktualizujte uživatelské jméno a heslo SQL Database Managed Instance.
3. Po dokončení konfigurace řetězec nahrazují aktuálního připojovacího řetězce v souboru web.config své aplikace.
4. Po aktualizaci souboru a jeho uložení, restartujte službu IIS na WEBVM spuštěním `IISRESET /RESTART` v okně příkazového řádku.
5. Po restartování služby IIS aplikace používá databázi, která běží na spravované instanci SQL Database.
6. V tomto okamžiku se můžete vypnout v místním počítači SQLVM. Migrace byla dokončena.

*Potřebujete další pomoc?*

- Zjistěte, jak [spustit testovací převzetí služeb](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Zjistěte, jak [vytvořit plán obnovení](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Zjistěte, jak [převzetí služeb při selhání do Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

S kompletní migrací aplikace SmartHotel360 běží na Virtuálním počítači Azure a je k dispozici v Azure SQL Database Managed Instance databáze SmartHotel360.  

Contoso potřebuje, a proveďte následující úlohy čištění:  

- Odebrání počítače WEBVM z inventáře serverů vCenter.
- Odeberte počítač SQLVM z inventáře serverů vCenter.
- Odeberte WEBVM a SQLVM z místní úlohy zálohování.
- Aktualizace zobrazíte nové umístění a IP adresu pro WEBVM interní dokumentaci.
- Odeberte SQLVM interní dokumentaci ke službě. Alternativně Contoso zkontrolovat, jestli v dokumentaci k zobrazení SQLVM jako odstraněný a už ve virtuálním počítači inventáře.
- Zkontrolujte všechny prostředky, které pracují s Vyřazená z provozu virtuálních počítačů. Aktualizujte všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznění a zabezpečte své nové infrastruktury.

### <a name="security"></a>Zabezpečení

Bezpečnostní tým Contoso kontroly virtuálních počítačů Azure a SQL Database Managed Instance, které chcete zkontrolovat všechny bezpečnostní problémy s jeho implementace:

- Tým kontroluje skupiny zabezpečení sítě, které se používají k řízení přístupu pro virtuální počítač. Můžete předat skupiny pomáhají zajistit, že pouze provoz, která může aplikace zabezpečení sítě.
- Bezpečnostní tým společnosti Contoso také zvážení zabezpečení dat na disku s použitím Azure Disk Encryption a služby Azure Key Vault.
- Tým umožňuje detekce hrozeb na Managed Instance. Detekce hrozeb odešle oznámení do systému. zabezpečení týmu nebo služby podpory společnosti Contoso chcete otevřít lístek, pokud se zjistí hrozbu. Další informace o [pro Managed Instance detekce hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Managed Instance zabezpečení – detekce hrozeb](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Další informace o postupech zabezpečení pro virtuální počítače najdete v tématu [osvědčené postupy zabezpečení pro úlohy IaaS v Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control).

### <a name="bcdr"></a>BCDR

Společnosti Contoso pro provozní kontinuitu a zotavení po havárii (BCDR), provede následující akce:

- Bezpečnost dat: Contoso zálohuje data na virtuálních počítačích pomocí služby Azure Backup. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Udržujte aplikace rychle zprovoznit: Contoso replikuje aplikace virtuální počítače v Azure do sekundární oblasti vzdálené používání služby Site Recovery. [Další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).
- Contoso zjišťuje informace o správě spravované instanci SQL, včetně [záloh databází](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).


### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Contoso má existující licencování pro WEBVM. Abyste mohli využívat ceny s programem Azure Hybrid Benefit, převede Contoso existujícího virtuálního počítače Azure.
- Contoso umožňuje Azure Cost Management licencuje Cloudyn, pobočka Microsoftu. Cost Management je řešení správy nákladů multicloudové, která pomáhá společnosti Contoso, používat a spravovat Azure a další cloudové prostředky. Další informace o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Závěr

V tomto článku se ke Contoso kolizi SmartHotel360 aplikace v Azure a migrujte aplikace front-endového virtuálního počítače do Azure pomocí služby Site Recovery. Contoso migruje místní databáze Azure SQL Database Managed Instance s využitím Azure Database Migration Service.

## <a name="next-steps"></a>Další postup

V dalším článku v této sérii Contoso [ke kolizi aplikace SmartHotel360 na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) pomocí služby Azure Site Recovery.

