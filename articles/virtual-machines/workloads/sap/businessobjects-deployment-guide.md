---
title: Nasazení platformy SAP BusinessObjects BI na platformě Azure | Microsoft Docs
description: Plánování, nasazení a konfigurace platformy SAP BusinessObjects BI v Azure
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 9d1759e5b809bc40e63fb6024fb7f107ad347da6
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094283"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Příručka pro plánování a implementaci SAP BusinessObjects BI Platform v Azure

## <a name="overview"></a>Přehled

Účelem tohoto průvodce je poskytnout pokyny pro plánování, nasazování a konfiguraci platformy SAP BusinessObjects BI, označované také jako platforma SAP BOBI v Azure. Tato příručka je určená pro pokrytí běžných služeb a funkcí Azure, které jsou relevantní pro platformu SAP BOBI. Tato příručka není vyčerpávajícím seznamem všech možných možností konfigurace. Zahrnuje řešení společná pro typické scénáře nasazení.

Tato příručka nemá za cíl nahradit standardní příručky pro instalaci a správu systému SAP BOBI Platform, operační systém nebo jakoukoli dokumentaci k databázi.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Plánování a implementace platformy SAP BusinessObjects BI v Azure

Microsoft Azure nabízí celou řadu služeb, jako jsou výpočetní prostředky, úložiště, sítě a mnoho dalších, aby si firmy mohli sestavovat své aplikace bez zdlouhavých zásobovacích cyklů. Společnosti Azure s podporou virtuálních počítačů můžou nasazovat a škálovat výpočetní prostředky na vyžádání pro různé aplikace SAP, jako jsou aplikace založené na SAP NetWeaver, SAP Hybris, SAP BusinessObjects BI Platform na základě jejich obchodních potřeb. Azure podporuje i připojení mezi různými místy, což umožňuje společnostem integrovat virtuální počítače Azure do jejich místních domén, jejich privátních cloudů a jejich systému SAP na šířku.

Tento dokument poskytuje pokyny pro plánování a implementaci pro platformu SAP BusinessObjects BI na platformě Azure. Doplňuje dokumentaci k instalaci SAP a poznámky SAP, které reprezentují primární prostředky pro instalace a nasazení SAP BOBI.

### <a name="architecture-overview"></a>Přehled architektury

Platforma SAP BusinessObjects BI je samostatný systém, který může existovat na jednom virtuálním počítači Azure nebo může být škálované na cluster mnoha Virtual Machines Azure, které spouštějí různé komponenty. Platforma SAP BOBI se skládá ze šesti koncepčních vrstev: vrstva klienta, webová vrstva, úroveň správy, vrstva úložiště, úroveň zpracování a Datová vrstva. (Další podrobnosti o jednotlivých úrovních najdete v příručce pro správce v tématu [SAP BusinessObjects Business Intelligence Platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) Help Portal). Níže jsou uvedeny podrobnosti vysoké úrovně na každé úrovni:

- **Vrstva klienta:** Obsahuje všechny aplikace desktopových klientů, které komunikují s platformou BI a poskytují různé možnosti vytváření sestav, analytické funkce a správy.
- **Webová vrstva:** Obsahuje webové aplikace nasazené na servery webových aplikací JAVA. Webové aplikace poskytují koncovým uživatelům funkce platformy BI prostřednictvím webového prohlížeče.
- **Úroveň správy:** Koordinuje a ovládá všechny komponenty, které tvoří platformu BI. Zahrnuje server Centrální správy (CMS) a server událostí a přidružené služby.
- **Úroveň úložiště:** Zodpovídá za zpracování souborů, jako jsou dokumenty a sestavy. Také zpracovává ukládání sestav do mezipaměti, aby se ukládaly systémové prostředky při přístupu k sestavám uživatelů.
- **Úroveň zpracování:** Analyzuje data a vytváří sestavy a další typy výstupu. Je to jediná vrstva, která přistupuje k databázím, které obsahují data sestavy.
- **Datová vrstva:** Skládá se z databázových serverů, které hostují systémové databáze CMS a úložiště dat auditování.

Platforma SAP BI se skládá z kolekce serverů, které běží na jednom nebo víc hostitelích. Je nutné zvolit správnou strategii nasazení na základě velikosti, obchodních potřeb a typu prostředí. Pro malou instalaci, jako je vývoj nebo testování, můžete použít jeden virtuální počítač Azure pro webový aplikační server, databázový server a všechny servery platformy BI. V případě, že používáte nabídku databáze jako služby (DBaaS) z Azure, databázový server se spustí odděleně od jiných komponent. Pro střední a velkou instalaci můžete mít servery běžící na několika virtuálních počítačích Azure.

V níže uvedeném obrázku se zobrazuje architektura rozsáhlého nasazení platformy SAP BOBI Platform na virtuálních počítačích Azure, kde je každá součást distribuována a umístěna do skupin dostupnosti, které mohou při výpadku služby tolerovat převzetí služeb při selhání.

![Architektura platformy SAP BusinessObjects BI v Azure](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Podrobnosti architektury

- Nástroj pro vyrovnávání zatížení

  V případě nasazení s více instancemi SAP BOBI jsou webové aplikační servery (nebo webová vrstva) spuštěné na dvou nebo více hostitelích. K rovnoměrnému zatížení uživatelů mezi webovými servery můžete použít nástroj pro vyrovnávání zatížení mezi koncovými uživateli a webovými servery. V Azure můžete použít [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) nebo [Azure Application Gateway](../../../application-gateway/overview.md) ke správě provozu na webových serverech.

- Servery webových aplikací

  Webový Server hostuje webové aplikace platformy SAP BOBI, jako je třeba CMC a panel pro spuštění BI. Aby bylo možné dosáhnout vysoké dostupnosti pro webový server, je nutné nasadit alespoň dva servery webových aplikací, abyste mohli spravovat redundanci a vyrovnávání zatížení. V Azure je možné tyto servery webových aplikací umístit do skupin dostupnosti nebo zón dostupnosti pro zajištění vyšší dostupnosti.

  Tomcat je výchozí webová aplikace pro platformu SAP BI. Pokud chcete dosáhnout vysoké dostupnosti pro Tomcat, Povolte replikaci relace pomocí [zachytávací statického](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) korelačního členství v Azure. Zajišťuje, aby uživatel měl přístup k webové aplikaci SAP BI i v případě, že dojde k přerušení služby Tomcat.

  > [!Important]
  > Ve výchozím nastavení používá Tomcat IP adresy a porty vícesměrového vysílání pro clusteringu, které se v Azure nepodporují (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- Servery platformy BI

  Servery platformy BI obsahují všechny služby, které jsou součástí aplikace SAP BOBI (úroveň správy, úroveň zpracování a vrstva úložiště). Když webový server obdrží požadavek, detekuje každý server platformy BI (konkrétně všechny servery CMS v clusteru) a automaticky vyrovnává zátěž svých požadavků. V případě, že se některé z hostitelů platformy BI nezdařily, webový server automaticky odešle požadavky na jiného hostitele.

  Pokud chcete dosáhnout vysoké dostupnosti nebo redundance pro platformu BI, musíte aplikaci nasadit aspoň na dva virtuální počítače Azure. Na základě velikosti můžete škálovat platformu BI tak, aby běžela na dalších virtuálních počítačích Azure.

- Server úložiště souborů (FRS)

  Server úložiště souborů obsahuje všechny sestavy a další dokumenty v BI, které byly vytvořeny. V nasazení s více instancemi jsou servery platforem BI spuštěné na několika virtuálních počítačích a každý virtuální počítač by měl mít přístup k těmto sestavám a dalším dokumentům BI. Systém souborů proto musí být sdílen napříč všemi platformami serverů BI.

  V Azure můžete buď použít [soubory Azure Premium](../../../storage/files/storage-files-introduction.md) nebo [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) pro server úložiště souborů. Obě tyto služby Azure mají integrovanou redundanci.

  > [!Important]
  > Protokol SMB pro soubory Azure je všeobecně dostupný, ale podpora protokolu NFS pro soubory Azure je aktuálně ve verzi Preview. Další informace najdete v tématu [Podpora NFS 4,1 pro soubory Azure je teď ve verzi Preview](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) .

- Databáze CMS & audit
  
  Platforma SAP BOBI vyžaduje, aby databáze ukládala svá systémová data, která se označují jako databáze CMS. Slouží k ukládání informací o platformě BI, jako jsou informace o uživateli, serveru, složce, dokumentu, konfiguraci a ověřování.

  Azure nabízí nabídku databáze [MySQL](https://azure.microsoft.com/en-us/services/mysql/) a databázi [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) jako službu (DBaaS), kterou je možné použít pro databázi CMS a databázi auditu. Protože se jedná o nabídku PaaS, zákazníci si nemusí dělat starosti s provozem, dostupností a údržbou databází. Zákazník může také zvolit vlastní databázi pro CMS a úložiště auditu na základě jejich obchodních potřeb.

## <a name="support-matrix"></a>Matice podpory

Tato část popisuje podporu různých komponent SAP BOBI, jako je SAP BusinessObjects BI Platform verze, operační systém a databáze v Azure.

### <a name="sap-businessobjects-bi-platform"></a>Platforma SAP BusinessObjects BI

Infrastruktura jako služba (IaaS) Azure umožňuje nasazovat a konfigurovat platformu SAP BusinessObjects BI na výpočetním prostředí Azure. Podporuje následující verzi platformy SAP BOBI –

- Platforma SAP BusinessObjects BI Platform 4,3
- Platforma SAP BusinessObjects BI Platform 4,2 SP04 +
- Platforma SAP BusinessObjects BI Platform 4,1 SP05 +

Platforma SAP BI běží na různých operačních systémech a databázích. Podporu platformy SAP BOBI mezi operačním systémem a verzí databáze najdete v části [dostupnost produktu](https://apps.support.sap.com/sap/support/pam) pro SAP BOBI.

### <a name="operating-system"></a>Operační systém

Azure podporuje nasazení platforem SAP BusinessObjects BI v následujících operačních systémech.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

Verze operačního systému uvedená ve službě [pam (Product Availability Matrix) pro SAP BUSINESSOBJECTS BI Platform](https://support.sap.com/pam) je podporovaná tak dlouho, dokud jsou kompatibilní se spouštěním v infrastruktuře Azure.

### <a name="databases"></a>Databáze

Platforma BI potřebuje databázi pro CMS a úložiště dat auditování, která se dá nainstalovat na všechny podporované databáze uvedené v tabulce [dostupnosti produktu SAP](https://support.sap.com/pam) , která obsahuje následující:

- Microsoft SQL Server

- [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) (podporovaná databáze pouze pro platformu SAP BOBI v systému Windows)

  Je to plně spravovaný databázový stroj SQL Server na základě nejnovější stabilní edice Enterprise systému SQL Server. Azure SQL Database zpracovává většinu funkcí správy databází, jako je upgrade, opravy a monitorování bez zásahu uživatele. Pomocí Azure SQL Database můžete vytvořit vysoce dostupnou a výkonnou vrstvu datového úložiště pro aplikace a řešení v Azure. Další podrobnosti najdete v dokumentaci [Azure SQL Database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) .

- [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (řiďte se stejnými pokyny pro kompatibilitu, jak je uvedeno pro MySQL AB v nástroji SAP pam)

  Jedná se o službu relačních databází, kterou používá edice MySQL Community Edition. Je to plně spravovaná nabídka databáze jako služba (DBaaS), která dokáže zvládnout klíčové úlohy s předvídatelným výkonem a dynamickou škálovatelností. Má vestavěnou vysokou dostupnost, automatické zálohování, opravy softwaru, automatickou detekci selhání a obnovení k bodu v čase po dobu až 35 dnů, což podstatně snižuje provozní úlohy. Další podrobnosti najdete v dokumentaci [Azure Database for MySQL](../../../mysql/overview.md) .

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (pro verzi a omezení zkontrolujte SAP Note [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

Tento dokument popisuje pokyny k nasazení **platformy SAP BOBI ve Windows s** **platformou Azure SQL Database a SAP BOBI platformou v systému Linux s Azure Database for MySQL**. Je to také náš doporučený postup pro provozování platformy SAP BusinessObjects BI na platformě Azure.

## <a name="sizing"></a>Velikosti

Změna velikosti je proces určení hardwarového požadavku na efektivní spuštění aplikace. Pro platformu SAP BOBI je potřeba provést změnu velikosti pomocí nástroje SAP pro změnu velikosti s názvem [rychlé Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer). Tento nástroj poskytuje protokoly SAP na základě vstupu, které pak musí být namapovány na certifikované typy virtuálních počítačů Azure pro SAP. V části SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) najdete seznam podporovaných produktů SAP a typů virtuálních počítačů Azure spolu s protokoly SAP. Další informace o změně velikosti najdete v tématu [Průvodce nastavením velikosti SAP BI](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

V případě potřeby úložiště pro platformu SAP BOBI nabízí Azure různé typy [Managed disks](../../managed-disks-overview.md). Pro instalační adresář SAP BOBI se doporučuje použít spravovaný disk úrovně Premium a pro databázi, která běží na virtuálních počítačích, postupujte podle pokynů uvedených v části [nasazení systému DBMS pro úlohy SAP](dbms_guide_general.md).

Azure podporuje dvě nabídky DBaaS pro datové vrstvy platformy SAP BOBI – [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database) (aplikace BI běžící v systému Windows) a [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql) (aplikace BI běžící v systému Linux a Windows). Díky tomu můžete zvolit nákupní model, který nejlépe vyhovuje vašim potřebám.

> [!Tip]
> V případě odkazu na rychlé změny velikosti zvažte 800 SAP = 1 vCPU při mapování výsledku SAP pro vrstvu databáze platformy SAP BOBI na Azure Database-as-a-Service (Azure SQL Database nebo Azure Database for MySQL).

### <a name="sizing-models-for-azure-sql-database"></a>Změna velikosti modelů pro Azure SQL Database

Azure SQL Database nabízí následující tři nákupní modely:

- založené na vCore

  Umožňuje zvolit počet virtuální jádra, velikost paměti a velikost a rychlost úložiště. Nákupní model založený na vCoreech vám také umožní použít [Zvýhodněné hybridní využití Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) k získání úspory nákladů. Tento model je vhodný pro zákazníky, kteří flexibilitu hodnot, řízení a transparentnost.

  V modelu vCore jsou nabízeny tři [Možnosti úrovně služeb](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) , které zahrnují Pro obecné účely, pro důležité obchodní informace a škálování. Úroveň služby definuje architekturu úložiště, limity v/v a možnosti provozní kontinuity související s dostupností a zotavením po havárii. Níže jsou uvedeny podrobnosti vysoké úrovně pro jednotlivé úrovně služeb –

  1. Úroveň služby **pro obecné účely** se nejlépe hodí pro obchodní úlohy. Nabízí uživatelsky orientované, vyvážené a škálovatelné možnosti výpočtů a úložiště. Další informace najdete v tématu [Možnosti a omezení prostředků](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5).
  2. **Pro důležité obchodní informace** úroveň služeb nabízí obchodním aplikacím nejvyšší odolnost proti chybám pomocí několika izolovaných replik a poskytuje nejvyšší výkon vstupně-výstupních operací na jednu repliku databáze. Další informace najdete v tématu [Možnosti a omezení prostředků](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5).
  3. Úroveň služby pro **škálování** na úrovni služeb je nejvhodnější pro firemní úlohy s vysoce škálovatelnými požadavky na úložiště a škálování pro čtení. Nabízí vyšší odolnost proti chybám tím, že umožňuje konfiguraci více než jedné repliky izolované databáze. Další informace najdete v tématu [Možnosti a omezení prostředků](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

- Model založený na jednotkách DTU

  Nákupní model založený na DTU nabízí kombinaci výpočetních, paměťových a vstupně-výstupních prostředků ve třech úrovních služeb, aby se podporovaly lehké a náročné databázové úlohy. Výpočetní velikosti v rámci jednotlivých vrstev poskytují různé kombinace těchto prostředků, do kterých můžete přidat další prostředky úložiště. Je nejvhodnější pro zákazníky, kteří mají jednoduché a předem nakonfigurované možnosti prostředků.

  [Úrovně služeb](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) v modelu nákupu na základě DTU jsou rozlišené rozsahem výpočetních velikostí s pevnou velikostí zahrnutého úložiště, pevným obdobím uchovávání záloh a pevnou cenou.

- Bez serveru

  Model bez serveru automaticky škáluje výpočetní výkon na základě požadavků na zatížení a fakturuje se za množství výpočetní služby za sekundu. Výpočetní vrstva bez serveru automaticky pozastaví databáze během neaktivních období, kdy se účtuje jenom úložiště, a automaticky obnoví databáze při návratu aktivity. Další informace najdete v tématu [Možnosti a omezení prostředků](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
  
  Je vhodnější pro občasné, nepředvídatelné využití s nízkým využitím výpočetních prostředků v průběhu času. Tento model se proto dá použít pro nasazení SAP BOBI bez produkčního prostředí.

> [!Note]
> V případě SAP BOBI je vhodné použít model založený na vCore a zvolit jednu z Pro obecné účely nebo Pro důležité obchodní informace úrovně služeb podle potřeb podniku.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Změna velikosti modelů pro Azure Database for MySQL

Azure Database for MySQL se dodává se třemi různými cenovými úrovněmi. Jsou rozlišené o množství výpočetních prostředků v virtuální jádra, paměti na vCore a technologii úložiště, která se používá k uložení data. Níže najdete podrobné informace o možnostech a další informace o různých atributech najdete v tématu [cenová úroveň](../../../mysql/concepts-pricing-tiers.md) pro Azure Database for MySQL.

- Základní

  Používá se pro cílové úlohy, které vyžadují lehký výpočetní výkon a vstupně-výstupní výkon.

- Pro obecné účely

  Je vhodný pro většinu obchodních úloh, které vyžadují vyvážené výpočetní prostředky a paměť s škálovatelnou vstupně-výstupní propustností.

- Optimalizováno pro paměť

  Pro vysoce výkonné databázové úlohy, které vyžadují výkon v paměti pro rychlejší zpracování transakcí a vyšší souběžnost.

> [!Note]
> Pro SAP BOBI je vhodné použít cenovou úroveň optimalizovanou pro Pro obecné účely nebo paměť na základě pracovní úlohy.

## <a name="azure-resources"></a>Prostředky Azure

### <a name="choosing-regions"></a>Výběr oblastí

Oblast Azure je jedním nebo několika datovými centry, které obsahují infrastrukturu pro spouštění a hostují různé služby Azure. Tato infrastruktura zahrnuje velký počet uzlů, které fungují jako výpočetní uzly nebo uzly úložiště, nebo spouštějí síťové funkce. Ne všechny oblasti nabízí stejné služby.

Platforma SAP BI obsahuje různé komponenty, které mohou vyžadovat konkrétní typy virtuálních počítačů, úložiště jako soubory Azure nebo Azure NetApp Files nebo databázi jako službu (DBaaS) pro svou datovou vrstvu, která nemusí být k dispozici v určitých oblastech. Přesné informace o typech virtuálních počítačů, Azure Storage typech nebo dalších službách Azure v [produktech dostupných v oblasti](https://azure.microsoft.com/en-us/global-infrastructure/services/) lokality najdete v části. Pokud už máte systémy SAP v Azure, pravděpodobně jste zjistili vaši oblast. V takovém případě je třeba nejprve prozkoumat, zda jsou v těchto oblastech k dispozici potřebné služby k určení architektury platformy SAP BI.

### <a name="availability-zones"></a>Zóny dostupnosti

Zóny dostupnosti jsou fyzicky samostatná umístění v oblasti Azure. Každá zóna dostupnosti se skládá z jednoho nebo více datových center vybavených nezávislými napájením, chlazením a sítěmi.

Abyste dosáhli vysoké dostupnosti na každé úrovni platformy SAP BI, můžete distribuovat virtuální počítače napříč zónou dostupnosti implementací architektury vysoké dostupnosti, která může poskytovat nejlepší smlouvu SLA v Azure. Pro smlouvu SLA pro virtuální počítače v Azure si Projděte nejnovější verzi [virtuálního počítače SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Pro datovou vrstvu služba Azure Database as a Service (DBaaS) poskytuje standardně architekturu vysoké dostupnosti. Stačí jenom vybrat oblast a službu s vysokou dostupností, redundanci a odolností pro zmírnění výpadků databáze z plánovaných a neplánovaných výpadků, aniž byste museli konfigurovat žádné další součásti. Další podrobnosti o smlouvě SLA pro podporovanou nabídku DBaaS v Azure najdete [v části vysoká dostupnost v Azure Database for MySQL](../../../mysql/concepts-high-availability.md) a [vysoká dostupnost pro Azure SQL Database](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Skupiny dostupnosti

Skupina dostupnosti je logická možnost seskupení pro izolaci prostředků virtuálních počítačů, které se nasazují při nasazení. Azure zajišťuje, aby virtuální počítače, které umístíte do skupiny dostupnosti, běžely na několika fyzických serverech, výpočetních stojanech, jednotkách úložiště a síťových přepínačích. Pokud dojde k selhání hardwaru nebo softwaru, bude ovlivněna pouze podmnožina virtuálních počítačů a vaše celkové řešení zůstane v provozu. Takže když jsou virtuální počítače umístěné ve skupinách dostupnosti, kontroler prostředků infrastruktury Azure distribuuje virtuální počítače do různých domén [selhání](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) a [upgradů](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) , aby se zabránilo nedostupnosti všech virtuálních počítačů z důvodu údržby infrastruktury nebo selhání v rámci jedné domény selhání.

Platforma SAP BI obsahuje mnoho různých komponent a při navrhování architektury je nutné zajistit, aby každá z těchto součástí byla odolná proti výpadkům. Dá se dosáhnout umístěním virtuálních počítačů Azure každé součásti v rámci skupin dostupnosti. Mějte na paměti, že při kombinaci virtuálních počítačů různých rodin virtuálních počítačů v rámci jedné skupiny dostupnosti se můžete setkat s problémy, které vám zabrání v zahrnutí určitého typu virtuálního počítače do této skupiny dostupnosti. Měli byste mít samostatnou skupinu dostupnosti pro webovou aplikaci, aplikaci BI pro platformu SAP BI, jak je zvýrazněna v přehledu architektury.

Také počet domén aktualizace a selhání, které může použít Skupina dostupnosti Azure v rámci jednotky škálování Azure, je omezený. Takže pokud budete přidávat virtuální počítače do jedné skupiny dostupnosti, pak dva nebo víc virtuálních počítačů nakonec skončí stejnou chybou nebo aktualizační doménou. Další informace najdete v části [sady dostupnosti Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) v dokumentu plánování a implementace virtuálních počítačů Azure.

Pro pochopení konceptu skupin dostupnosti Azure a způsobu, jakým se skupiny dostupnosti vztahují k doménám selhání a upgradu, přečtěte si článek [Správa dostupnosti](../../manage-availability.md) .

> [!Important]
> Pojmy Zóny dostupnosti Azure a sady dostupnosti Azure se vzájemně vylučují. To znamená, že můžete nasadit pár nebo několik virtuálních počítačů do konkrétní zóny dostupnosti nebo do skupiny dostupnosti Azure. Ale ne obojí.

### <a name="virtual-machines"></a>Virtuální počítače

Virtuální počítač Azure je nabídka služeb, která umožňuje nasadit vlastní image do Azure jako instance infrastruktury jako služby (IaaS). Zjednodušuje údržbu a provoz aplikací tím, že poskytuje výpočetní a úložné úložiště na vyžádání pro hostování, škálování a správu webových aplikací a připojených aplikací.

Azure nabízí pro všechny potřeby svých aplikací odrůdy virtuálních počítačů. Pro úlohy SAP ale Azure omezil výběr na jiné rodiny virtuálních počítačů, které jsou vhodné pro úlohy a SAP HANA úlohy SAP. Pokud chcete získat další informace, podívejte [se, jaký software SAP se podporuje pro nasazení Azure](sap-supported-product-on-azure.md).

Na základě velikosti platformy SAP BI potřebujete namapovat svůj požadavek na virtuální počítač Azure, který je podporovaný v Azure pro produkt SAP. Systém SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) je dobrým výchozím bodem, který vypíše podporované typy virtuálních počítačů Azure pro produkty SAP v systémech Windows a Linux. Také je potřeba mít na paměti, že kromě výběru čistě podporovaných typů virtuálních počítačů je také nutné ověřit, zda jsou tyto typy virtuálních počítačů k dispozici v konkrétní oblasti. Můžete kontrolovat dostupnost typu virtuálního počítače na stránce [Dostupné produkty podle oblasti](https://azure.microsoft.com/global-infrastructure/services/) . Pro výběr cenového modelu se můžete podívat na [úlohy SAP na virtuálních počítačích Azure](planning-guide.md#azure-virtual-machines-for-sap-workload) .

### <a name="storage"></a>Úložiště

Azure Storage je cloudová služba spravovaná v Azure, která poskytuje vysoce dostupné, zabezpečené, odolné, škálovatelné a redundantní úložiště. Některé typy úložišť mají omezené použití pro scénáře SAP. Ale několik typů Azure Storage jsou vhodné nebo optimalizované pro konkrétní scénáře úloh SAP. Další informace najdete v tématu [Azure Storage typy pro Průvodce úlohami SAP](planning-guide-storage.md) , protože vysvětlují různé možnosti úložiště, které jsou vhodné pro SAP.

Azure Storage má pro zákazníky k dispozici jiné typy úložiště a podrobnosti o tom, jak se dá přečíst, najdete v článku [Jaké typy disků jsou k dispozici v Azure?](../../disks-types.md). Platforma SAP BOBI používá k sestavení aplikace následující Azure Storage –

- Disky spravované Azure

  Jedná se o svazek úložiště na úrovni bloku, který je spravovaný Azure. Při instalaci na virtuální počítače Azure můžete použít disky pro aplikační servery a databáze platformy SAP BOBI. K dispozici jsou různé typy [Azure Managed disks](../../managed-disks-overview.md) , ale doporučujeme použít [Premium SSD](../../disks-types.md#premium-ssd) pro aplikace a databázi SAP BOBI Platform.

  V níže uvedeném příkladu se SSD Premium používá pro instalační adresář platformy BOBI. Pro databázi nainstalovanou na virtuálním počítači můžete použít spravované disky pro data a objem protokolu podle pokynů. Databáze CMS a audit jsou obvykle malé a nemají stejné požadavky na výkon úložiště jako u jiných databází SAP OLTP/OLAP.

- Soubory Azure Premium nebo Azure NetApp Files

  V rámci platformy SAP BOBI odkazuje server úložiště souborů (FRS) na adresáře disků, kde jsou uložené obsahy, jako jsou sestavy, základní počítače a připojení, které používají všechny aplikační servery daného systému. [Soubory Azure Premium](../../../storage/files/storage-files-introduction.md) nebo úložiště [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) se dají použít jako systém sdíleného systému souborů pro aplikace SAP BOBI (FRS). Vzhledem k tomu, že tato nabídka úložiště není dostupná pro všechny oblasti, najdete informace [v části produkty dostupné v oblasti](https://azure.microsoft.com/en-us/global-infrastructure/services/) lokalita, kde najdete aktuální informace.

  Pokud služba není ve vaší oblasti k dispozici, můžete vytvořit server NFS, ze kterého můžete sdílet systém souborů do aplikace SAP BOBI. Musíte ale také zvážit jeho vysokou dostupnost.

![Rozložení úložiště platformy SAP BusinessObjects BI v Azure](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Sítě

SAP BOBI je platforma pro vytváření sestav a analýzy, která nedrží žádná firemní data. Proto je systém připojený k jiným databázovým serverům, ze kterých načítá všechna data a poskytuje uživatelům přehled. Azure poskytuje síťovou infrastrukturu, která umožňuje mapování všech scénářů, které se dají realizovat s platformou SAP BI, jako je připojení k místnímu systému, systémy v různých virtuálních sítích a dalších. Další informace najdete v článku [Microsoft Azure sítě pro úlohy SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

V případě nabídky databáze jako služby má jakákoli nově vytvořená databáze (Azure SQL Database nebo Azure Database for MySQL) bránu firewall, která blokuje všechna externí připojení. Pokud chcete povolit přístup ke službě DBaaS z virtuálních počítačů s platformou BI, musíte zadat jedno nebo více pravidel brány firewall na úrovni serveru, abyste mohli povolit přístup k serveru DBaaS. Další informace najdete v části [pravidla brány firewall](../../../mysql/concepts-firewall-rules.md) pro Azure Database for MySQL a [řízení přístupu k síti](../../../azure-sql/database/network-access-controls-overview.md) pro Azure SQL Database.

## <a name="next-steps"></a>Další kroky

- [Nasazení platformy SAP BusinessObjects BI na platformě Linux](businessobjects-deployment-guide-linux.md)
- [Plánování a implementace Azure Virtual Machines pro SAP](planning-guide.md)
- [Nasazení Azure Virtual Machines pro SAP](deployment-guide.md)
- [Nasazení Azure Virtual Machines DBMS pro SAP](dbms-guide.md)