---
title: Migrace SAP HANA v Azure (velké instance) do virtuálních počítačů Azure| Dokumenty společnosti Microsoft
description: Jak migrovat SAP HANA v Azure (velké instance) do virtuálních počítačů Azure
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
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617044"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA na Azure Velké instance migrace do virtuálních počítačů Azure
Tento článek popisuje možné scénáře nasazení azure velké instance a nabízí plánování a migrace přístup s minimalizované prostoje přechodu

## <a name="overview"></a>Přehled
Od oznámení velkých instancí Azure pro SAP HANA (HLI) v září 2016 mnoho zákazníků přijalo tento hardware jako nabídku služby pro svou výpočetní platformu v paměti.  V posledních letech rozšíření velikosti virtuálního počítače Azure spolu s podporou nasazení na škálování hana překročilo požadavky na kapacitu databáze ERP většiny podnikových zákazníků.  Začínáme vidět zákazníky, kteří vyjadřují zájem o migraci úloh SAP HANA z fyzických serverů do virtuálních počítačů Azure.
Tato příručka není krok za krokem konfigurační dokument. Popisuje běžné modely nasazení a nabízí plánování a migraci radí.  Záměrem je vyvolat nezbytné aspekty pro přípravu minimalizovat prostoje přechodu.

## <a name="assumptions"></a>Předpoklady
Tento článek umožňuje následující předpoklady:
- Jediný zájem, který se zvažuje, je homogenní migrace výpočetní služby databáze HANA z Hana Large Instance (HLI) do virtuálního počítače Azure bez významného upgradu softwaru nebo oprav. Tyto menší aktualizace zahrnují použití novější verze operačního systému nebo hana verze výslovně uvedeno jako podporované příslušné poznámky SAP. 
- Všechny aktualizace nebo upgrady aktivity je třeba provést před nebo po migraci.  Například SAP HANA MCOS převod na nasazení MDC. 
- Přístup migrace, který by nabídl nejméně prostojů, je replikace systému SAP HANA. Jiné metody migrace nejsou součástí rozsahu tohoto dokumentu.
- Tyto pokyny platí jak pro rev3, tak pro skutážní prodej vysokého napětí pro rev3.
- Architektura nasazení HANA zůstává během migrace primárně nezměněna.  To znamená, že systém s jednou instancí ZOTAVENÍ po havárii zůstane v cílovém umístění stejným způsobem.
- Zákazníci zkontrolovali a pochopili smlouvu o úrovni služeb (SLA) cílové (nabýt) architektury. 
- Obchodní podmínky mezi HLI a virtuálními stránkami se liší. Zákazníci by měli sledovat využití svých virtuálních počítače pro správu nákladů.
- Zákazníci chápou, že HLI je vyhrazená výpočetní platforma, zatímco virtuální počítače běží na sdílené, ale izolované infrastruktuře.
- Zákazníci ověřili, že cílové virtuální počítače podporují zamýšlenou architekturu. Chcete-li zobrazit všechny podporované skutové sady virtuálních zařízení certifikované pro nasazení SAP HANA, přečtěte si [hardwareový adresář SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Zákazníci ověřili plán návrhu a migrace.
- Plán pro virtuální virtuální virtuální server pro zotavení po havárii spolu s primární lokalitou.  Zákazníci nemohou použít HLI jako uzel zotavení po havárii pro primární lokalitu spuštěnou na virtuálních počítačích po migraci.
- Zákazníci zkopírovali požadované záložní soubory, aby cílili virtuální počítače, a to na základě požadavků na obnovení podniku a dodržování předpisů. Díky zálohám s virtuálním počítačem umožňuje obnovení v čase v průběhu přechodného období.
- Pro HSR HA musí zákazníci nastavit a nakonfigurovat zařízení STONITH podle průvodců SAP HANA HA pro [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) a [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Není to předkonfigurované jako případ HLI.
- Tento přístup migrace se nevztahuje na hli sku s konfigurací Optane.

## <a name="deployment-scenarios"></a>Scénáře nasazení
Běžné modely nasazení se zákazníky HLI jsou shrnuty v následující tabulce.  Migrace do virtuálních počítačů Azure pro všechny scénáře HLI je možná.  Chcete-li využívat doplňkové služby Azure k dispozici, může být vyžadováno menší změny architektury.

| ID scénáře | Scénář HLI | Chcete doslovně migrovat na virtuální hod? | Poznámka |
| --- | --- | --- | --- |
| 1 | [Jeden uzel s jedním SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Ano | - |
| 2 | [Jeden uzel s MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Ano | - |
| 3 | [Jeden uzel s zotavenípo uhlazenou pomocí replikace úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Ne | Replikace úložiště není dostupná s virtuální platformou Azure, změna aktuálního řešení zotavení po havárii na HSR nebo zálohování a obnovení |
| 4 | [Jeden uzel s DR (víceúčelový) pomocí replikace úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Ne | Replikace úložiště není dostupná s virtuální platformou Azure, změna aktuálního řešení zotavení po havárii na HSR nebo zálohování a obnovení |
| 5 | [HSR s STONITH pro vysokou dostupnost](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Ano | Žádné předkonfigurované SBD pro cílové virtuální počítače.  Vyberte a nasaďte řešení STONITH.  Možné možnosti: Azure Fencing Agent (podporováno jak pro [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA s HSR, DR s replikací úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Ne | Nahrazení replikace úložiště pro potřeby zotavení po havárii buď hsr nebo zálohování maješky nebo obnovení |
| 7 | [Automatické převzetí služeb při selhání hostitele (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Ano | Použití ANF pro sdílené úložiště s virtuálními počítači Azure |
| 8 | [Horizontální navýšení kapacity s pohotovostním režimem](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Ano | BW/4HANA s M128s, M416s, M416ms Virtuální zařízení pomocí ANF pouze pro úložiště |
| 9 | [Horizontální navýšení kapacity bez pohotovostního režimu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Ano | BW/4HANA s M128s, M416s, M416ms VMs (s nebo bez použití ANF pro skladování) |
| 10 | [Horizontální navýšení kapacity pomocí zotavení po havárii pomocí replikace úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Ne | Nahrazení replikace úložiště pro potřeby zotavení po havárii buď hsr nebo zálohování maješky nebo obnovení |
| 11 | [Jeden uzel s DR pomocí HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Ano | - |
| 12 | [Jeden uzel HSR do DR (optimalizované pro náklady)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Ano | - |
| 13 | [HA a DR s HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Ano | - |
| 14 | [HA a DR s HSR (optimalizované pro náklady)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Ano | - |
| 15 | [Horizontální navýšení kapacity pomocí zotavení po havárii pomocí hsr](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Ano | BW/4HANA s M128s. M416s, M416ms Virtuální zařízení (s nebo bez použití ANF pro úložiště) |


## <a name="source-hli-planning"></a>Plánování zdroje (HLI)
Při registraci serveru HLI prošli správa služeb společnosti Microsoft i zákazníci plánováním výpočetních, síťových, úložných a operačních nastavení pro spuštění databáze SAP HANA.  Podobné plánování se musí uskutečnit pro migraci do virtuálního počítače Azure.

### <a name="sap-hana-housekeeping"></a>Sap HANA úklid 
Je vhodné uklidit obsah databáze tak nežádoucí, zastaralá data nebo zastaralé protokoly nejsou migrovány do nové databáze.  Úklid obecně zahrnuje odstranění nebo archivaci starých dat, jejichž platnost vypršela nebo neaktivních dat.  Tato opatření "hygieny údajů" by měla být testována v nevýrobních systémech, aby se ověřila platnost jejich údajů před použitím do výroby.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Povolit připojení k síti pro nové virtuální počítače nebo virtuální síť 
V nasazení HLI zákazníka byla síť nastavena na základě informací popsaných v článku [architektura sítě SAP HANA (Velké instance).](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Směrování síťového provozu se také provádí způsobem popsaným v části Směrování v Azure.
- Při nastavování nového virtuálního počítače jako cíle migrace, Pokud je umístěn v existující virtuální síti s rozsahy IP adres již povoleno připojení k HLI, není nutná žádná další aktualizace připojení.
- Pokud je nový virtuální počítač Azure umístěn v nové virtuální síti Microsoft Azure, může být v jiné oblasti a partnerem existující virtuální sítě, klíč služby ExpressRoute a ID prostředků z původního zřizování HLI jsou použitelné pro povolení přístupu pro tento nový virtuální rozsahu IP adres sítě.  Koordinujte se službou Microsoft Service Management a povolte připojení virtuální sítě k připojení HLI.  Poznámka: Chcete-li minimalizovat latenci sítě mezi vrstvami aplikace a databáze, musí být vrstvy aplikace i databáze ve stejné virtuální síti.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Existující sada dostupnosti aplikační vrstvy, zóny dostupnosti a skupina umístění bezkontaktní komunikace (PPG)
Aktuální model nasazení se provádí ke splnění určitých cílů na úrovni služeb.  V tomto kroku zajistěte, aby cílová infrastruktura splňovala nebo překračovala stanovené cíle.  
Zákazníci sap aplikační servery jsou pravděpodobně umístěny v množině dostupnosti.  Pokud je současná úroveň služby nasazení uspokojivá a 
- Pokud cílový virtuální virtuální_ virtuální_ přijmutí název hostitele logického názvu HLI, aktualizace služby DNS (DNS) řešení adres ukazující na IP virtuálního uživatele by fungovat bez aktualizace všech profilů SAP
- Pokud nepoužíváte PPG, nezapomeňte umístit všechny servery aplikace a DB do stejné zóny, abyste minimalizovali latenci sítě.
- Pokud používáte ppg, podívejte se do části tohoto dokumentu: Plánování destinace, Sada dostupnosti, Zóny dostupnosti a Skupina umístění blízkosti (PPG)".

### <a name="storage-replication-discontinuance-process-if-used"></a>Proces přerušení replikace úložiště (pokud je použit)
Pokud replikace úložiště se používá jako řešení zotavení po Havárii, by měla být ukončena (neplánovaná) po vypnutí aplikace SAP.  Kromě toho poslední SAP HANA katalog, soubor protokolu a zálohy dat byly replikovány na vzdálené svazky úložiště DR HLI.  Pokud tak učiníte jako preventivní opatření v případě, že dojde k havárii během přechodu fyzického serveru na virtuální počítač Azure.

### <a name="data-backups-preservation-consideration"></a>Zvažování zachování záloh dat
Po přestřihl na SAP HANA na virtuálním počítači Azure, všechna data založená na snímku nebo zálohy protokolů na HLI nejsou snadno přístupné nebo obnovitelné pro virtuální počítač v případě potřeby. V období časného přechodu, než záloha založená na Azure vytvoří dostatek historie, aby splňovala požadavky na obnovení bodu v čase, doporučujeme kromě snímků na HLI, dny nebo týdny před přechodem pořizovací místo pořizovací masy.  Tyto zálohy zkopírujte do účtu Azure Storage přístupného pomocí nového virtuálního počítače SAP HANA.
Kromě zálohování obsahu HLI je rozumné mít úplné zálohy prostředí SAP snadno přístupné v případě, že je potřeba vrácení zpět.

### <a name="adjusting-system-monitoring"></a>Monitorování seřizovacího systému 
Zákazníci používají mnoho různých nástrojů ke sledování a odesílání upozornění pro systémy v rámci jejich prostředí SAP.  Tato položka je pouze výzvou pro příslušnou akci, která zahrnuje změny pro monitorování a aktualizaci příjemců oznámení výstrahv případě potřeby.

### <a name="microsoft-operations-team-involvement"></a>Zapojení operačního týmu Microsoft 
Otevřete lístek z portálu Azure na základě existující instance HLI.  Po vytvoření lístku podpory vás bude kontaktovat pracovník technické podpory prostřednictvím e-mailu.  

### <a name="engage-microsoft-account-team"></a>Zapojení týmu účtů Microsoft
Plánování migrace v blízkosti výročí obnovení čas smlouvy HLI minimalizovat zbytečné náklady na výpočetní prostředek. K vyřazení čepele HLI je nutné koordinovat ukončení smlouvy a skutečné vypnutí jednotky.

## <a name="destination-planning"></a>Plánování destinace
Postavit se nové infrastruktuře, která by zaujala stávající infrastrukturu, si zaslouží určité zamyšlení, aby se nový přírůstek vešel do velkého schématu věcí.  Níže jsou uvedeny některé klíčové body pro rozjímání.

### <a name="resource-availability-in-the-target-region"></a>Dostupnost zdrojů v cílové oblasti 
Aktuální oblast nasazení aplikačních serverů SAP jsou obvykle v těsné blízkosti přidružených HLI.  HlI jsou však nabízeny v méně umístěních než dostupné oblasti Azure.  Při migraci fyzické HLI do virtuálního počítače Azure, je také vhodná doba k "doladění" vzdálenost blízkosti všech souvisejících služeb pro optimalizaci výkonu.  Přitom jedním z klíčových hledisek je zajistit, aby vybraná oblast disponorovala všemi požadovanými prostředky.  Například dostupnost určité řady virtuálních počítačů nebo nabídka zón Azure pro nastavení s vysokou dostupností.

### <a name="virtual-network"></a>Virtuální síť 
Zákazníci musí zvolit, zda chcete spustit novou databázi HANA v existující virtuální síti nebo vytvořit novou.  Primárním rozhodujícím faktorem je aktuální síťové rozložení pro prostředí SAP.  Také když infrastruktura přejde z jednozónového na dvouzónové nasazení a používá PPG, ukládá změnu architektury. Další informace najdete v článku [Azure PPG pro optimální latenci sítě s aplikací SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Zabezpečení
Ať už nový virtuální počítač SAP HANA přistane na nové nebo existující virtuální síti/podsíti, představuje novou důležitou službu podniku, která vyžaduje ochranu.  Řízení přístupu v souladu se zásadami zabezpečení informací o společnosti by mělo být vyhodnoceno a nasazeno pro tuto novou třídu služeb.

### <a name="vm-sizing-recommendation"></a>Doporučení pro velikost virtuálního montovana
Tato migrace je také příležitost správné velikosti vašeho výpočetního stroje HANA.  Pomocí [zobrazení systému](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) HANA ve spojení s HANA Studio lze pochopit spotřebu systémových prostředků, což umožňuje správné velikosti pro zvýšení efektivity výdajů.

### <a name="storage"></a>Úložiště 
Výkon úložiště je jedním z faktorů, které ovlivňují uživatelské prostředí aplikace SAP.  Založit na dané skladové jednotce virtuálního počítače, existují minimální rozložení úložiště publikované [konfigurace virtuálního počítače SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Doporučujeme zkontrolovat tyto minimální specifikace a porovnat se stávajícími statistikami systému HLI, aby byla zajištěna odpovídající kapacita a výkon vyvěšení videa pro nový virtuální vod HANA.

Pokud nakonfigurujete PPG pro nový virtuální virtuální soud HANA a jeho přidružené servery, odešlete lístek podpory ke kontrole a zajistěte společné umístění úložiště a virtuálního počítače. Vzhledem k tomu, že vaše řešení zálohování může být nutné změnit, náklady na úložiště by měly být také přehodnoceny, aby se zabránilo překvapení provozních výdajů.

### <a name="storage-replication-for-disaster-recovery"></a>Replikace úložiště pro zotavení po havárii
S HLI replikace úložiště byla nabídnuta jako výchozí možnost pro zotavení po havárii. Tato funkce není výchozí možnost pro SAP HANA na virtuálním počítači Azure. Zvažte HSR, zálohování / obnovení nebo jiná podporovaná řešení splňující vaše obchodní potřeby.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Sady dostupnosti, zóny dostupnosti a skupiny umístění bezkontaktní komunikace 
Chcete-li zkrátit vzdálenost mezi aplikační vrstvou a SAP HANA, aby byla latence sítě minimálně, měl by být nový databázový virtuální virtuální soud a aktuální aplikační servery SAP umístěny do PPG. Informace o tom, jak sada dostupnosti Azure a zóny dostupnosti Azure fungují s PPG pro nasazení SAP, najdete v centru [pro umístění blízkosti.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)
Pokud jsou členové cílového systému HANA nasazeni ve více než jedné zóně Azure, zákazníci by měli mít přehledné zobrazení profilu latence vybraných zón. Umístění systémových komponent SAP je optimální z ohledem na proximální vzdálenost mezi aplikací SAP a databází.  Nástroj [pro testování latence zóny dostupnosti](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) ve veřejné doméně usnadňuje měření.  


### <a name="backup-strategy"></a>Strategie zálohování
Mnoho zákazníků již používá řešení zálohování třetích stran pro SAP HANA na HLI.  V takovém případě je třeba nakonfigurovat pouze další chráněné databáze virtuálních počítače a HANA.  Probíhající úlohy zálohování HLI nyní mohou být neplánované, pokud je počítač po migraci vyřazen z provozu.
Azure Backup pro SAP HANA na virtuálním počítači je teď obecně dostupné.  Podrobné informace o tématech: [Zálohování](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [Obnovení](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), Správa záloh SAP HANA ve virtuálních počítačích Azure [najdete](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) v těchto odkazech.

### <a name="dr-strategy"></a>Strategie dr.
Pokud vaše cíle úrovně služeb přizpůsobit delší dobu obnovení, jednoduché zálohování do úložiště objektů blob a obnovení na místě nebo obnovení nového virtuálního počítače je nejjednodušší a nejlevnější strategie zotavení po havárii.  
Stejně jako platforma velké instance, kde HANA DR se obvykle provádí s HSR; Na virtuálním počítači Azure hsr je také nejpřirozenější a nativní SAP HANA DR řešení.  Bez ohledu na to, zda je zdrojové nasazení s jednou instancí nebo clusterem, je v oblasti zotavení po havárii vyžadována replika zdrojové infrastruktury.
Tato replika zotavení po havárii bude nakonfigurována po dokončení primární migrace HLI na virtuální počítače.  DR HANA DB se zaregistruje do primární instance SAP HANA na instanci virtuálního počítači jako sekundární replikační lokality.  

### <a name="sap-application-server-connectivity-destination-change"></a>Změna cíle připojení aplikačního serveru SAP
Výsledkem migrace HSR je nový hostitel HANA DB a proto nový název hostitele DB pro aplikační vrstvu, profily SAP je třeba upravit tak, aby odrážely nový název hostitele.  Pokud se přepínání provádí překladem názvů zachováním názvu hostitele, není nutná žádná změna profilu.

### <a name="operating-system"></a>Operační systém
Bitové kopie operačního systému pro HLI a VM, přestože jsou na stejné úrovni vydání, například SLES 12 SP4, nejsou identické. Zákazníci musí ověřit požadované balíčky, opravy hot, opravy, jádro a opravy zabezpečení na HLI nainstalovat stejné balíčky na cíl.  Je podporováno použití funkce HSR k replikaci ze staršího operačního systému na virtuální počítač s novější verzí operačního systému.  Ověřte konkrétní podporované verze kontrolou [poznámky SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nová žádost o licenci SAP
Jednoduchý popis pro vyžádání nové licence SAP pro nový systém HANA teď, když byl migrován do virtuálních vás.

### <a name="service-level-agreement-sla-differences"></a>Rozdíly ve smlouvě o úrovni služeb (SLA)
Autoři chtěli volat na rozdíl dostupnosti SLA mezi HLI a Azure VM.  Například clusterované páry HLIs HA nabízejí 99,99% dostupnost. K dosažení stejné sla, je nutné nasadit virtuální chod v zónách dostupnosti. Tento [článek](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) popisuje dostupnost s přidruženými architekturami nasazení, aby zákazníci mohli odpovídajícím způsobem plánovat cílovou infrastrukturu.


## <a name="migration-strategy"></a>Strategie migrace
V tomto dokumentu se vztahuje pouze hana systémová replikace přístup pro migraci z HLI do virtuálního počítače Azure.  Závisí na cílové řešení úložiště nasazeny, proces se mírně liší. Kroky vysoké úrovně jsou popsány níže.

### <a name="vm-with-premiumultra-disks-for-data"></a>Virtuální počítač s prémiovými/ultradisky pro data
Pro virtuální počítače, které jsou nasazené s prémií nebo ultradisky, standardní konfigurace replikace systému SAP HANA je použitelná pro nastavení HSR.  [Článek nápovědy SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) poskytuje přehled kroků spojených s nastavením replikace systému, převzetím sekundárního systému, vrácením primárního systému a zakázáním replikace systému.  Pro účely migrace budeme potřebovat pouze nastavení, převzetí a zakázání kroků replikace.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Virtuální přístup s ANF pro svazky dat a protokolů
Na vysoké úrovni nejnovější HLI storage snímky úplných dat a svazků protokolu je třeba zkopírovat do služby Azure Storage, kde jsou přístupné a obnovitelné cílovým virtuálním počítačem HANA.  Proces kopírování lze provést pomocí libovolných nativních nástrojů pro kopírování Linuxu.  

> [!IMPORTANT]
> Kopírování a přenos dat může trvat hodiny, závisí na velikosti databáze HANA a šířce pásma sítě.  Převážná část procesu kopírování by měla být provedena před primární prostoje HANA DB.

### <a name="mcos-to-mdc-conversion"></a>Převod MCOS na MDC
Model nasazení Multiple Components in One System (MCOS) byl používán některými našimi zákazníky HLI.  Motivací bylo obejít omezení úložiště kontejneru více databází (MDC) starších verzí SAP HANA.  V modelu MCOS několik nezávislých SAP HANA instance jsou naskládány do jednoho okna HLI.  Použití HSR pro migraci by fungovalo dobře, ale výsledkem je více virtuálních počítačích HANA s jedním tenantem DB.  Tento konečný stav vytváří rušnější krajinu, než na jakou si zákazník zvykl.  S SAP HANA 2.0 výchozí nasazení je MDC, životaschopnou alternativou je provést [přesun klienta HANA](https://launchpad.support.sap.com/#/notes/2472478) po migraci HSR.  Tento proces "konsoliduje" tyto nezávislé databáze HANA do spolunájemníků v jednom kontejneru HANA.  

### <a name="application-layer-consideration"></a>Zvažování aplikační vrstvy
Db server je považován za střed systému SAP.  Všechny aplikační servery by měly být umístěny v blízkosti DATABÁZE SAP HANA.  V některých případech, kdy je požadováno nové použití PPG, může být požadováno přemístění existujících aplikačních serverů do PPG, kde je vyžadován virtuální počítač HANA.  Vytváření nových aplikačních serverů může být považováno za jednodušší, pokud již máte po ruce šablony nasazení.  
Pokud jsou existující aplikační servery a nový virtuální virtuální soud HANA optimálně umístěny, není nutné sestavit žádné nové aplikační servery, pokud není vyžadována další kapacita.  
Pokud je vytvořena nová infrastruktura pro zvýšení dostupnosti služeb, stávající aplikační servery mohou být zbytečné a měly by být vypnuty a odstraněny.
Pokud se změnil název hostitele cílového virtuálního uživatele a liší se od názvu hostitele HLI, je třeba upravit profily aplikačního serveru SAP tak, aby ukazovaly na nového hostitele.  Pokud se změnila pouze IP adresa HANA DB, je k vedení příchozích připojení k novému virtuálnímu virtuálnímu virtuálnímu soudu HANA nutná aktualizace záznamu DNS.

### <a name="acceptance-test"></a>Přejímací zkouška
Přestože migrace z HLI do virtuálního počítači neprovede žádnou podstatnou změnu obsahu databáze ve srovnání s heterogenní migrací, stále doporučujeme ověření klíčových funkcí a aspekt výkonu nového nastavení.  

### <a name="cutover-plan"></a>Plán přetoku
I když je tato migrace přímočará, zahrnuje však vyřazení existující databáze DB z provozu.  Pečlivé plánování zachování zdrojového systému s přidruženým obsahem a záložními bitovými kopiemi jsou důležité v případě, že je nutné záložní.  Dobré plánování nabízí rychlejší zvrat.   


## <a name="post-migration"></a>Po dokončení migrace
Migrace úloha není provedena, dokud jsme bezpečně oddělit všechny služby závislé na HLI nebo připojení k zajištění integrity dat je zachována.  Také vypnout zbytečné služby.  Tato část volá několik top-of-mind položky.

### <a name="decommissioning-the-hli"></a>Vyřazení HLI z provozu
Po úspěšné migraci HANA DB do virtuálního počítače Azure zajistěte, aby na HLI DB neběžely žádné produktivní obchodní transakce.  Udržování hli běží po dobu rovnající se jeho místní záložní uchovávání okna je však bezpečné praxe zajištění rychlejší obnovení v případě potřeby.  Teprve poté by měl být nůž HLI vyřazen z provozu.  Zákazníci by měli smluvně uzavřít své závazky hli se společností Microsoft kontaktováním svých zástupců společnosti Microsoft.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Odstraňte všechny proxy (ex: Iptables, BIGIP) nakonfigurované pro HLI 
Pokud proxy služby, jako je IPTables se používá ke směrování místního provozu do a z HLI, již není potřeba po úspěšné migraci na virtuální počítač.  Tato služba připojení by však měla být zachována tak dlouho, dokud je čepel HLI stále v pohotovosti.  Službu vypněte až po úplném vyřazení čepele HLI z provozu.

### <a name="remove-global-reach-for-hli"></a>Odebrat globální dosah pro HLI 
Global Reach se používá k propojení brány ExpressRoute zákazníků s bránou HLI ExpressRoute.  Umožňuje místnímu provozu zákazníků dostat se přímo k tenantovi HLI bez použití proxy služby.  Toto připojení již není potřeba v případě nepřítomnosti jednotky HLI po migraci.  Stejně jako v případě proxy služby IPTables by měl být GlobalReach také udržován, dokud nebude čepel HLI zcela vyřazena z provozu.

### <a name="operating-system-subscription--movereuse"></a>Předplatné operačního systému – přesunout/znovu použít
Vzhledem k tomu, že servery virtuálních zařízení jsou vztyčené a hli listy jsou vyřazeny z provozu, předplatná operačního okna mohou být nahrazena nebo znovu použita, aby se zabránilo dvojímu placení licencí operačního serveru.



## <a name="next-steps"></a>Další kroky
Přečtěte si tyto články:
- [Konfigurace a operace infrastruktury SAP HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [Úlohy SAP v Azure: kontrolní seznam plánování a nasazení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
