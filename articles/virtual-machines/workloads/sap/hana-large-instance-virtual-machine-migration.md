---
title: Migrace SAP HANA v Azure (velké instance) na virtuální počítače Azure | Microsoft Docs
description: Postup migrace SAP HANA v Azure (velké instance) na virtuální počítače Azure
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154917"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA migrace velkých instancí Azure do Azure Virtual Machines
Tento článek popisuje možné scénáře nasazení rozsáhlých instancí Azure a nabízí plánovací a migrační přístup s minimalizovanými výpadky přechodu.

## <a name="overview"></a>Přehled
Vzhledem k tomu, že oznámení o velkých instancích Azure pro SAP HANA (HLI) v září 2016, tento hardware přijal mnoho zákazníků jako službu pro výpočetní platformu v paměti.  V posledních letech rozšíření škálovatelnosti virtuálních počítačů Azure v kombinaci s podporou modelu nasazení na více instancí HANA překročilo maximální nároky na kapacitu databáze ERP v podnicích pro zákazníky.  Začneme vidět zákazníky, kteří vyjadřují přání migrovat své SAP HANA úlohy z fyzických serverů na virtuální počítače Azure.
Tato příručka není podrobným dokumentem konfigurace, ale popisuje společné modely nasazení a nabízí plánování, radí v migraci s úmyslem volat nezbytné požadavky na přípravu pro minimalizaci výpadků přechodu.

## <a name="assumptions"></a>Předpoklady
Tento článek neposkytuje následující předpoklady:
- Jediným dotčeným zájmem je homogenní migrace databázové služby HANA z databáze Hana (HLI) na virtuální počítač Azure bez významného upgradu softwaru nebo opravy. Tyto dílčí aktualizace zahrnují použití novější verze operačního systému nebo verze HANA výslovně uvedené jako podporované v příslušných poznámkách SAP. 
- Všechny aktivity aktualizace a upgradu, pokud je potřeba provést před migrací nebo po ní.  Například SAP HANA MCOS převádění na nasazení MDC. 
- Postup migrace, který by poskytoval minimální prostoje, je SAP HANA systémové replikaci. Jiné metody migrace nejsou součástí rozsahu tohoto dokumentu.
- To platí pro Rev3 i rev4 SKU HLI.
- Architektura nasazení HANA zůstává během migrace primárně nezměněná.  To znamená, že systém s jednou instancí DR zůstane v cíli stejný jako.
- Zákazníci zkontrolovali a rozuměli smlouva SLA (SLA) cílové architektury (k). 
- Z důvodu rozdílů mezi komerčními podmínkami mezi HLIs a virtuálními počítači by zákazníci měli monitorovat využití svých virtuálních počítačů za účelem správy nákladů.
- Zákazníci rozumí a potvrzují, že HLI je vyhrazenou výpočetní platformou. Virtuální počítače se ale spouštějí na sdílené infrastruktuře, která je zabezpečená a izolovaná od ostatních tenantů.
- Zákazníci ověřili, že cílové virtuální počítače podporují vaši zamýšlenou architekturu. Pokud chcete zobrazit všechny podporované SKU virtuálních počítačů certifikovaných pro nasazení SAP HANA, zkontrolujte prosím [SAP HANA adresář hardwaru](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Zákazníci ověřili plán návrhu a migrace.
- Naplánujte uzel zotavení po havárii spolu s primární lokalitou.  Zákazníci nebudou moci používat uzel HLI DR pro primární lokalitu spuštěnou na virtuálních počítačích po migraci.
- Zákazníci zkopírovali požadované záložní soubory do cílových virtuálních počítačů na základě možností obnovy podnikových požadavků a dodržování předpisů. To se věnuje tomu, že se v průběhu přechodného období vyžaduje obnovení k určitému bodu v čase.
- Pro HSR HA musí zákazníci pro [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) a [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)nastavit a nakonfigurovat zařízení STONITH na SAP HANA.  Není předem nakonfigurovaný jako HLI případ.
- Tento přístup k migraci nepokrývá skladové položky HLI s konfigurací Optane.

## <a name="deployment-scenarios"></a>Scénáře nasazení
Společné modely nasazení s HLI zákazníky jsou shrnuté v následující tabulce.  Migrace na virtuální počítače Azure pro všechny scénáře HLI je možná.  Několik scénářů může vyžadovat menší úpravu architektury, aby se získaly výhody aktuální nabídky služeb Azure.

| ID scénáře | Scénář HLI | Chcete migrovat na virtuální počítač v doslovném znění? | Přeznačit |
| --- | --- | --- | --- |
| 1 | [Jeden uzel s jedním identifikátorem SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Ano | - |
| 2 | [Jeden uzel s MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Ano | - |
| 3 | [Jeden uzel se systémem DR pomocí replikace úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Ne | Replikace úložiště není pro virtuální platformu Azure k dispozici, změňte aktuální řešení zotavení po havárii na HSR nebo zálohování nebo obnovení. |
| 4 | [Jeden uzel se systémem DR (víceúčelový) pomocí replikace úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Ne | Replikace úložiště není pro virtuální platformu Azure k dispozici, změňte aktuální řešení zotavení po havárii na HSR nebo zálohování nebo obnovení. |
| 5 | [HSR s STONITH pro vysokou dostupnost](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Ano | Pro cílové virtuální počítače není k dispozici předkonfigurovaná SBD.  Vyberte a nasaďte řešení STONITH.  Možné možnosti: Agent služby Azure na úrovni služby (podporuje se pro [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA s HSRem, DR s replikací úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Ne | Nahraďte replikaci úložiště pro potřeby zotavení po havárii buď pomocí HSR, nebo zálohování nebo obnovení. |
| 7 | [Automatické převzetí služeb při selhání hostitele (1 + 1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Ano | Použití ANF pro sdílené úložiště s virtuálními počítači Azure |
| 8 | [Horizontální navýšení kapacity pomocí úsporného režimu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Ano | ČERNOBÍLé a 4HANA s M128s, M416s, M416ms virtuálními počítači pomocí ANF jenom pro úložiště |
| 9 | [Horizontální navýšení kapacity bez úsporného režimu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Ano | ČERNOBÍLé/4HANA s M128s, M416s, M416ms virtuálními počítači (s využitím nebo bez použití ANF pro úložiště) |
| 10 | [Horizontální navýšení kapacity pomocí replikace úložiště v systému DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Ne | Nahraďte replikaci úložiště pro potřeby zotavení po havárii buď pomocí HSR, nebo zálohování nebo obnovení. |
| 11 | [Jeden uzel s DR pomocí HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Ano | - |
| 12 | [HSR jednoho uzlu do DR (náklady optimalizované)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Ano | - |
| 13 | [HA a DR s HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Ano | - |
| 14 | [HA a DR s HSR (náklady optimalizované)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Ano | - |
| 15 | [Horizontální navýšení kapacity pomocí HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Ano | ČERNOBÍLé/4HANA s M128s. Virtuální počítače s M416s, M416ms (s nebo bez použití ANF pro úložiště) |


## <a name="source-hli-planning"></a>Zdroj (HLI) plánování
Při připojování serveru HLI se v rámci plánování výpočetních prostředků, sítí, úložišť a nastavení specifických pro spuštění databáze SAP HANA provedla jak Správa služeb Microsoftu, tak i zákazníci.  Pro migraci na virtuální počítač Azure je třeba provést podobné plánování.

### <a name="sap-hana-housekeeping"></a>SAP HANA údržbu 
Před migrací databáze HANA je dobrým zvykem uklizený obsah databáze, aby neproběhla migrace nevyžádaných, zastaralých dat nebo zastaralých protokolů do nové databáze.  Údržbu obvykle zahrnuje odstranění nebo archivaci starých, vypršených nebo neaktivních dat.  Proto by tyto akce "hygienická data" měly být testovány v neprodukčních systémech, aby bylo možné před využitím produkčního prostředí ověřit platnost dat jejich oříznutí.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Povolení síťového připojení pro nové virtuální počítače a virtuální sítě 
V nasazení HLI zákazníka se připojení k síti z Azure virtuální sítě zavedlo na základě informací popsaných v článku [architektura sítě SAP Hana (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Směrování provozu sítě se taky provádí způsobem popsaným v části "směrování v Azure".
1. Pro účely nastavení nového virtuálního počítače jako cíle HSR pro migraci platí, že pokud je nový virtuální počítač Azure umístěný ve stávající virtuální síti s rozsahy IP adres, které už mají oprávnění pro připojení k uzlu HLI, není nutná žádná další aktualizace připojení HLI.
2. Pokud je nový virtuální počítač Azure umístěný v nové virtuální síti (může být v jiné oblasti) a má partnerský vztah se stávající virtuální sítí, dá se pro přístup k tomuto novému rozsahu IP adres virtuální sítě použít klíč služby ExpressRoute a ID prostředku z původního HLI zřízení.  Zajistěte koordinaci se správou služeb Microsoftu, aby se virtuální síť mohla HLI připojit.  Poznámka: Chcete-li minimalizovat latenci sítě mezi aplikační a databázovou vrstvou, musí být aplikace i vrstva databáze ve stejné virtuální síti.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>Existující skupina dostupnosti, Zóny dostupnosti a umístění blízkosti vrstvy aplikace
Aktuální model nasazení se provádí za účelem splnění určitých cílů na úrovni služby.  V tomto přesunu zajistěte, aby cílová infrastruktura splňovala nebo překročila nastavené cíle.  
Zákazníci, kteří používají aplikační servery SAP, se budou nacházet do skupiny dostupnosti.  Pokud je aktuální úroveň služby nasazení uspokojivá a 
- Pokud je nahrazení SAP HANAho serveru provedeno prostřednictvím překladu názvů tím, že si vyměňujete IP adresy pro název hostitele databáze, nic dalšího nemusíte dělat.  
- Pokud nepoužíváte PPG, Nezapomeňte umístit všechny aplikační a databázové servery do stejné zóny, abyste minimalizovali latenci sítě.
- Pokud používáte PPG, přečtěte si část plánování cíle dokumentu, nastavení dostupnosti, Zóny dostupnosti a skupina umístění blízkosti (PPG).

### <a name="storage-replication-discontinuance-process-if-used"></a>Proces zastavení replikace úložiště (Pokud se používá)
Pokud se používá replikace úložiště jako řešení zotavení po havárii, bude nutné replikaci ukončit (neplánované) po vypnutí aplikace SAP a poslední SAP HANA katalogu, souboru protokolu, zálohy dat byly replikovány do svazků vzdáleného úložiště.  Tato akce je preventivní cílemá, že máme aktuální databázi na DR HLI pro případ, že dojde k havárii během přechodu z fyzického na virtuální počítač Azure.

### <a name="data-backups-preservation-consideration"></a>Aspekty zachování záloh dat
Až přímou migraci SAP HANA na virtuálním počítači Azure, všechna data založená na snímku nebo zálohování protokolů na HLI nebudou v případě potřeby snadno dostupná nebo obnovitelné k virtuálnímu počítači. Pro dobu předčasného přechodu, před tím, než zálohování založené na Azure vytvoří dostatek historie pro splnění požadavků na obnovení k určitému bodu v čase, doporučujeme, abyste u snímků na HLI, dnů/týdnů před přímou migraci zabrali zálohy na úrovni souborů.  Tyto zálohy se zkopírují do účtu Azure Storage přístupového k novému virtuálnímu počítači SAP HANA. Kromě zálohování obsahu HLI je také obezřetný postup, který bude mít k dispozici úplné zálohy SAP na šířku pro případ, že je potřeba vrácení zpět.

### <a name="adjusting-system-monitoring"></a>Úprava monitorování systému 
Zákazníci používají mnoho různých nástrojů pro monitorování a posílání oznámení o výstrahách pro systémy v rámci své technologie SAP na šířku.  Tato položka je pouze jednoduchým voláním pro příslušnou akci při přidávání nových virtuálních počítačů, které obsahují jakékoli úpravy pro monitorování a aktualizují příjemce oznámení výstrah v případě potřeby.

### <a name="microsoft-operations-team-involvement"></a>Zapojení do Microsoft Operations Team 
Otevřete lístek z Azure Portal báze na existující instanci HLI.  Po vytvoření lístku podpory vás pracovník podpory vás bude kontaktovat e-mailem.  

### <a name="engage-microsoft-account-team"></a>Zapojení týmu účtů Microsoft
Plánování migrace se blíží k době obnovení výročí smlouvy HLI, abyste minimalizovali zbytečné náklady na výpočetní prostředky. Pokud chcete vyřadit z provozu okno HLI, je potřeba koordinovat ukončení smluv a vlastní vypínání jednotky.

## <a name="destination-planning"></a>Plánování cíle
Zamyslete se nad novou infrastrukturou, která převezme místo existující infrastruktury, aby se nové přidání vešlo do velkého schématu věcí.  Níže jsou uvedeny některé klíčové body pro contemplation.

### <a name="resource-availability-in-the-target-region"></a>Dostupnost prostředků v cílové oblasti 
Aktuální oblast nasazení aplikačního serveru SAP je typicky v blízkosti s přidruženou HLIs.  HLIs se ale nabízí v méně umístěních než dostupné oblasti Azure.  Při migraci z fyzického HLIu na virtuální počítač Azure představuje možnost "vyladění" vzdálenosti blízkosti všech souvisejících služeb pro optimalizaci výkonu.  V takovém případě klíčovým aspektem by se zajistilo, že zvolená oblast má prostředky, které mají zájem.  Třeba dostupnost určité rodiny virtuálních počítačů nebo nabídky zón Azure pro zajištění vysoké dostupnosti.

### <a name="virtual-network"></a>Virtuální síť 
Architekt infrastruktury se zakládá na tom, jestli se má nová databáze HANA spustit v existující virtuální síti aplikace SAP nebo vytvořit novou.  Primárním rozhodujícím faktorem je aktuální rozložení sítě pro SAP na šířku.  V případě, že se bere v úvahu změna infrastruktury nasazení, například přechod z jednoho pásma do nasazení se dvěma zónami a využití PPG. Toto rozšíření přináší změny architektury. Další informace najdete v článku [Azure PPG pro optimální latenci sítě pomocí aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Zabezpečení
Bez ohledu na to, jestli se nový SAP HANA virtuální počítač vystavuje na nové nebo existující virtuální síti nebo podsíti, představuje novou podnikovou službu, která vyžaduje zabezpečení.  Správné řízení přístupu, které vyhovuje zásadám zabezpečení informací společnosti pro tuto novou třídu služby, by se mělo vyhodnotit a nasadit. 

### <a name="vm-sizing-recommendation"></a>Doporučení pro změny velikosti virtuálního počítače
Tato migrace je také příležitostí ke správné velikosti výpočetního modulu HANA.  Jedna z nich může využívat [Systémová zobrazení](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) Hana ve spojení s Hana Studio a pochopit spotřebu systémových prostředků, což umožňuje správnou změnu velikosti pro zajištění efektivity útraty.

### <a name="storage"></a>Úložiště 
Výkon úložiště je jedním z faktorů, které mají vliv na uživatelské prostředí aplikace SAP.  Základem pro danou SKLADOVOU položku virtuálního počítače, existuje minimální doporučení pro rozložení úložiště, které je publikované [SAP HANA konfigurací úložiště virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Doporučujeme, abyste si prostudovali tyto minimální specifikace a porovnali si stávající HLI systémové statistiky, abyste zajistili adekvátní vstupně-výstupní kapacity a výkon pro nový virtuální počítač HANA.

Pokud PPG nakonfigurujete pro nový virtuální počítač HANA a jeho přidružené severy, odešlete lístek podpory pro kontrolu a zajištění společného umísťování úložiště a virtuálního počítače HANA.  
Vzhledem k tomu, že vaše řešení zálohování může být potřeba změnit, měli byste také znovu navštívit náklady na úložiště, aby nedocházelo k provozním útratám překvapením. 

### <a name="storage-replication-for-disaster-recovery"></a>Replikace úložiště pro zotavení po havárii
V HLI byla replikace úložiště nabízená jako výchozí možnost pro replikaci zotavení po havárii pro databázi HANA. Tato funkce není pro virtuální počítač Azure výchozí možností. Zvažte HSR, zálohování a obnovení nebo jiná podporovaná řešení vyhovující vašim obchodním potřebám.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Skupiny dostupnosti, Zóny dostupnosti a umístění blízkosti 
V úvahu minimalizace vzdálenosti mezi aplikační vrstvou a SAP HANA za účelem zachování minimální latence sítě je potřeba, aby se nový virtuální počítač databáze a aktuální aplikační servery SAP nastavily do skupiny umístění blízkosti (PPG). Informace o tom, jak sada dostupnosti Azure a Zóny dostupnosti fungují s PPG pro nasazení SAP, najdete v tématu [Skupina umístění blízkosti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) .
Pokud jsou členové cílového systému HANA nasazeni ve více než jedné zóně Azure, musí mít zákazníci jasný pohled na profil latence zvolených zón. Umístění systémových komponent SAP je optimální vzhledem k blízkosti vzdálenosti mezi aplikací SAP a databází.  [Testovací nástroj pro latenci zóny dostupnosti](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) veřejné domény pomáhá zjednodušit měření.  


### <a name="backup-strategy"></a>Strategie zálohování
Mnoho zákazníků už používá řešení zálohování třetích stran pro SAP HANA v HLI.  V takovém případě je nutné nakonfigurovat pouze další chráněné databáze virtuálních počítačů a HANA.  Probíhající úlohy zálohování HLI pro teď nebudou naplánované, pokud se počítač po migraci vyřadí z provozu.
Zálohování Azure pro SAP HANA na virtuálním počítači je teď všeobecně dostupné.  Podrobnější informace najdete v těchto odkazech: [zálohování](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [obnovení](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), [Správa](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) SAP HANA zálohování ve virtuálních počítačích Azure.

### <a name="dr-strategy"></a>Strategie zotavení po havárii
Pokud vaše cíle na úrovni služby přinese delší dobu obnovení jednoduchého zálohování do úložiště objektů BLOB a obnovení na místě nebo do nového virtuálního počítače, jedná se o nejjednodušší a minimální nákladný strategii zotavení po havárii.  
Podobně jako u velkých instancí platforem, kde se HANA DR obvykle provádí s HSR; Na virtuálním počítači Azure je HSR i nejpřirozenější a nativní řešení zotavení po havárii SAP HANA.  Bez ohledu na to, jestli je zdrojové nasazení jediná instance, cluster s nebo bez automatického převzetí služeb při selhání nebo s více uzly, se v oblasti DR vyžaduje cílová HSR replika zdrojové infrastruktury. Tato replika DR (HSR Target) se vytvoří po dokončení migrace primárního HLI do virtuálního počítače.  Instance DR HANA se bude registrovat na primární SAP HANA na instanci virtuálního počítače jako lokalitu sekundární replikace.  

### <a name="sap-application-server-connectivity-destination-change"></a>Změna cíle připojení aplikačního serveru SAP
Přístup k migraci HSR má za následek nový hostitel databáze HANA, a proto nový název hostitele databáze pro aplikační vrstvu, profily SAP musí být upraveny tak, aby odrážely nový název hostitele.  Pokud je přepínač proveden překladem názvů, který zachovává název hostitele, není nutná žádná změna profilu.

### <a name="operating-system"></a>Operační systém
Image operačních systémů pro HLI a virtuální počítače, i když jsou na stejné úrovni verze, SLES 11 SP4 například nejsou stejné. Zákazníci musí ověřit požadované balíčky, opravy hotfix, opravy, jádro a opravy zabezpečení na HLI, aby se mohly nainstalovat do operačního systému cílového virtuálního počítače.  Kromě toho je běžné, že zákazníci nakonfigurují novější verzi operačního systému na cílovém virtuálním počítači pro SAP HSR.  To je podporováno pro [SAP note 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nová žádost o licenci SAP
Jednoduché volání pro vyžádání nové licence SAP pro nový systém HANA, když je teď migrace na virtuální počítače.

### <a name="service-level-agreement-sla-differences"></a>Rozdíly smlouvy o úrovni služeb (SLA)
Autoři rádi volali rozdíl mezi smlouvou SLA o dostupnosti mezi HLI a virtuálním počítačem Azure.  Například clusterované páry HA HLIs nabízejí 99,99% dostupnost. Aby bylo možné dosáhnout stejné smlouvy SLA, musí jedna z nich nasazovat virtuální počítače v zónách dostupnosti. Tento [článek](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) popisuje dostupnost s přidruženými architekturami nasazení, aby si zákazníci mohli odpovídajícím způsobem naplánovat příslušnou cílovou infrastrukturu.


## <a name="migration-strategy"></a>Strategie migrace
V tomto dokumentu se zabýváme pouze přístupem k replikaci systému HANA pro migraci z HLI do virtuálního počítače Azure.  Závisí na nasazení cílového řešení úložiště, proces se mírně liší. Kroky vysoké úrovně jsou popsány níže.

### <a name="vm-with-premiumultra-disks-for-data"></a>Virtuální počítač s Premium/extrémně-disky pro data
U virtuálních počítačů, které jsou nasazené s využitím úrovně Premium nebo Ultra-disks, platí standardní SAP HANA konfigurace replikace systému a dá se použít k instalaci HSR.  Odkaz na [článek o nápovědě SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) poskytuje přehled kroků, které jsou součástí nastavení replikace systému mezi dvěma systémy, přebírání sekundárního systému, navrácení služeb po obnovení do primárního systému a zakázání systémové replikace.  Pro účely migrace budete potřebovat instalaci, převzetí služeb při selhání a zakazování replikace systému v HLI zdrojových krocích.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Virtuální počítač s ANF pro datové a protokolové svazky
Na vysoké úrovni je potřeba zkopírovat nejnovější snímky úložiště HLI a svazky protokolů do Azure Storage, kde jsou dostupné a obnovitelné pro cílový virtuální počítač HANA.  Proces kopírování můžete provést pomocí všech nativních nástrojů pro kopírování pro Linux.  

>[!Important]
> Kopírování a přenos dat může trvat hodiny, záleží na velikosti databáze HANA a šířce pásma sítě.  Hromadnou kopii procesu kopírování je třeba provést předem při primárním výpadku databáze HANA.

### <a name="mcos-to-mdc-conversion"></a>Převod MCOS na MDC
Některé z našich zákazníků HLI si zvolili několik MCOS modelů nasazení v jednom systému.  Motivace měla obejít omezení snímku úložiště s více databázemi (MDC) starších verzí SAP HANA.  V modelu MCOS jsou v jednom okně HLI zavrstveny různé nezávisle SAP HANA instance.  Použití HSR pro migraci by mohlo fungovat na více virtuálních počítačích HANA s jednou databází klienta.  Tento konečný stav vychází z vytíženější na šířku, než kolik jich zákazník mohl být zvyklí.  SAP HANA s MDC 2,0 výchozím nasazením, který je v, je možné po migraci HSR provést [Přesun tenanta Hana](https://launchpad.support.sap.com/#/notes/2472478) .  Tento proces konsoliduje tyto nezávislé databáze HANA do tenantů v jednom kontejneru HANA.  

### <a name="application-layer-consideration"></a>Zvážení aplikační vrstvy
DATABÁZOVÝ server je zobrazený uprostřed systému SAP.  Všechny aplikační servery by se měly nacházet poblíž SAP HANA DB.  V některých případech, když je žádoucí nové použití PPG, přemístění stávajících aplikačních serverů na PPG, kde se může vyžadovat virtuální počítač HANA.  Vytváření nových aplikačních serverů může být jednodušší, pokud již máte šablony nasazení užitečné.  
Pokud jsou existující aplikační servery a nový virtuální počítač HANA v optimálním umístění, nemusíte vytvářet žádné nové aplikační servery, pokud není potřeba další kapacita.  
Pokud je nová infrastruktura navržená tak, aby vylepšila dostupnost služby, může se stát, že existující aplikační servery budou zbytečné a měly by být vypnuté a odstraněné.
Pokud se cílový název hostitele virtuálního počítače změnil a liší se od názvu hostitele HLI, musí se profily aplikačního serveru SAP upravit tak, aby odkazovaly na nového hostitele.  Pokud se změnila jenom IP adresa databáze HANA, je potřeba aktualizovat záznam DNS pro potenciální příchozí připojení k novému virtuálnímu počítači HANA.

### <a name="acceptance-test"></a>Akceptační test
I když migrace z HLI do virtuálního počítače neprovede žádné podstatné změny v obsahu databáze v porovnání s heterogenní migrací, pořád doporučujeme ověřovat funkce klíčů a aspekt výkonu nového nastavení.  

### <a name="cutover-plan"></a>Plán přímou migraci
I když je tato migrace přímo dopředná, ale zahrnuje vyřazení existující databáze z provozu.  Pečlivé plánování zachování zdrojového systému pomocí přidruženého obsahu a záložních imagí je důležité v případě, že je potřeba použití náhradní zálohy.  Dobré plánování nabízí nejrychlejší reverzní zpětný chod.   


## <a name="post-migration"></a>Po migraci
Úloha migrace se neprovádí, dokud jsme nemuseli bezpečně oddělit jakékoli služby závislé na HLI nebo připojení, abyste zajistili zachování integrity dat.  Také vypněte zbytečné služby.  Tato část volá několik nejdůležitějších položek.

### <a name="decommissioning-the-hli"></a>Vyřazení z provozu HLI
Po úspěšné migraci databáze HANA na virtuální počítač Azure by se v HLI DB neměly provádět žádné provozní obchodní transakce.  Nicméně udržování HLI a provozu po dobu, která je stejná jako místní okno uchovávání zálohovaných dat, představuje bezpečný postup, který v případě potřeby zajišťuje nejrychlejší obnovení dat.  Pak by okno HLI mělo být vyřazené z provozu.  Kromě technického podniku by zákazníci v jejich nejlepším zájmu uzavřeli závazky HLI s Microsoftem.  Zákazníci by měli kontaktovat své zástupce Microsoftu, aby mohli pracovat prostřednictvím procesu vyřazení HLI z provozu.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Odebrat všechny proxy (např. softwaru iptables, Big-IP) nakonfigurovaných pro HLI 
Pokud se služba proxy serveru, jako je softwaru iptables, používá ke směrování místního provozu do a z HLI, tato služba už po úspěšné migraci do virtuálního počítače není potřeba.  Tato služba připojení by se ale měla uchovávat, dokud je okno HLI pořád na stejném umístění, jak je popsáno v tématu vyřazení z provozu.  Tato služba se dá vypnout, až se okno HLI úplně vyřadí z provozu. 

### <a name="remove-global-reach-for-hli"></a>Odebrat Global Reach pro HLI 
Global Reach se obvykle používá k propojení brány ExpressRoute zákazníka s bránou ExpressRoute HLI, která umožňuje, aby místní provoz zákazníka dosáhl klienta HLI, aniž by musel být proxy služba.  Podobně jako u služby proxy serveru softwaru iptables by měl být GlobalReach uložený, dokud se okno HLI plně nevyřadí.

### <a name="operating-system-subscription--movereuse"></a>Předplatné operačního systému – přesunout a znovu použít
Vzhledem k tomu, že jsou servery virtuálních počítačů stood nahoru a že jsou vyřazení oken HLI, můžete nahradit nebo znovu použít odběry operačního systému, abyste se vyhnuli dvojímu placení licencí na operační systém.



## <a name="next-steps"></a>Další kroky
Podívejte se na tyto články:
- [SAP HANA konfigurací infrastruktury a operací v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [Úlohy SAP v Azure: kontrolní seznam pro plánování a nasazení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
