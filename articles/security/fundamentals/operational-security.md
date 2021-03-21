---
title: Provozní zabezpečení Azure | Microsoft Docs
description: Pokud si chcete Microsoft Azure monitorovat protokoly, její služby a jak funguje, přečtěte si tento přehled.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 7d71820db3d58931f2fcd8d18441534ad36183c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711990"
---
# <a name="azure-operational-security"></a>Provozní zabezpečení Azure
## <a name="introduction"></a>Úvod

### <a name="overview"></a>Přehled
Víme, že zabezpečení je úloha v cloudu a jak důležité je, že najdete přesné a včasné informace o zabezpečení Azure. Jedním z nejlepších důvodů, jak používat Azure pro vaše aplikace a služby, je využít výhod nejrůznějších nástrojů zabezpečení a dostupných možností. Tyto nástroje a možnosti usnadňují vytváření zabezpečených řešení na zabezpečené platformě Azure. Microsoft Azure musí poskytovat důvěrnost, integritu a dostupnost zákaznických dat a zároveň umožňuje transparentní zodpovědnost.

Abychom zákazníkům pomohli lépe pochopit pole bezpečnostních mechanismů implementovaných v rámci Microsoft Azure z provozních perspektiv od zákazníka i od Microsoftu, je tento dokument white paper "provozní zabezpečení Azure" napsán, který poskytuje komplexní přehled o provozním zabezpečení, které je k dispozici v systému Windows Azure.

### <a name="azure-platform"></a>Platforma Azure
Azure je platforma veřejné cloudové služby, která podporuje širokou škálu operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení. Může spouštět kontejnery Linux s integrací Docker. Vytvářejte aplikace pomocí jazyků JavaScript, Python, .NET, PHP, Java a Node.js; Vytvořte back-endy pro zařízení s iOS, Androidem a Windows. Cloudová služba Azure podporuje stejné technologie jako miliony pro vývojáře a odborníky na IT, kteří už využívají a důvěřují jim.

Když vytváříte nebo migrujete prostředky IT na, poskytovatele veřejné cloudové služby, kterého se spoléháte na schopnosti této organizace chránit vaše aplikace a data službami a ovládacími prvky, které poskytují ke správě zabezpečení cloudových prostředků.

Infrastruktura Azure je navržená od zařízení až po aplikace pro hostování milionů zákazníků současně a poskytuje důvěryhodný základ, na kterém podniky můžou splňovat požadavky na zabezpečení. Kromě toho Azure poskytuje řadu konfigurovatelných možností zabezpečení a možnost je ovládat, abyste mohli přizpůsobit zabezpečení, aby splňovaly jedinečné požadavky nasazení vaší organizace. Tento dokument vám pomůže pochopit, jak vám funkce zabezpečení Azure můžou tyto požadavky splnit.

### <a name="abstract"></a>Shrnutí
Provozní zabezpečení Azure odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svých dat, aplikací a dalších prostředků v Microsoft Azure. Provozní zabezpečení Azure je postavené na platformě, která zahrnuje znalostní bázi nejrůznějších funkcí, které jsou jedinečné pro společnost Microsoft, včetně Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Center a podrobného povědomí o kyberbezpečnosti hrozbách.

Tento dokument white paper popisuje přístup Microsoftu k provoznímu zabezpečení Azure v rámci Microsoft Azure cloudové platformy a zahrnuje následující služby:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-introduction.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Sledovací proces sítě Azure](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Analýzy Azure Storage](/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure monitorování protokolů

Protokoly monitorování Microsoft Azure jsou řešení pro správu IT pro hybridní cloud. Azure Monitor protokoly vám pro cloudovou správu vaší infrastruktury měli možnost použít samostatně nebo pro rozšiřování stávajícího nasazení nástroje System Center.

![Protokoly služby Azure Monitor](./media/operational-security/azure-operational-security-fig1.png)

Díky protokolům Azure Monitor můžete spravovat libovolnou instanci v jakémkoli cloudu, včetně místních, Azure, AWS, Windows serveru, Linux, VMware a OpenStack, s nižšími náklady než konkurenční řešení. Protokoly Azure Monitor, které jsou vytvořené pro cloudový svět, nabízí nový přístup ke správě vašeho podniku, který je Nejrychlejším a nejefektivnějším způsobem pro splnění nových obchodních výzev a přizpůsobení nových úloh, aplikací a cloudových prostředí.

### <a name="azure-monitor-services"></a>Služby Azure Monitor

Základní funkce protokolů Azure Monitor poskytují sady služeb, které běží v Azure. Každá služba poskytuje specifickou funkci správy a kombinováním služeb je možné dosahovat různých scénářů správy.

| Služba  | Popis|
| :------------- | :-------------|
| Protokoly služby Azure Monitor | Monitorování a analýza dostupnosti a výkonu různých prostředků včetně fyzických a virtuálních počítačů |
|Automation | Automatizace ručních procesů a vynucení konfigurací pro fyzické a virtuální počítače |
| Backup | Zálohování a obnovení důležitých dat. |
| Site Recovery | Poskytnutí vysoké dostupnosti pro důležitá data |

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure monitor](https://azure.microsoft.com/documentation/services/log-analytics) poskytují monitorovací služby shromažďováním dat ze spravovaných prostředků do centrálního úložiště. Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export.


Tato metoda umožňuje konsolidovat data z různých zdrojů, takže můžete kombinovat data ze služeb Azure s existujícím místním prostředím. Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.


![Diagram, který zobrazuje konsolidaci dat z různých zdrojů, takže můžete kombinovat data ze služeb Azure s existujícím místním prostředím.](./media/operational-security/azure-operational-security-fig2.png)

Služba Azure Monitor spravuje vaše cloudová data bezpečně pomocí následujících metod:
-   oddělení dat
-   uchovávání dat
-   fyzické zabezpečení
-   Správa incidentů
-   dodržování
-   certifikace standardů zabezpečení

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) poskytuje služby zálohování a obnovení dat a je součástí sady Azure monitor produktů a služeb.
Chrání data vaší aplikace a dlouhá léta je uchovává bez nutnosti velkých investic a s minimálními provozními náklady. Kromě úloh aplikací, jako jsou SQL Server a SharePoint, může zálohovat data z fyzických i virtuálních serverů Windows. Můžete ho také použít v [nástroji System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) k replikaci chráněných dat do Azure pro zajištění redundance a dlouhodobého uložení.


Chráněná data ve službě Azure Backup se ukládají do trezoru záloh umístěného v konkrétní geografické oblasti. Data se replikují v rámci stejné oblasti a v závislosti na typu trezoru se můžou taky replikovat do jiné oblasti a zajistit tak odolnost proti chybám.

### <a name="management-solutions"></a>Řešení pro správu
[Azure monitor](../../security-center/security-center-introduction.md) je cloudové řešení pro správu IT od Microsoftu, které pomáhá spravovat a chránit místní i cloudovou infrastrukturu.


[Řešení pro správu](../../azure-monitor/insights/solutions.md) jsou předbalené sady logik, které implementují konkrétní scénář správy pomocí jedné nebo více Azure monitor služeb. Od Microsoftu jsou k dispozici různá řešení, která můžete snadno přidat do svého předplatného Azure, abyste zvýšili hodnotu vaší investice do Azure Monitor. Jako partner můžete vytvořit vlastní řešení pro podporu aplikací a služeb a poskytnout je uživatelům prostřednictvím Azure Marketplace nebo šablon pro rychlý Start.


![Řešení pro správu](./media/operational-security/azure-operational-security-fig4.png)

Dobrým příkladem řešení, které využívá více služeb k poskytnutí dalších funkcí je [Update Management řešení](../../automation/update-management/overview.md). Toto řešení používá agenta [Azure Monitorch protokolů](../../azure-monitor/logs/log-query-overview.md) pro systémy Windows a Linux ke shromáždění informací o požadovaných aktualizacích u každého agenta. Tato data zapisuje do úložiště protokolů Azure Monitor, kde je můžete analyzovat pomocí vloženého řídicího panelu.

Při vytváření nasazení se k instalaci požadovaných aktualizací použijí Runbooky v [Azure Automation](../../automation/automation-intro.md) . Celý tento proces spravujete na portálu a nemusíte se starat o podrobnosti.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomáhá chránit vaše prostředky Azure. Poskytuje integrované monitorování zabezpečení a správu zásad napříč předplatnými Azure. V rámci služby můžete definovat zásady nejen proti předplatným Azure, ale také ke [skupinám prostředků](../../azure-resource-manager/management/overview.md#resource-groups), abyste mohli podrobnější informace.

### <a name="security-policies-and-recommendations"></a>Zásady a doporučení zabezpečení

Zásady zabezpečení definují sadu ovládacích prvků doporučenou pro prostředky v rámci daného předplatného nebo skupiny prostředků.

V Security Center určíte zásady na základě toho, jaké má vaše společnost požadavky na zabezpečení, a podle typu aplikací nebo citlivosti dat.

![Zásady a doporučení zabezpečení](./media/operational-security/azure-operational-security-fig5.png)


Zásady povolené na úrovni předplatného se automaticky šíří do všech skupin prostředků v rámci předplatného, jak je znázorněno v diagramu na pravé straně:


### <a name="data-collection"></a>Shromažďování dat

Security Center shromažďuje data z vašich virtuálních počítačů za účelem posouzení jejich stavu, poskytování doporučení zabezpečení a upozorňování na hrozby. Po prvním přístupu Security Center je shromažďování dat povolené na všech virtuálních počítačích v rámci vašeho předplatného. Shromažďování dat je doporučené, ale můžete je zrušit vypnutím shromažďování dat v rámci zásad služby Security Center.

### <a name="data-sources"></a>Zdroje dat

- Azure Security Center analyzuje data z následujících zdrojů a poskytuje přehled o stavu vašeho zabezpečení, zjišťuje ohrožení zabezpečení a doporučuje způsoby zmírnění rizik a detekuje aktivní hrozby:

-   Služby Azure: Využívá informace o konfiguraci služeb Azure, které máte nasazené, tím, že komunikuje s poskytovatelem prostředků pro příslušnou službu.

- Síťový provoz: Využívá vzorkovaná metadata síťového provozu z infrastruktury společnosti Microsoft, jako je třeba zdrojová a cílová IP adresa/port, velikost paketu nebo síťový protokol.

-   Partnerská řešení: Využívá výstrahy zabezpečení ze všech integrovaných partnerských řešení, jako jsou třeba brány firewall a antimalwarová řešení.

-   Virtuální počítače: Využívá z vašich virtuálních počítačů konfigurační informace a informace o událostech zabezpečení, jako jsou třeba protokoly událostí a auditů systému Windows, protokoly IIS, zprávy syslog a soubory se stavem systému.

### <a name="data-protection"></a>Ochrana dat

Služba Azure Security Center pomáhá zákazníkům předcházet hrozbám, detekovat je a reagovat na ně tím, že shromažďuje a zpracovává data související se zabezpečením, včetně informací o konfiguraci, metadat, protokolů událostí, souborů se stavem systému a dalších dat. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb.

-   **Oddělení dat**: Data se v rámci služby ukládají logicky oddělená pro jednotlivé komponenty. Všechna data jsou označená podle organizace. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby.

-   **Přístup k datům**: aby bylo možné poskytnout doporučení pro zabezpečení a prozkoumat potenciální bezpečnostní hrozby, mohou pracovníci společnosti Microsoft získat přístup k informacím shromažďovaným nebo analyzovaným službami Azure, včetně souborů s výpisem stavu systému, událostí vytváření procesů, snímků disků virtuálních počítačů a artefaktů, které mohou neúmyslně zahrnovat zákaznická data nebo osobní údaje z vašich virtuálních počítačů. V [souladu s podmínkami služby Microsoft Online Services a prohlášením o zásadách ochrany osobních údajů](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), které společnost Microsoft nepoužívá zákaznická data ani informace z nich nevyužívá pro reklamní nebo podobné obchodní účely.

-   **Použití dat**: Společnost Microsoft vylepšuje své schopnosti prevence a detekce pomocí schémat a analýzy hrozeb napříč několika klienty. Činíme tak v souladu se závazky k ochraně osobních údajů popsanými v našem [Prohlášení o zásadách ochrany osobních údajů](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Umístění dat

Azure Security Center shromažďuje dočasné kopie souborů se stavem systému a analyzuje je za účelem detekce stop pokusů o napadení zabezpečení, neúspěšných i úspěšných. Azure Security Center provádí tuto analýzu v rámci stejné geografie jako pracovní prostor a po dokončení analýzy tyto dočasné kopie odstraní. Artefakty počítačů se ukládají centrálně ve stejné oblasti jako virtuální počítač.

-   **Vaše účty úložiště**: účet úložiště je zadaný pro každou oblast, ve které jsou spuštěné virtuální počítače. To umožňuje ukládání dat v oblasti, kde se nachází virtuální počítač, ze kterého data jsou shromažďována.

-   **Azure Security Center Storage**: Informace o výstrahách zabezpečení, včetně partnerských výstrah, doporučení a stavu zabezpečení, se ukládají centrálně (v současnosti v USA). Tyto informace mohou podle potřeby zahrnovat související údaje o konfiguraci a událostech zabezpečení shromažďované z virtuálních počítačů, a to za účelem poskytování výstrah zabezpečení, doporučení nebo informací o stavu zabezpečení.


## <a name="azure-monitor"></a>Azure Monitor

Řešení [zabezpečení a audit protokolu Azure monitor](../../security-center/security-center-monitoring.md) umožňuje aktivně monitorovat všechny prostředky, což může přispět k minimalizaci dopadu incidentů zabezpečení. Protokoly Azure Monitor Security and Audit mají domény zabezpečení, které lze použít pro monitorování prostředků. Doména zabezpečení poskytuje rychlý přístup k možnostem. pro monitorování zabezpečení jsou další podrobnosti pokryty následujícími doménami:

-   Posouzení malwaru
-   Posouzení aktualizací
-   Identita a přístup.

Azure Monitor poskytuje odkazy na informace o konkrétních typech prostředků. Nabízí vizualizaci, dotaz, směrování, upozorňování, automatické škálování a automatizaci dat jak z infrastruktury Azure (protokol aktivit), tak z každého jednotlivého prostředku Azure (diagnostické protokoly).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


Cloudové aplikace jsou komplexní s mnoha pohybujícími se částmi. Monitorování poskytuje data, která zajistí, že vaše aplikace zůstane v dobrém stavu. Také vám pomůže nastavovat potenciální problémy nebo řešit potíže s předchozími.

Kromě toho můžete k získání podrobných přehledů o vaší aplikaci použít data monitorování. Tato znalostní báze vám může pomoci zvýšit výkon a udržovatelnost aplikace nebo automatizovat akce, které by jinak vyžadovaly ruční zásah.

### <a name="azure-activity-log"></a>Protokol aktivit Azure


Jedná se o protokol, který poskytuje přehled o operacích provedených u prostředků v rámci vašeho předplatného. Protokol aktivit se dřív nazýval "protokoly auditu" nebo "provozní protokoly", protože oznamuje události na úrovni ovládacího prvku pro vaše předplatná.

![Protokol aktivit Azure](./media/operational-security/azure-operational-security-fig7.png)

Pomocí protokolu aktivit můžete určit, kdo a kdy použít pro všechny operace zápisu (PUT, POST, DELETE) u prostředků ve vašem předplatném. Můžete také pochopit stav operace a dalších relevantních vlastností. Protokol aktivit nezahrnuje operace čtení (GET) nebo operací pro prostředky, které používají klasický model.

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

Tyto protokoly jsou vydávány prostředkem a poskytují bohatou a častou data o provozu daného prostředku. Obsah těchto protokolů se liší podle typu prostředku.

Například protokoly událostí systému Windows jsou jednou z kategorií diagnostického protokolu pro virtuální počítače a protokoly objektů blob, tabulek a front jsou kategoriemi diagnostických protokolů pro účty úložiště.

Diagnostické protokoly se liší od [protokolu aktivit (dříve označovaného jako protokol auditu nebo operační protokol)](../../azure-monitor/essentials/platform-logs-overview.md). Protokol aktivit nabízí přehled o operacích provedených u prostředků v rámci vašeho předplatného. Diagnostické protokoly poskytují přehled o operacích, které provedly samotné vaše prostředky.

### <a name="metrics"></a>Metriky

Azure Monitor vám umožňuje využívat telemetrii, abyste získali přehled o výkonu a stavu vašich úloh v Azure. Nejdůležitější typ dat telemetrie Azure jsou metriky (označované taky jako čítače výkonu) vydávané většinou prostředků Azure. Azure Monitor poskytuje několik způsobů, jak nakonfigurovat a využívat tyto [metriky](../../azure-monitor/data-platform.md) pro monitorování a řešení potíží. Metriky představují hodnotný zdroj telemetrie a umožňují provádět následující úlohy:

-   **Sledujte výkon** svého prostředku (jako je virtuální počítač, web nebo aplikace logiky), a to tak, že vykreslíte jeho metriky v grafu portálu a připnete ho k řídicímu panelu.

-   **Zobrazí se oznámení o problému** , který ovlivňuje výkon vašeho prostředku, pokud metrika překračuje určitou prahovou hodnotu.

-   **Konfigurace automatizovaných akcí**, jako je automatické škálování prostředku nebo vyvolávání Runbooku, pokud metrika převyšuje určitou prahovou hodnotu.

-   **Provádění pokročilých analýz** nebo vytváření sestav o trendech výkonu a využití vašeho prostředku.

-   **Archivujte** historii výkonu nebo stavu prostředku pro účely dodržování předpisů nebo auditování.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Je to schopnost v Azure, která umožňuje shromažďování diagnostických dat v nasazené aplikaci. Můžete použít diagnostické rozšíření z různých různých zdrojů. V současné době jsou podporované [webové role a role pracovních procesů Azure Cloud Service](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure Virtual Machines](../../virtual-machines/windows/overview.md) se systémem Microsoft Windows a [Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md). Další služby Azure mají svou vlastní samostatnou diagnostiku.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Auditování zabezpečení sítě je důležité pro detekci ohrožení zabezpečení sítě a zajištění souladu s vaším modelem zabezpečení IT a dodržováním předpisů. Pomocí zobrazení skupiny zabezpečení můžete načíst nakonfigurovanou skupinu zabezpečení sítě a pravidla zabezpečení a platná pravidla zabezpečení. Pomocí seznamu použitých pravidel můžete určit porty, které jsou otevřené, a posoudit ohrožení zabezpečení sítě.

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni sítě v, do a z Azure. Nástroje pro diagnostiku a diagnostiku sítě, které jsou dostupné v Network Watcher, vám pomůžou pochopit, diagnostikovat a získávat přehledy vaší sítě v Azure. Tato služba zahrnuje zachytávání paketů, další směrování, ověření toku protokolu IP, zobrazení skupiny zabezpečení a protokoly NSG Flow. Monitorování úrovně scénáře nabízí kompletní pohled na síťové prostředky na rozdíl od monitorování jednotlivých síťových prostředků.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

Network Watcher v současné době má následující možnosti:

-   **<a href="/azure/network-watcher/network-watcher-monitoring-overview">Protokoly auditu</a>**– zaprotokolují se operace prováděné jako součást konfigurace sítí. Tyto protokoly můžete zobrazit v Azure Portal nebo načíst pomocí nástrojů Microsoftu, jako jsou Power BI nebo nástroje třetích stran. Protokoly auditu jsou k dispozici prostřednictvím portálu, PowerShellu, CLI a rozhraní REST API. Další informace o protokolech auditu najdete v tématu operace auditu s Správce prostředků. Protokoly auditu jsou k dispozici pro operace provedené na všech síťových prostředcích.


-   **<a href="/azure/network-watcher/network-watcher-ip-flow-verify-overview">Ověření toku protokolu IP</a>** – kontroluje, jestli je paket povolený nebo zakázaný na základě informací o toku 5 – parametry paketů řazené kolekce členů (cílová IP adresa, zdrojová IP adresa, cílový port, zdrojový port a protokol). Pokud je paket odepřen skupinou zabezpečení sítě, vrátí se pravidlo a skupina zabezpečení sítě, která paket zamítla.

-   **<a href="/azure/network-watcher/network-watcher-next-hop-overview">Další směrování</a>** – Určuje další segment směrování pro pakety směrované do síťových prostředků Azure, což vám umožní diagnostikovat všechny nesprávně nakonfigurované trasy definované uživatelem.

-   **<a href="/azure/network-watcher/network-watcher-security-group-view-overview">Zobrazení skupiny zabezpečení</a>** – získá platná a použitá pravidla zabezpečení, která se používají na virtuálním počítači.

-   **<a href="/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Protokolování toku NSG</a>** – protokoly toku pro skupiny zabezpečení sítě umožňují zachytávání protokolů souvisejících s provozem povoleným nebo odepřeným pravidly zabezpečení ve skupině. Tok je definován pomocí informací o pěti seřazených kolekcích členů – zdrojová IP adresa, cílová IP adresa, zdrojový port, cílový port a protokol.

## <a name="azure-storage-analytics"></a>Analýza úložiště Azure

[Analýza úložiště](/rest/api/storageservices/fileservices/storage-analytics) může ukládat metriky, které zahrnují agregovanou statistiku transakcí a data kapacity týkající se požadavků do služby úložiště. Transakce jsou hlášeny jak na úrovni operace rozhraní API, tak na úrovni služby úložiště a kapacita se oznamuje na úrovni služby úložiště. Data metriky se dají použít k analýze využití služby úložiště, diagnostice problémů s požadavky provedenými na službu úložiště a ke zlepšení výkonu aplikací, které používají službu.

[Analýza úložiště Azure](/rest/api/storageservices/fileservices/storage-analytics) provádí protokolování a poskytuje data metrik pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů využití a diagnostikování problémů s účtem úložiště. K dispozici je Analýza úložiště protokolování pro [služby blob, Queue a Table](../../storage/common/storage-introduction.md). Analýza úložiště protokoluje podrobné informace o úspěšných a neúspěšných požadavcích na službu úložiště.

Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí. Položky protokolu jsou vytvořeny pouze v případě, že jsou zadány požadavky na koncový bod služby. Pokud například účet úložiště obsahuje aktivitu v koncovém bodu objektu blob, ale ne ve svých koncových bodech tabulky nebo fronty, vytvoří se jenom protokoly týkající se Blob service.

Chcete-li použít Analýza úložiště, je nutné ji povolit individuálně pro každou službu, kterou chcete monitorovat. Můžete ji povolit v [Azure Portal](https://portal.azure.com/). Podrobnosti najdete v tématu [monitorování účtu úložiště v Azure Portal](../../storage/common/manage-storage-analytics-logs.md). Analýza úložiště můžete také povolit programově prostřednictvím REST API nebo klientské knihovny. Pomocí operace set Service Properties (nastavit vlastnosti služby) Povolte Analýza úložiště pro každou službu zvlášť.

Agregovaná data se ukládají do známého objektu BLOB (pro protokolování) a v známých tabulkách (pro metriky), ke kterým může být přistupovaná pomocí Blob service a Table service rozhraní API.

Analýza úložiště má limit 20 TB pro množství uložených dat, která jsou nezávislá na celkovém limitu pro váš účet úložiště. Všechny protokoly se ukládají v objektech [blob bloku](../../storage/common/storage-analytics.md) v kontejneru s názvem $Logs, které se automaticky vytvoří, když je analýza úložiště povolený pro účet úložiště.

Následující akce prováděné Analýza úložiště jsou Fakturovatelné:

-   Požadavky na vytvoření objektů BLOB pro protokolování
-   Požadavky na vytvoření entit tabulky pro metriky.

> [!Note]
> Další informace o fakturaci a zásadách uchovávání dat najdete v tématu [Analýza úložiště a fakturace](/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Pro zajištění optimálního výkonu si přejete omezit počet vysoce využívaných disků připojených k virtuálnímu počítači, abyste zabránili možnému omezení. Pokud nepoužíváte vysokou velikost všech disků, může účet úložiště podporovat větší počet disků.

> [!Note]
> Další informace o limitech účtu úložiště najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](../../storage/common/scalability-targets-standard-account.md).


Jsou protokolovány následující typy ověřených a anonymních požadavků.

| Ověřuje  | Anonymní|
| :------------- | :-------------|
| Úspěšné požadavky | Úspěšné požadavky |
|Neúspěšné požadavky, včetně vypršení časového limitu, omezování, chyb sítě, selhání autorizace a dalších chyb | Žádosti s použitím sdíleného přístupového podpisu (SAS), včetně neúspěšných a úspěšných požadavků |
| Žádosti s použitím sdíleného přístupového podpisu (SAS), včetně neúspěšných a úspěšných požadavků |Chyby vypršení časového limitu u klientů i serveru |
|   Žádosti o analytická data |    Neúspěšné požadavky GET s kódem chyby 304 (Nezměněno) |
| Požadavky vytvořené Analýza úložiště samotné, například vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat je popsán v tématech [Analýza úložiště protokolovaných operací a stavové zprávy](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [Analýza úložiště formátu protokolu](/rest/api/storageservices/fileservices/storage-analytics-log-format) . | Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat je popsán v [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu analýza úložiště](/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD zahrnuje také úplnou sadu funkcí pro správu identit, včetně vícefaktorového ověřování, registrace zařízení, samoobslužné správy hesel, samoobslužné správy skupin, privilegovaných účtů, řízení přístupu na základě rolí, sledování využití aplikací, podrobného auditování a monitorování a upozorňování zabezpečení.

-   Zvyšte zabezpečení aplikací pomocí vícefaktorového ověřování Azure AD a podmíněného přístupu.

-   Sledujte využití aplikací a Chraňte své podnikání před pokročilými hrozbami pomocí sestav zabezpečení a monitorování.

Azure Active Directory (Azure AD) obsahuje různé sestavy zabezpečení, aktivit a auditu pro váš adresář. [Sestava Azure Active Directory audit](../../active-directory/reports-monitoring/overview-reports.md) pomáhá zákazníkům identifikovat privilegované akce, ke kterým došlo v jejich Azure Active Directory. Privilegované akce zahrnují změny zvýšení úrovně (například vytvoření role nebo resetování hesla), změny konfigurace zásad (například zásady hesel) nebo změny konfigurace adresáře (například změny nastavení federace domén).

Sestavy poskytují záznam auditu pro název události, objekt actor, který tuto akci provedl, cílový prostředek, který se změnil, a datum a čas (ve standardu UTC). Zákazníci mohou načíst seznam událostí auditu pro své Azure Active Directory prostřednictvím [Azure Portal](https://portal.azure.com/), jak je popsáno v tématu [zobrazení protokolů auditu](../../active-directory/reports-monitoring/overview-reports.md). Zde je seznam zahrnutých sestav:

| Sestavy zabezpečení  | Sestavy aktivit| Sestavy auditu |
| :------------- | :-------------| :-------------|
|Přihlášení z neznámých zdrojů | Využití aplikací: souhrn | Sestava auditu adresáře |
|Přihlášení po několika neúspěších | Využití aplikací: podrobnosti |   |
|Přihlášení z více geografických poloh | Řídicí panel aplikací |  |
|Přihlášení z IP adres s podezřelou aktivitou |Chyby zřizování účtů |  |
|Nestandardní přihlašovací aktivita |Zařízení jednotlivých uživatelů |  |
|Přihlášení z možných nakažených zařízení |Aktivity jednotlivých uživatelů |   |
|Uživatelé s neobvyklou přihlašovací aktivitou |Sestava aktivit skupin |   |
| |Sestava aktivit registrace resetování hesla |   |
| |Aktivity resetování hesla |   |



Data těchto sestav můžou být užitečná pro vaše aplikace, jako jsou SIEM systémy, audit a nástroje pro business intelligence. [Rozhraní API](../../active-directory/reports-monitoring/concept-reporting-api.md) pro vytváření sestav Azure AD poskytují programový přístup k datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z různých programovacích jazyků a nástrojů.

Události v sestavě auditu Azure AD se uchovávají po dobu 180 dnů.

> [!Note]
> Další informace o uchovávání sestav v sestavách najdete v tématu [Azure Active Directory zásady uchovávání sestav](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

Pro zákazníky, kteří mají zájem o ukládání svých [událostí auditu](../../active-directory/reports-monitoring/concept-audit-logs.md) pro delší dobu uchování, se dá rozhraní API pro vytváření sestav použít k pravidelnému vystavování událostí auditu do samostatného úložiště dat.

## <a name="summary"></a>Souhrn

V tomto článku se dozvíte, jak ochránit vaše osobní údaje a zabezpečovat vaše data, a přitom doručovat software a služby, které vám pomůžou se správou IT infrastruktury vaší organizace. Společnost Microsoft rozpoznává, že když pověří svá data jinými uživateli, je nutné, aby důvěryhodnost vyžadovala přísné zabezpečení. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb. Zabezpečení a ochrana dat je nejvyšší prioritou Microsoftu.

Tento článek vysvětluje

-   Způsob shromažďování, zpracování a zabezpečení dat v sadě Azure Monitor.

-   Rychle Analyzujte události v různých zdrojích dat. Identifikujte rizika zabezpečení a pochopte rozsah a dopad hrozeb a útoků, abyste zmírnili škodu narušení zabezpečení.

-   Identifikujte vzory útoků vizualizací odchozího škodlivého provozu IP a typů škodlivých hrozeb. Porozumět stavům zabezpečení celého prostředí bez ohledu na platformu.

-   Zachyťte všechna data protokolů a událostí požadovaná pro audit zabezpečení nebo dodržování předpisů. Lomítko čas a prostředky potřebné k poskytnutí auditu zabezpečení s úplným, prohledávatelným a exportovatelným protokolem a datovou sadou událostí.

<ul>
<li>Shromažďování událostí souvisejících se zabezpečením, auditem a analýzou nedodržení těchto prostředků:</li>
<ul>
<li>Postoj k zabezpečení</li>
<li>Významné problémy</li>
<li>Shrnutí hrozeb</li>
</ul>
</ul>

## <a name="next-steps"></a>Další kroky

- [Návrh a provozní zabezpečení](https://www.microsoft.com/trustcenter/security/designopsecurity)

Společnost Microsoft navrhuje své služby a software s ohledem na zabezpečení, které vám pomůžou zajistit odolnost cloudové infrastruktury odolné proti útokům.

- [Protokoly Azure Monitor | Dodržování předpisů & zabezpečení](https://www.microsoft.com/cloud-platform/security-and-compliance)

Pomocí dat a analýz zabezpečení Microsoftu můžete provádět inteligentní a efektivní detekci hrozeb.

- [Azure Security Center plánování a provozu](../../security-center/security-center-planning-and-operations-guide.md) Sada kroků a úloh, které můžete použít k optimalizaci používání Security Center na základě požadavků na zabezpečení a modelu správy cloudu ve vaší organizaci.