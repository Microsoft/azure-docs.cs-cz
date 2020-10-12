---
title: Migrace SAP HANA v Azure (velké instance) na virtuální počítače Azure | Microsoft Docs
description: Postup migrace SAP HANA v Azure (velké instance) na virtuální počítače Azure
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: db51ec682f43366f5637c461e3fe4037dec8e364
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87085210"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA migrace velkých instancí Azure do Azure Virtual Machines
Tento článek popisuje možné scénáře nasazení rozsáhlých instancí Azure a nabízí plánovací a migrační přístup s minimalizovanými výpadky přechodu.

## <a name="overview"></a>Přehled
Vzhledem k tomu, že oznámení o velkých instancích Azure pro SAP HANA (HLI) v září 2016, tento hardware přijal mnoho zákazníků jako nabídku služeb pro výpočetní platformu v paměti.  V posledních letech překročila rozšíření velikosti virtuálního počítače Azure, které se podporuje nasazení na více instancí HANA, větší nároky na kapacitu databáze ERP pro podnikové zákazníky.  Začneme, abychom zákazníkům vyjádřili zájem o migraci SAP HANA úloh z fyzických serverů na virtuální počítače Azure.
Tato příručka není podrobného dokumentu konfigurace. Popisuje běžné modely nasazení a nabízí Rady pro plánování a migraci.  Záměrem je volat nezbytné požadavky na přípravu pro minimalizaci výpadků přechodu.

## <a name="assumptions"></a>Předpoklady
Tento článek přináší následující předpoklady:
- Jediným dotčeným zájmem je homogenní migrace databázové služby HANA z databáze Hana (HLI) na virtuální počítač Azure bez významného upgradu softwaru nebo opravy. Tyto dílčí aktualizace zahrnují použití novější verze operačního systému nebo verze HANA výslovně uvedené jako podporované v příslušných poznámkách SAP. 
- Všechny aktivity aktualizací a upgradů je třeba provést před migrací nebo po ní.  Například SAP HANA MCOS převádění na nasazení MDC. 
- Postup migrace, který by poskytoval minimální prostoje, je SAP HANA systémové replikaci. Jiné metody migrace nejsou součástí rozsahu tohoto dokumentu.
- Tyto pokyny platí pro Rev3 i rev4 SKU HLI.
- Architektura nasazení HANA zůstává během migrace primárně nezměněná.  To znamená, že systém s jedinou instancí nástroje DR zůstane v cíli stejný jako.
- Zákazníci zkontrolovali a rozuměli smlouva SLA (SLA) cílové architektury (k). 
- Obchodní výrazy mezi HLIs a virtuálními počítači se liší. Zákazníci by měli sledovat využití svých virtuálních počítačů pro správu nákladů.
- Zákazníci rozumí, že HLI je vyhrazená výpočetní platforma, zatímco virtuální počítače běží na sdílené i izolované infrastruktuře.
- Zákazníci ověřili, že cílové virtuální počítače podporují vaši zamýšlenou architekturu. Pokud chcete zobrazit všechny podporované SKU virtuálních počítačů, které jsou certifikované pro nasazení SAP HANA, přečtěte si článek [SAP HANA adresář hardwaru](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Zákazníci ověřili plán návrhu a migrace.
- Naplánujte virtuální počítač pro zotavení po havárii spolu s primární lokalitou.  Zákazníci nemůžou používat HLI jako uzel DR pro primární lokalitu spuštěnou na virtuálních počítačích po migraci.
- Zákazníci zkopírovali požadované záložní soubory do cílových virtuálních počítačů na základě možností obnovy podnikových požadavků a dodržování předpisů. S dostupnými zálohami virtuálních počítačů umožňuje obnovení k určitému bodu v čase během přechodného období.
- Pro HSR HA musí zákazníci pro [SLES](./high-availability-guide-suse-pacemaker.md) a [RHEL](./high-availability-guide-rhel-pacemaker.md)nastavit a nakonfigurovat zařízení STONITH na SAP HANA.  Není předem nakonfigurovaný jako HLI případ.
- Tento přístup k migraci nepokrývá skladové položky HLI s konfigurací Optane.

## <a name="deployment-scenarios"></a>Scénáře nasazení
Společné modely nasazení s HLI zákazníky jsou shrnuté v následující tabulce.  Migrace na virtuální počítače Azure pro všechny scénáře HLI je možná.  Aby bylo možné využívat doplňkové služby Azure, mohou být vyžadovány drobné změny architektury.

| ID scénáře | Scénář HLI | Chcete migrovat na virtuální počítač v doslovném znění? | Přeznačit |
| --- | --- | --- | --- |
| 1 | [Jeden uzel s jedním identifikátorem SID](./hana-supported-scenario.md#single-node-with-one-sid) | Yes | - |
| 2 | [Jeden uzel s MCOS](./hana-supported-scenario.md#single-node-mcos) | Yes | - |
| 3 | [Jeden uzel se systémem DR pomocí replikace úložiště](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | No | Replikace úložiště není pro virtuální platformu Azure k dispozici, změňte aktuální řešení zotavení po havárii na HSR nebo zálohování nebo obnovení. |
| 4 | [Jeden uzel se systémem DR (víceúčelový) pomocí replikace úložiště](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | No | Replikace úložiště není pro virtuální platformu Azure k dispozici, změňte aktuální řešení zotavení po havárii na HSR nebo zálohování nebo obnovení. |
| 5 | [HSR s STONITH pro vysokou dostupnost](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Yes | Pro cílové virtuální počítače není k dispozici předkonfigurovaná SBD.  Vyberte a nasaďte řešení STONITH.  Možné možnosti: Agent služby Azure na úrovni služby (podporuje se pro [RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES](./high-availability-guide-suse-pacemaker.md)), SBD |
| 6 | [HA s HSRem, DR s replikací úložiště](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | No | Nahraďte replikaci úložiště pro potřeby zotavení po havárii buď pomocí HSR, nebo zálohování nebo obnovení. |
| 7 | [Automatické převzetí služeb při selhání hostitele (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11) | Yes | Použití ANF pro sdílené úložiště s virtuálními počítači Azure |
| 8 | [Horizontální navýšení kapacity pomocí úsporného režimu](./hana-supported-scenario.md#scale-out-with-standby) | Yes | ČERNOBÍLé a 4HANA s M128s, M416s, M416ms virtuálními počítači pomocí ANF jenom pro úložiště |
| 9 | [Horizontální navýšení kapacity bez úsporného režimu](./hana-supported-scenario.md#scale-out-without-standby) | Yes | ČERNOBÍLé/4HANA s M128s, M416s, M416ms virtuálními počítači (s využitím nebo bez použití ANF pro úložiště) |
| 10 | [Horizontální navýšení kapacity pomocí replikace úložiště v systému DR](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | No | Nahraďte replikaci úložiště pro potřeby zotavení po havárii buď pomocí HSR, nebo zálohování nebo obnovení. |
| 11 | [Jeden uzel s DR pomocí HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Yes | - |
| 12 | [HSR jednoho uzlu do DR (náklady optimalizované)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Yes | - |
| 13 | [HA a DR s HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Yes | - |
| 14 | [HA a DR s HSR (náklady optimalizované)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Yes | - |
| 15 | [Horizontální navýšení kapacity pomocí HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Yes | ČERNOBÍLé/4HANA s M128s. Virtuální počítače s M416s, M416ms (s nebo bez použití ANF pro úložiště) |


## <a name="source-hli-planning"></a>Zdroj (HLI) plánování
Při připojování serveru HLI se v rámci plánování výpočetních prostředků, sítí, úložišť a nastavení specifických pro spuštění databáze SAP HANA provedla jak Správa služeb Microsoftu, tak i zákazníci.  Pro migraci na virtuální počítač Azure je třeba provést podobné plánování.

### <a name="sap-hana-housekeeping"></a>SAP HANA údržbu 
Je dobrým provozním postupem, jak uklizený obsah databáze, takže se nemigrují, zastaralá data nebo zastaralá protokolují do nové databáze.  Údržbu obvykle zahrnuje odstranění nebo archivaci starých, neplatných nebo neaktivních dat.  Tyto akce "hygieny dat" by měly být testovány v neprodukčních systémech, aby bylo možné před využitím produkčního prostředí ověřit platnost jejich dat.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Povolení síťového připojení pro nové virtuální počítače a virtuální sítě 
V nasazení HLI zákazníka se síť nastavila na základě informací popsaných v článku [architektura sítě SAP Hana (velké instance)](./hana-network-architecture.md). Směrování provozu sítě se taky provádí způsobem popsaným v části "směrování v Azure".
- Pokud se v části nastavení nového virtuálního počítače jako cíle migrace nachází ve stávající virtuální síti s rozsahy IP adres, které už mají povolený přístup k HLI, nevyžaduje se žádná další aktualizace připojení.
- Pokud je nový virtuální počítač Azure umístěný v novém Microsoft Azure Virtual Network, může být v jiné oblasti a má partnerský vztah se stávající virtuální sítí. k povolení přístupu pro tento nový rozsah IP adres virtuální sítě se dá použít klíč služby ExpressRoute a ID prostředku z původního HLI zřizování.  Zajistěte koordinaci se správou služeb Microsoftu, aby se virtuální síť mohla HLI připojit.  Poznámka: aby se minimalizovala latence sítě mezi aplikační a databázovou vrstvou, musí být vrstva aplikace i databáze ve stejné virtuální síti.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Existující sada dostupnosti vrstvy aplikace, Zóny dostupnosti a skupina umístění blízkosti (PPG)
Aktuální model nasazení se provádí za účelem splnění určitých cílů na úrovni služby.  V tomto přesunu zajistěte, aby cílová infrastruktura splňovala nebo překročila nastavené cíle.  
Zákazníci, kteří používají aplikační servery SAP, se budou nacházet do skupiny dostupnosti.  Pokud je aktuální úroveň služby nasazení uspokojivá a 
- Pokud cílový virtuální počítač předpokládá název hostitele HLI logického názvu, může aktualizace překladu adres služby DNS (Domain Name Service) odkazující na IP adresu virtuálního počítače fungovat bez aktualizace profilů SAP.
- Pokud nepoužíváte PPG, Nezapomeňte umístit všechny aplikační a databázové servery do stejné zóny, abyste minimalizovali latenci sítě.
- Pokud používáte PPG, přečtěte si část tohoto dokumentu: "cílové plánování, sada dostupnosti, Zóny dostupnosti a skupina umístění blízkosti (PPG)".

### <a name="storage-replication-discontinuance-process-if-used"></a>Proces zastavení replikace úložiště (Pokud se používá)
Pokud se jako řešení zotavení po havárii používá replikace úložiště, po vypnutí aplikace SAP by se měla ukončit (Neplánovaná).  Kromě toho se replika posledního SAP HANA katalogu, souboru protokolu a zálohování dat do vzdálených svazků úložiště DR HLI.  Uděláte to jako preventivní opatření pro případ, že dojde k havárii během přechodu fyzického serveru na virtuální počítač Azure.

### <a name="data-backups-preservation-consideration"></a>Aspekty zachování záloh dat
Po převzetí SAP HANA na virtuálním počítači Azure nejsou všechna data založená na snímku nebo zálohování protokolů na HLI v případě potřeby snadno dostupná nebo obnovitelné na virtuální počítač. Předtím, než zálohování založené na Azure vytvoří v období předčasného přechodu dostatek historie pro splnění požadavků na obnovení k určitému bodu v čase, doporučujeme, abyste u snímků HLI, dnů nebo týdnů před vyjmutím vybrali zálohy na úrovni souborů.  Tyto zálohy se zkopírují do účtu Azure Storage přístupového k novému virtuálnímu počítači SAP HANA.
Kromě zálohování obsahu HLI je to pro případ, že je potřeba provést vrácení zpět, je vhodné mít k dispozici úplné zálohování SAP na šířku.

### <a name="adjusting-system-monitoring"></a>Úprava monitorování systému 
Zákazníci používají mnoho různých nástrojů pro monitorování a posílání oznámení o výstrahách pro systémy v rámci své technologie SAP na šířku.  Tato položka je pouze voláním pro příslušnou akci, která bude začlenit změny pro monitorování a aktualizovat příjemce oznámení výstrah v případě potřeby.

### <a name="microsoft-operations-team-involvement"></a>Zapojení do Microsoft Operations Team 
Otevřete lístek z Azure Portal na základě existující instance HLI.  Po vytvoření lístku podpory vás pracovník podpory vás bude kontaktovat e-mailem.  

### <a name="engage-microsoft-account-team"></a>Zapojení týmu účet Microsoft
Plánování migrace se blíží k době obnovení výročí smlouvy HLI, abyste minimalizovali zbytečné náklady na výpočetní prostředky. Pokud chcete vyřadit z provozu okno HLI, je potřeba koordinovat ukončení smluv a vlastní vypínání jednotky.

## <a name="destination-planning"></a>Plánování cíle
Zamyslete se nad novou infrastrukturou, která převezme místo existující infrastruktury, aby se nové přidání vešlo do velkého schématu věcí.  Níže jsou uvedeny některé klíčové body pro contemplation.

### <a name="resource-availability-in-the-target-region"></a>Dostupnost prostředků v cílové oblasti 
Aktuální oblast nasazení aplikačních serverů SAP je typicky v blízkosti přidruženého HLIsu.  HLIs se ale nabízí v méně umístěních než dostupné oblasti Azure.  Při migraci fyzického HLIu na virtuální počítač Azure je také vhodný čas k vyladění vzdálenosti blízkosti všech souvisejících služeb pro účely optimalizace výkonu.  V takovém případě je jedním z klíčových aspektů, aby zvolená oblast měla všechny požadované prostředky.  Například dostupnost určité rodiny virtuálních počítačů nebo nabídky zón Azure pro nastavení vysoké dostupnosti.

### <a name="virtual-network"></a>Virtuální síť 
Zákazníci si musí vybrat, jestli se má nová databáze HANA spustit ve stávající virtuální síti, nebo vytvořit novou.  Primárním rozhodujícím faktorem je aktuální rozložení sítě pro SAP na šířku.  I když infrastruktura přechází z jednoho pásma do nasazení se dvěma zónami a používá PPG, dojde ke změně architektury. Další informace najdete v článku [Azure PPG pro optimální latenci sítě pomocí aplikace SAP](./sap-proximity-placement-scenarios.md).   

### <a name="security"></a>Zabezpečení
Bez ohledu na to, jestli se nový SAP HANA virtuální počítač vystavuje na nové nebo existující virtuální síti nebo podsíti, představuje novou podnikovou službu, která vyžaduje zabezpečení.  Pro tuto novou třídu služby by se mělo vyhodnotit a nasazovat řízení přístupu v souladu se zásadami zabezpečení informací společnosti.

### <a name="vm-sizing-recommendation"></a>Doporučení pro změny velikosti virtuálního počítače
Tato migrace je také příležitostí ke správné velikosti výpočetního modulu HANA.  Jedna z nich může používat [Systémová zobrazení](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) Hana společně s Hana Studio k pochopení spotřeby systémových prostředků, což umožňuje správnou změnu velikosti pro zajištění efektivity útraty.

### <a name="storage"></a>Storage 
Výkon úložiště je jedním z faktorů, které mají vliv na uživatelské prostředí aplikace SAP.  Základem pro danou SKLADOVOU položku virtuálního počítače, existuje minimální rozložení úložiště publikované [SAP HANA konfigurací úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md). Doporučujeme, abyste si prostudovali tyto minimální specifikace a porovnali si stávající HLI systémovou statistiku, abyste zajistili adekvátní vstupně-výstupní kapacity a výkon pro nový virtuální počítač HANA.

Pokud PPG nakonfigurujete pro nový virtuální počítač HANA a jeho přidružené severy, odešlete lístek podpory pro kontrolu a zajištění společného umístění úložiště a virtuálního počítače. Vzhledem k tomu, že vaše řešení zálohování může být potřeba změnit, měli byste také znovu navštívit náklady na úložiště, aby nedocházelo k provozním útratám překvapením.

### <a name="storage-replication-for-disaster-recovery"></a>Replikace úložiště pro zotavení po havárii
S HLI byla replikace úložiště nabízená jako výchozí možnost pro zotavení po havárii. Tato funkce není výchozí možností pro SAP HANA na virtuálním počítači Azure. Zvažte HSR, zálohování a obnovení nebo jiná podporovaná řešení vyhovující vašim obchodním potřebám.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Skupiny dostupnosti, Zóny dostupnosti a umístění blízkosti 
Pro zkrácení vzdálenosti mezi aplikační vrstvou a SAP HANA, aby se zajistila minimální latence sítě, je potřeba, aby se nový virtuální počítač databáze a aktuální aplikační servery SAP umístily do PPG. Informace o tom, jak sada dostupnosti Azure a Zóny dostupnosti fungují s PPG pro nasazení SAP, najdete v tématu [Skupina umístění blízkosti](./sap-proximity-placement-scenarios.md) .
Pokud jsou členové cílového systému HANA nasazeni ve více než jedné zóně Azure, musí mít zákazníci jasný pohled na profil latence zvolených zón. Umístění systémových komponent SAP je optimální vzhledem k blízkosti vzdálenosti mezi aplikací SAP a databází.  [Testovací nástroj pro latenci zóny dostupnosti](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) veřejné domény pomáhá zjednodušit měření.  


### <a name="backup-strategy"></a>Strategie zálohování
Mnoho zákazníků už používá řešení zálohování třetích stran pro SAP HANA v HLI.  V takovém případě je nutné nakonfigurovat pouze další chráněné databáze virtuálních počítačů a HANA.  Probíhající úlohy zálohování HLI se teď můžou neplánovat, pokud se počítač po migraci vyřadí z provozu.
Azure Backup pro SAP HANA na virtuálním počítači je teď všeobecně dostupná.  Podrobnější informace najdete v těchto odkazech: [zálohování](../../../backup/backup-azure-sap-hana-database.md), [obnovení](../../../backup/sap-hana-db-restore.md), [Správa](../../../backup/sap-hana-db-manage.md) SAP HANA zálohování ve virtuálních počítačích Azure.

### <a name="dr-strategy"></a>Strategie zotavení po havárii
Pokud vaše cíle na úrovni služby přinese delší dobu obnovení, jednoduché zálohování do úložiště objektů BLOB a obnovení na místě nebo obnovení do nového virtuálního počítače je nejjednodušší a nejméně nákladná strategie zotavení po havárii.  
Podobně jako u velkých instancí platforem, kde se HANA DR obvykle provádí s HSR; Na virtuálním počítači Azure je HSR i nejpřirozenější a nativní řešení zotavení po havárii SAP HANA.  Bez ohledu na to, jestli je zdrojové nasazení na jednu instanci nebo clusterovaný, se v oblasti zotavení po havárii vyžaduje replika zdrojové infrastruktury.
Tato replika DR se nakonfiguruje po dokončení primární migrace HLI na virtuální počítač.  DATABÁZE DR HANA se bude registrovat na primární SAP HANA na instanci virtuálního počítače jako lokalitu sekundární replikace.  

### <a name="sap-application-server-connectivity-destination-change"></a>Změna cíle připojení aplikačního serveru SAP
Migrace HSR vede k novému hostiteli databáze HANA, a proto novým názvem hostitele databáze pro aplikační vrstvu, musí být profily SAP upraveny tak, aby odrážely nový název hostitele.  Pokud je přepínání provedeno pomocí překladu názvů, který zachovává název hostitele, není nutná žádná změna profilu.

### <a name="operating-system"></a>Operační systém
Bitové kopie operačního systému pro HLI a virtuální počítače, i když jsou na stejné úrovni vydaných verzí, SLES 12 SP4 například nejsou stejné. Pokud chtějí zákazníci nainstalovat stejné balíčky v cíli, musí ověřit požadované balíčky, opravy hotfix, opravy, jádro a opravy zabezpečení v HLI.  Pro replikaci ze staršího operačního systému na virtuální počítač s novější verzí operačního systému se podporuje použití HSR.  Konkrétní podporované verze ověřte v části [SAP note 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nová žádost o licenci SAP
Jednoduché volání pro vyžádání nové licence SAP pro nový systém HANA, když je teď migrace na virtuální počítače.

### <a name="service-level-agreement-sla-differences"></a>Rozdíly smlouvy o úrovni služeb (SLA)
Autoři rádi volali rozdíl mezi smlouvou SLA o dostupnosti mezi HLI a virtuálním počítačem Azure.  Například clusterované páry HA HLIs nabízejí 99,99% dostupnost. Aby bylo možné dosáhnout stejné smlouvy SLA, musí jedna z nich nasazovat virtuální počítače v zónách dostupnosti. Tento [článek](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) popisuje dostupnost s přidruženými architekturami nasazení, aby si zákazníci mohli odpovídajícím způsobem naplánovat jeho cílovou infrastrukturu.


## <a name="migration-strategy"></a>Strategie migrace
V tomto dokumentu se zabýváme pouze přístupem k replikaci systému HANA pro migraci z HLI do virtuálního počítače Azure.  Závisí na nasazení cílového řešení úložiště, proces se mírně liší. Kroky vysoké úrovně jsou popsány níže.

### <a name="vm-with-premiumultra-disks-for-data"></a>Virtuální počítač s Premium/extrémně-disky pro data
Pro virtuální počítače, které jsou nasazené s úrovní Premium nebo Ultra-disks, se pro nastavení HSR použije standardní konfigurace replikace systému SAP HANA.  [Článek o nápovědě SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) poskytuje přehled kroků, které jsou součástí nastavení replikace systému, přebírá sekundární systém, navrácení služeb po obnovení do primární sítě a zakázání systémové replikace.  Pro účely migrace budeme potřebovat nastavení, přebírat a zakazovat kroky replikace.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Virtuální počítač s ANF pro datové a protokolové svazky
Na vysoké úrovni je potřeba zkopírovat nejnovější snímky úložiště HLI a svazky protokolů do Azure Storage, kde jsou dostupné a obnovitelné pro cílový virtuální počítač HANA.  Proces kopírování můžete provést pomocí všech nativních nástrojů pro kopírování pro Linux.  

> [!IMPORTANT]
> Kopírování a přenos dat může trvat hodiny, záleží na velikosti databáze HANA a šířce pásma sítě.  Hromadnou kopii procesu kopírování je třeba provést předem při primárním výpadku databáze HANA.

### <a name="mcos-to-mdc-conversion"></a>Převod MCOS na MDC
Někteří naši zákazníci v HLI používali MCOS model nasazení více komponent v jednom systému.  Motivace měla obejít omezení snímku úložiště s více databázemi (MDC) starších verzí SAP HANA.  V modelu MCOS jsou v jednom okně HLI zavrstveny různé nezávisle SAP HANA instance.  Použití HSR pro migraci by fungovalo správně, ale výsledkem je více virtuálních počítačů HANA s jednou databází klienta.  Tento konečný stav vychází z vytíženější na šířku, než kolik jich zákazník mohl být zvyklí.  SAP HANA s MDC 2,0 výchozím nasazením, který je v, je možné po migraci HSR provést [Přesun tenanta Hana](https://launchpad.support.sap.com/#/notes/2472478) .  Tento proces konsoliduje tyto nezávislé databáze HANA do tenantů v jednom kontejneru HANA.  

### <a name="application-layer-consideration"></a>Zvážení aplikační vrstvy
DATABÁZOVÝ server je zobrazený uprostřed systému SAP.  Všechny aplikační servery by se měly nacházet poblíž SAP HANA DB.  V některých případech, když je žádoucí nové použití PPG, přemístění stávajících aplikačních serverů na PPG, kde se může vyžadovat virtuální počítač HANA.  Vytváření nových aplikačních serverů může být jednodušší, pokud již máte šablony nasazení užitečné.  
Pokud jsou existující aplikační servery a nový virtuální počítač HANA v optimálním umístění, nemusíte vytvářet žádné nové aplikační servery, pokud není potřeba další kapacita.  
Pokud je nová infrastruktura navržená tak, aby vylepšila dostupnost služby, může se stát, že existující aplikační servery budou zbytečné a měly by být vypnuté a odstraněné.
Pokud se cílový název hostitele virtuálního počítače změnil a liší se od názvu hostitele HLI, musí se profily aplikačního serveru SAP upravit tak, aby odkazovaly na nového hostitele.  Pokud se změnila jenom IP adresa databáze HANA, je potřeba aktualizovat záznam DNS pro potenciální příchozí připojení k novému virtuálnímu počítači HANA.

### <a name="acceptance-test"></a>Akceptační test
I když migrace z HLI do virtuálního počítače neprovede žádné podstatné změny v obsahu databáze v porovnání s heterogenní migrací, pořád doporučujeme ověřovat funkce klíčů a aspekt výkonu nového nastavení.  

### <a name="cutover-plan"></a>Plán přímou migraci
I když je tato migrace přímo dopředná, ale zahrnuje vyřazení existující databáze z provozu.  Pečlivé plánování zachování zdrojového systému pomocí přidruženého obsahu a záložních imagí je důležité v případě, že je potřeba použití náhradní zálohy.  Dobré plánování nabízí nejrychlejší reverzní zpětný chod.   


## <a name="post-migration"></a>Po dokončení migrace
Úloha migrace se neprovádí, dokud jsme nemuseli bezpečně oddělit jakékoli služby závislé na HLI nebo připojení, abyste zajistili zachování integrity dat.  Také vypněte zbytečné služby.  Tato část volá několik nejdůležitějších položek.

### <a name="decommissioning-the-hli"></a>Vyřazení z provozu HLI
Po úspěšné migraci databáze HANA do virtuálního počítače Azure se ujistěte, že ve službě HLI DB nejsou spuštěny žádné produktivní obchodní transakce.  Zachování HLI spuštění po určitou dobu se ale rovná místnímu oknu uchovávání záloh, a to i v případě potřeby k urychlení obnovení.  Pak by okno HLI mělo být vyřazené z provozu.  Zákazníci by měli smluvním zástupcům společnosti, aby uzavřeli své HLI závazky od Microsoftu.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Odebrat všechny proxy (např. softwaru iptables, Big-IP) nakonfigurovaných pro HLI 
Pokud se služba proxy serveru, jako je softwaru iptables, používá ke směrování místního provozu do a z HLI, už po úspěšné migraci do virtuálního počítače není potřeba.  Tato služba připojení by ale měla být udržována, dokud je okno HLI stále zachováno.  Po úplném vyřazení okna HLI je služba vypnula.

### <a name="remove-global-reach-for-hli"></a>Odebrat Global Reach pro HLI 
Global Reach slouží k propojení brány ExpressRoute zákazníkům s bránou ExpressRoute HLI.  Umožňuje zákazníkům místní provoz, aby se k tenantovi HLI dostal přímo bez použití proxy služby.  Toto připojení se už nepotřebuje při nepřítomnosti jednotky HLI po migraci.  Podobně jako u služby proxy serveru softwaru iptables by měl být GlobalReach také zachovaná, dokud se okno HLI plně nevyřadí.

### <a name="operating-system-subscription--movereuse"></a>Předplatné operačního systému – přesunout a znovu použít
Vzhledem k tomu, že jsou servery virtuálních počítačů stood nahoru a že jsou vyřazení oken HLI, můžete nahradit nebo znovu použít odběry operačního systému, abyste se vyhnuli dvojímu placení licencí na operační systém.



## <a name="next-steps"></a>Další kroky
Přečtěte si tyto články:
- [SAP HANA konfigurací infrastruktury a operací v Azure](./hana-vm-operations.md).
- [Úlohy SAP v Azure: kontrolní seznam pro plánování a nasazení](./sap-deployment-checklist.md).
