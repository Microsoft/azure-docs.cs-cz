---
title: Provozní zabezpečení Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o protokolech programu Microsoft Azure Monitor, jeho službách a o tom, jak funguje.
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
ms.openlocfilehash: 34c0c52945abc6e0ab74b1cb180581c76464bee8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749951"
---
# <a name="azure-operational-security"></a>Provozní zabezpečení Azure
## <a name="introduction"></a>Úvod

### <a name="overview"></a>Přehled
Víme, že zabezpečení je úloha jedna v cloudu a jak je důležité, abyste našli přesné a včasné informace o zabezpečení Azure. Jedním z nejlepších důvodů, proč používat Azure pro vaše aplikace a služby, je využít širokou škálu dostupných nástrojů a funkcí zabezpečení. Tyto nástroje a funkce umožňují vytvářet zabezpečená řešení na zabezpečené platformě Azure. Windows Azure musí poskytovat důvěrnost, integritu a dostupnost zákaznických dat a zároveň umožňovat transparentní odpovědnost.

Aby zákazníci lépe porozuměli řadě ovládacích prvků zabezpečení implementovaných v rámci Microsoft Azure z provozního hlediska zákazníka i Microsoftu, je napsána tato bílá kniha "Provozní zabezpečení Azure", která poskytuje komplexní Podívejte se na provozní zabezpečení, které je k dispozici ve Windows Azure.

### <a name="azure-platform"></a>Platforma Azure
Azure je platforma veřejných cloudových služeb, která podporuje široký výběr operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení. Může spouštět linuxové kontejnery s integrací Dockeru; vytvářet aplikace s JavaScriptem, Pythonem, .NET, PHP, Javou a Node.js; pro zařízení se systémy iOS, Android a Windows. Služba Azure Cloud podporuje stejné technologie, na které se miliony vývojářů a IT profesionálů už spoléhají a kterým důvěřujeme.

Když budete stavět na poskytovateli veřejných cloudových služeb nebo na něm migrovat, spoléháte se na schopnosti této organizace chránit vaše aplikace a data pomocí služeb a ovládacích prvků, které poskytují pro správu zabezpečení vašich cloudových prostředků.

Infrastruktura Azure je navržena od zařízení až po aplikace pro současné hostování milionů zákazníků a poskytuje důvěryhodný základ, na kterém mohou firmy splnit své požadavky na zabezpečení. Kromě toho vám Azure poskytuje širokou škálu konfigurovatelných možností zabezpečení a možnost jejich řízení, takže si můžete přizpůsobit zabezpečení tak, aby splňovalo jedinečné požadavky nasazení vaší organizace. Tento dokument vám pomůže pochopit, jak vám možnosti zabezpečení Azure můžou pomoci splnit tyto požadavky.

### <a name="abstract"></a>Shrnutí
Provozní zabezpečení Azure označuje služby, ovládací prvky a funkce, které jsou uživatelům k dispozici pro ochranu jejich dat, aplikací a dalších prostředků v Microsoft Azure. Provozní zabezpečení Azure je postaveno na rámci, které zahrnuje znalosti získané prostřednictvím různých funkcí, které jsou jedinečné pro Microsoft, včetně programu Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Center, a hluboké povědomí o prostředí kybernetických hrozeb.

Tato bílá kniha popisuje přístup Microsoftu k provoznímu zabezpečení Azure v rámci cloudové platformy Microsoft Azure a zahrnuje následující služby:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-intro.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Sledování sítě Azure](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Adresář Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Protokoly sledování Microsoft Azure

Protokoly programu Microsoft Azure Monitor jsou řešením správy IT pro hybridní cloud. Protokoly Azure Monitoru, které se používají samostatně nebo k rozšíření stávajícího nasazení System Center, poskytují maximální flexibilitu a kontrolu pro cloudovou správu vaší infrastruktury.

![Protokoly služby Azure Monitor](./media/operational-security/azure-operational-security-fig1.png)

Díky protokolům Azure Monitor u můžete spravovat libovolnou instanci v libovolném cloudu, včetně místních, Azure, AWS, Windows Serveru, Linuxu, VMware a OpenStack, a to za nižší cenu než konkurenční řešení. Protokoly Azure Monitoru, které byly navrženy pro svět, který je na prvním místě v cloudu, nabízí nový přístup ke správě vašeho podniku, který je nejrychlejším a nákladově nejefektivnějším způsobem, jak čelit novým obchodním výzvám a přizpůsobit se novým úlohám, aplikacím a cloudovým prostředím.

### <a name="azure-monitor-services"></a>Služby Azure Monitor

Základní funkce protokolů Azure Monitor uzajit sadu služeb, které běží v Azure. Každá služba poskytuje specifickou funkci správy a kombinováním služeb je možné dosahovat různých scénářů správy.

| Služba  | Popis|
| :------------- | :-------------|
| Protokoly služby Azure Monitor | Monitorování a analýza dostupnosti a výkonu různých prostředků včetně fyzických a virtuálních počítačů |
|Automatizace | Automatizace ručních procesů a vynucení konfigurací pro fyzické a virtuální počítače |
| Zálohování | Zálohovat a obnovovat důležitá data. |
| Site Recovery | Poskytnutí vysoké dostupnosti pro důležitá data |

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics) poskytuje monitorovací služby shromažďováním dat ze spravovaných prostředků do centrálního úložiště. Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export.


Tato metoda umožňuje konsolidovat data z různých zdrojů, takže můžete kombinovat data ze služeb Azure s vaším stávajícím místním prostředím. Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.


![Protokoly služby Azure Monitor](./media/operational-security/azure-operational-security-fig2.png)

Služba Azure Monitor spravuje vaše cloudová data bezpečně pomocí následujících metod:
-   oddělení dat
-   uchovávání dat
-   fyzické zabezpečení
-   správa incidentů
-   Dodržování
-   certifikace bezpečnostních norem

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) poskytuje služby zálohování a obnovení dat a je součástí sady produktů a služeb Azure Monitor.
Chrání data vaší aplikace a dlouhá léta je uchovává bez nutnosti velkých investic a s minimálními provozními náklady. Kromě úloh aplikací, jako je SQL Server a SharePoint, může zálohovat data z fyzických a virtuálních serverů Windows. Může ho také použít [Správce ochrany dat system center (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) k replikaci chráněných dat do Azure pro redundanci a dlouhodobé úložiště.


Chráněná data ve službě Azure Backup se ukládají do trezoru záloh umístěného v konkrétní geografické oblasti. Data jsou replikována v rámci stejné oblasti a v závislosti na typu úložiště mohou být také replikována do jiné oblasti pro další odolnost.

### <a name="management-solutions"></a>Řešení pro správu
[Azure Monitor](../../security-center/security-center-intro.md) je cloudové řešení pro správu IT od Microsoftu, které vám pomůže spravovat a chránit místní a cloudovou infrastrukturu.


[Řešení pro správu](../../monitoring/monitoring-solutions.md) jsou předem zabalené sady logik, které implementují konkrétní scénář správy pomocí jedné nebo více služeb Azure Monitor. Od Microsoftu a od partnerů jsou dostupná různá řešení, která můžete snadno přidat do předplatného Azure a zvýšit tak hodnotu vaší investice do Azure Monitoru. Jako partner můžete vytvářet vlastní řešení pro podporu aplikací a služeb a poskytovat je uživatelům prostřednictvím Azure Marketplace nebo šablon rychlého startu.


![Řešení pro správu](./media/operational-security/azure-operational-security-fig4.png)

Dobrým příkladem řešení, které používá více služeb k poskytování dalších funkcí, je [řešení správy aktualizací](../../automation/automation-update-management.md). Toto řešení používá agenta [protokolů Azure Monitor](../../log-analytics/log-analytics-queries.md) pro Windows a Linux ke shromažďování informací o požadovaných aktualizacích na každém agentovi. Zapíše tato data do úložiště protokolů Azure Monitor, kde je můžete analyzovat pomocí zahrnutého řídicího panelu.

Při vytváření nasazení se sady Runbook v [Azure Automation](../../automation/automation-intro.md) používají k instalaci požadovaných aktualizací. Celý tento proces spravujete na portálu a nemusíte se starat o podrobnosti.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomáhá chránit vaše prostředky Azure. Poskytuje integrované monitorování zabezpečení a správu zásad napříč vašimi předplatnými Azure. V rámci služby můžete definovat doposud nejen proti předplatným Azure, ale také proti [skupiny prostředků](../../azure-resource-manager/management/overview.md#resource-groups), takže můžete být podrobnější.

### <a name="security-policies-and-recommendations"></a>Zásady a doporučení zabezpečení

Zásady zabezpečení definují sadu ovládacích prvků doporučenou pro prostředky v rámci daného předplatného nebo skupiny prostředků.

V Security Center určíte zásady na základě toho, jaké má vaše společnost požadavky na zabezpečení, a podle typu aplikací nebo citlivosti dat.

![Zásady a doporučení zabezpečení](./media/operational-security/azure-operational-security-fig5.png)


Zásady, které jsou povoleny na úrovni předplatného, se automaticky rozšíří do všech skupin prostředků v rámci předplatného, jak je znázorněno v diagramu na pravé straně:


### <a name="data-collection"></a>Shromažďování dat

Security Center shromažďuje data z vašich virtuálních počítačů za účelem posouzení jejich stavu, poskytování doporučení zabezpečení a upozorňování na hrozby. Při prvním přístupu k Centru zabezpečení je povoleno shromažďování dat na všech virtuálních počítačích ve vašem předplatném. Shromažďování dat je doporučené, ale můžete je zrušit vypnutím shromažďování dat v rámci zásad služby Security Center.

### <a name="data-sources"></a>Zdroje dat

- Azure Security Center analyzuje data z následujících zdrojů a poskytuje přehled o stavu vašeho zabezpečení, zjišťuje ohrožení zabezpečení a doporučuje způsoby zmírnění rizik a detekuje aktivní hrozby:

-   Služby Azure: Využívá informace o konfiguraci služeb Azure, které máte nasazené, tím, že komunikuje s poskytovatelem prostředků pro příslušnou službu.

- Síťový provoz: Využívá vzorkovaná metadata síťového provozu z infrastruktury společnosti Microsoft, jako je třeba zdrojová a cílová IP adresa/port, velikost paketu nebo síťový protokol.

-   Partnerská řešení: Využívá výstrahy zabezpečení ze všech integrovaných partnerských řešení, jako jsou třeba brány firewall a antimalwarová řešení.

-   Virtuální počítače: Využívá z vašich virtuálních počítačů konfigurační informace a informace o událostech zabezpečení, jako jsou třeba protokoly událostí a auditů systému Windows, protokoly IIS, zprávy syslog a soubory se stavem systému.

### <a name="data-protection"></a>Ochrana dat

Služba Azure Security Center pomáhá zákazníkům předcházet hrozbám, detekovat je a reagovat na ně tím, že shromažďuje a zpracovává data související se zabezpečením, včetně informací o konfiguraci, metadat, protokolů událostí, souborů se stavem systému a dalších dat. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb.

-   **Oddělení dat**: Data se v rámci služby ukládají logicky oddělená pro jednotlivé komponenty. Všechna data jsou označená podle organizace. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby.

-   **Přístup k datům**: K poskytování doporučení zabezpečení a zkoumání potenciálních bezpečnostních hrozeb mohou pracovníci společnosti Microsoft přistupovat k informacím shromážděným nebo analyzovaným službami Azure, včetně souborů s výpisem stavu systému, událostí vytváření procesů, snímků disku virtuálního počítače a artefaktů, které mohou neúmyslně zahrnovat zákaznická data nebo osobní data z vašich virtuálních počítačů. Dodržujeme [podmínky a prohlášení o zásadách ochrany osobních údajů společnosti Microsoft pro online služby](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), které uvádějí, že společnost Microsoft nepoužívá zákaznická data ani z nich neodvozuje informace pro reklamní nebo podobné komerční účely.

-   **Použití dat**: Společnost Microsoft vylepšuje své schopnosti prevence a detekce pomocí schémat a analýzy hrozeb napříč několika klienty. Činíme tak v souladu se závazky k ochraně osobních údajů popsanými v našem [Prohlášení o zásadách ochrany osobních údajů](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Umístění dat

Azure Security Center shromažďuje dočasné kopie souborů se stavem systému a analyzuje je za účelem detekce stop pokusů o napadení zabezpečení, neúspěšných i úspěšných. Azure Security Center provádí tuto analýzu v rámci stejné geografie jako pracovní prostor a po dokončení analýzy tyto dočasné kopie odstraní. Artefakty počítačů se ukládají centrálně ve stejné oblasti jako virtuální počítač.

-   **Vaše účty úložiště**: Účet úložiště je určen pro každou oblast, kde jsou spuštěny virtuální počítače. To umožňuje ukládání dat v oblasti, kde se nachází virtuální počítač, ze kterého data jsou shromažďována.

-   **Azure Security Center Storage**: Informace o výstrahách zabezpečení, včetně partnerských výstrah, doporučení a stavu zabezpečení, se ukládají centrálně (v současnosti v USA). Tyto informace mohou podle potřeby zahrnovat související údaje o konfiguraci a událostech zabezpečení shromažďované z virtuálních počítačů, a to za účelem poskytování výstrah zabezpečení, doporučení nebo informací o stavu zabezpečení.


## <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor protokoly zabezpečení](../../security-center/security-center-monitoring.md) a auditování řešení umožňuje IT aktivně sledovat všechny prostředky, které mohou pomoci minimalizovat dopad bezpečnostních incidentů. Protokoly Azure Monitor zabezpečení a auditmají domény zabezpečení, které lze použít pro monitorování prostředků. Doména zabezpečení poskytuje rychlý přístup k možnostem, pro monitorování zabezpečení jsou popsány další podrobnosti:

-   Posouzení malwaru
-   Posouzení aktualizací
-   Identita a přístup.

Azure Monitor poskytuje odkazy na informace o konkrétních typech prostředků. Nabízí vizualizaci, dotaz, směrování, výstrahy, automatické škálování a automatizaci na datech jak z infrastruktury Azure (protokol aktivit), tak z každého jednotlivého prostředku Azure (diagnostické protokoly).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


Cloudové aplikace jsou složité s mnoha pohyblivými částmi. Monitorování poskytuje data k zajištění, že vaše aplikace zůstane v provozu v pořádku stavu. To také pomáhá odvrátit potenciální problémy nebo řešení minulých.

Kromě toho můžete použít data monitorování k získání podrobné přehledy o vaší aplikaci. Tyto znalosti vám mohou pomoci zlepšit výkon nebo udržovatelnost aplikací nebo automatizovat akce, které by jinak vyžadovaly ruční zásah.

### <a name="azure-activity-log"></a>Protokol aktivit Azure


Jedná se o protokol, který poskytuje přehled o operacích, které byly provedeny na prostředky ve vašem předplatném. Protokol aktivit byl dříve označován jako "Protokoly auditu" nebo "Provozní protokoly", protože hlásí události roviny řízení pro vaše předplatná.

![Protokol aktivit Azure](./media/operational-security/azure-operational-security-fig7.png)

Pomocí protokolu aktivit můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) převzaté z prostředků ve vašem předplatném. Můžete také pochopit stav operace a další relevantní vlastnosti. Protokol aktivit nezahrnuje operace čtení (GET) nebo operace pro prostředky, které používají klasický model.

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

Tyto protokoly jsou vydávány prostředku a poskytují bohaté, časté údaje o provozu tohoto prostředku. Obsah těchto protokolů se liší podle typu prostředku.

Protokoly systému událostí systému Windows jsou například jednou kategorií diagnostického protokolu pro virtuální servery a protokoly objektů blob, tabulky a fronty jsou kategorie diagnostických protokolů pro účty úložiště.

Protokoly diagnostiky se liší od [protokolu aktivit (dříve označované jako protokol auditu nebo provozní protokol)](../../azure-monitor/platform/platform-logs-overview.md). Protokol aktivit poskytuje přehled o operacích, které byly provedeny na prostředky ve vašem předplatném. Diagnostické protokoly poskytují přehled o operacích, které provedly samotné vaše prostředky.

### <a name="metrics"></a>Metriky

Azure Monitor umožňuje využívat telemetrická data, abyste získali přehled o výkonu a stavu vašich úloh v Azure. Nejdůležitějším typem dat telemetrie Azure jsou metriky (nazývané také čítače výkonu) vyzařované většinou prostředků Azure. Azure Monitor poskytuje několik způsobů, jak nakonfigurovat a využívat tyto [metriky](../../monitoring/monitoring-data-collection.md) pro monitorování a řešení potíží. Metriky jsou cenným zdrojem telemetrie a umožňují provádět následující úkoly:

-   **Sledujte výkon** prostředků (například virtuálního počítače, webu nebo aplikace logiky) vykreslením metrik na portálovém grafu a připnutím tohoto grafu k řídicímu panelu.

-   **Získejte upozornění na problém,** který má vliv na výkon vašeho prostředku, když metrika překročí určitou prahovou hodnotu.

-   **Nakonfigurujte automatické akce**, jako je automatické škálování prostředku nebo spuštění runbooku, když metrika překročí určitou prahovou hodnotu.

-   **Provádějte pokročilé analýzy** nebo vykazování trendů výkonu nebo využití vašeho zdroje.

-   **Archivujte** výkon nebo historii stavu prostředku pro účely dodržování předpisů nebo auditování.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Je to funkce v rámci Azure, která umožňuje shromažďování diagnostických dat na nasazené aplikaci. Můžete použít rozšíření diagnostiky z různých zdrojů. Aktuálně podporované jsou [webové a pracovní role Azure Cloud Service](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), virtuální počítače Azure [se](../../virtual-machines/windows/overview.md) systémem Microsoft Windows a [Service Fabric](../../azure-monitor/platform/diagnostics-extension-overview.md). Ostatní služby Azure mají vlastní samostatnou diagnostiku.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Auditování zabezpečení sítě je nezbytné pro zjišťování slabých míst sítě a zajištění souladu s vaším modelem zabezpečení IT a zásadováním regulace. Pomocí zobrazení Skupiny zabezpečení můžete načíst nakonfigurovanou skupinu zabezpečení sítě a pravidla zabezpečení a účinná pravidla zabezpečení. Pomocí seznamu použitých pravidel můžete určit otevřené porty a posoudit chybu zabezpečení sítě.

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni sítě v Azure, do a z Azure. Nástroje pro diagnostiku a vizualizaci sítě, které jsou k dispozici pomocí nástroje Sledování sítě, vám pomohou pochopit, diagnostikovat a získat přehled o vaší síti v Azure. Tato služba zahrnuje sběr paketů, další směrování, ověření toku IP, zobrazení skupiny zabezpečení, protokoly toku skupiny nsg. Monitorování úrovně scénáře poskytuje koncové zobrazení síťových prostředků na rozdíl od monitorování jednotlivých síťových prostředků.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

Sledování sítě má v současné době následující možnosti:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Protokoly auditu</a>**- Operace prováděné jako součást konfigurace sítí jsou protokolovány. Tyto protokoly si můžete prohlédnout na webu Azure Portal nebo je načíst pomocí nástrojů Microsoftu, jako je Power BI nebo nástroje třetích stran. Protokoly auditu jsou k dispozici prostřednictvím portálu, prostředí PowerShell, CLI a rozhraní Rest API. Další informace o protokolech auditu naleznete v tématu Audit operace se Správcem prostředků. Protokoly auditu jsou k dispozici pro operace provedené na všech síťových prostředcích.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Ověření toku IP</a>** - Zkontroluje, zda je paket povolen nebo odepřen na základě informací o toku 5 parametrů paketu n-tice (cílová IP adresa, zdrojová IP adresa, cílový port, zdrojový port a protokol). Pokud je paket skupinou zabezpečení sítě odmítnut, je vráceno pravidlo a skupina zabezpečení sítě, která paket odepřela.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Další směrování</a>** – určuje další směrování pro pakety směrované v Azure Network Fabric, což umožňuje diagnostikovat všechny nesprávně nakonfigurované uživatelem definované trasy.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Zobrazení skupiny zabezpečení</a>** – získá platná a použitá pravidla zabezpečení, která jsou použita na virtuálním počítači.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Protokolování toku nsg</a>** – protokoly toku pro skupiny zabezpečení sítě umožňují zachytit protokoly související s přenosy, které jsou povoleny nebo odepřeny pravidly zabezpečení ve skupině. Tok je definován informacemi o 5 řazených členech řaděných členů chau – zdrojová IP adresa, cílová adresa IP, zdrojový port, cílový port a protokol.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) můžete ukládat metriky, které zahrnují agregované statistiky transakcí a údaje o kapacitě žádostí do služby úložiště. Transakce jsou vykazovány na úrovni operace rozhraní API i na úrovni služby úložiště a kapacita je hlášena na úrovni služby úložiště. Data metrik lze použít k analýze využití služby úložiště, diagnostikovat problémy s požadavky na službu úložiště a zlepšit výkon aplikací, které používají službu.

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) provádí protokolování a poskytuje data metrik pro účet úložiště. Tato data můžete použít ke sledování požadavků, analýze trendů využití a diagnostice problémů s účtem úložiště. Protokolování Storage Analytics je k dispozici pro [služby Blob, Fronta a Table](../../storage/common/storage-introduction.md). Analýza úložiště protokoluje podrobné informace o úspěšných a neúspěšných požadavcích na službu úložiště.

Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí. Položky protokolu jsou vytvořeny pouze v případě, že jsou požadavky proti koncovému bodu služby. Například pokud účet úložiště má aktivitu v jeho koncovém bodě objektu blob, ale ne v jeho koncové body tabulky nebo fronty, jsou vytvořeny pouze protokoly týkající se služby objektu Blob.

Chcete-li používat Storage Analytics, musíte ji povolit individuálně pro každou službu, kterou chcete sledovat. Můžete ji povolit na [webu Azure Portal](https://portal.azure.com/); Podrobnosti najdete [v tématu Sledování účtu úložiště na webu Azure Portal](../../storage/common/storage-monitor-storage-account.md). Analýzu úložiště můžete také povolit programově prostřednictvím rozhraní REST API nebo klientské knihovny. Pomocí operace Nastavit vlastnosti služby povolte analýzu úložiště jednotlivě pro každou službu.

Agregovaná data jsou uložena ve známém objektu blob (pro protokolování) a ve známých tabulkách (pro metriky), ke kterým lze přistupovat pomocí řešení API služby Blob a table service.

Storage Analytics má limit 20 TB na množství uložených dat, které je nezávislé na celkovém limitu pro váš účet úložiště. Všechny protokoly jsou uloženy v [blobů bloku](../../storage/common/storage-analytics.md) v kontejneru s názvem $logs, které se automaticky vytvoří, když je storage Analytics povolena pro účet úložiště.

Následující akce prováděné službou Storage Analytics jsou fakturovatelné:

-   Požadavky na vytvoření objektů BLOB pro protokolování
-   Požadavky na vytvoření entit tabulky pro metriky.

> [!Note]
> Další informace o zásadách fakturace a uchovávání dat najdete v [tématu Analýza úložiště a fakturace](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Chcete-li dosáhnout optimálního výkonu, chcete omezit počet vysoce využitých disků připojených k virtuálnímu počítači, abyste se vyhnuli možnému omezení. Pokud všechny disky nejsou vysoce využívány ve stejnou dobu, účet úložiště může podporovat větší číslo disku.

> [!Note]
> Další informace o limitech účtů úložiště najdete v tématu [Škálovatelnost cíle pro účty standardní úložiště](../../storage/common/scalability-targets-standard-account.md).


Jsou protokolovány následující typy ověřených a anonymních požadavků.

| Ověřené  | Anonymní|
| :------------- | :-------------|
| Úspěšné požadavky | Úspěšné požadavky |
|Neúspěšné požadavky, včetně vypršení časového limitu, omezování, chyb sítě, selhání autorizace a dalších chyb | Požadavky pomocí sdíleného přístupového podpisu (SAS), včetně neúspěšných a úspěšných požadavků |
| Požadavky pomocí sdíleného přístupového podpisu (SAS), včetně neúspěšných a úspěšných požadavků |Chyby vypršení časového limitu u klientů i serveru |
|   Žádosti o analytická data |    Neúspěšné požadavky GET s kódem chyby 304 (Nezměněno) |
| Požadavky provedené samotnou službou Storage Analytics, jako je například vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat je zdokumentován v tématech [protokolovaných operací služby Storage Analytics a zpráv o stavu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a v [tématech formátu protokolu služby Storage Analytics.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) | Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat je zdokumentován ve formátu [protokolovaných operací služby Storage Analytics a zpráv o stavu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a ve formátu protokolu [analýzy úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD také obsahuje úplnou sadu funkcí správy identit, včetně vícefaktorového ověřování, registrace zařízení, samoobslužné správy hesel, samoobslužné správy skupin, správy privilegovaných účtů, přístupu založeného na rolích monitorování využití aplikací, bohaté auditování a monitorování a upozorňování na zabezpečení.

-   Zlepšete zabezpečení aplikací pomocí vícefaktorového ověřování Azure AD a podmíněného přístupu.

-   Sledujte využití aplikací a chraňte svou firmu před pokročilými hrozbami pomocí sestav a monitorování zabezpečení.

Azure Active Directory (Azure AD) obsahuje různé sestavy zabezpečení, aktivit a auditu pro váš adresář. [Sestava auditu služby Azure Active Directory](../../active-directory/active-directory-reporting-azure-portal.md) pomáhá zákazníkům identifikovat privilegované akce, ke kterým došlo v jejich službě Azure Active Directory. Privilegované akce zahrnují změny nadmořské výšky (například vytvoření role nebo obnovení hesla), změnu konfigurace zásad (například zásady hesel) nebo změny konfigurace adresáře (například změny nastavení federace domény).

Sestavy poskytují záznam auditu pro název události, objekt u actor, který provedl akci, cílový zdroj ovlivněnzměnou a datum a čas (v UTC). Zákazníci mohou načíst seznam událostí auditu pro svůj Azure Active Directory prostřednictvím [portálu Azure](https://portal.azure.com/), jak je popsáno v [části Zobrazení protokolů auditování](../../active-directory/reports-monitoring/overview-reports.md). Zde je seznam zahrnutých sestav:

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



Data těchto sestav mohou být užitečná pro vaše aplikace, jako jsou systémy SIEM, audit a nástroje business intelligence. Azure AD vytváření sestav [API](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) poskytují programový přístup k datům prostřednictvím sady api založených na rest. Tato rozhraní API můžete volat z různých programovacích jazyků a nástrojů.

Události v sestavě auditování Azure AD jsou zachovány po dobu 180 dnů.

> [!Note]
> Další informace o uchovávání informací v sestavách najdete v tématu [Zásady uchovávání sestav služby Azure Active Directory](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

Pro zákazníky, kteří mají zájem o ukládání [událostí auditu](../../active-directory/active-directory-reporting-activity-audit-logs.md) pro delší dobu uchovávání, rozhraní API pro vytváření přehledů lze pravidelně vyžádat události auditu do samostatného úložiště dat.

## <a name="summary"></a>Souhrn

Tento článek shrnuje ochranu osobních údajů a zabezpečení vašich dat a současně poskytuje software a služby, které vám pomohou spravovat it infrastrukturu vaší organizace. Společnost Microsoft uznává, že když svá data svěří jiným osobám, vyžaduje tato důvěra přísné zabezpečení. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb. Zabezpečení a ochrana dat je nejvyšší prioritou společnosti Microsoft.

Tento článek vysvětluje

-   Jak se data shromažďují, zpracovávají a zabezpečují v sadě Azure Monitor.

-   Rychle analyzujte události ve více zdrojích dat. Identifikujte bezpečnostní rizika a pochopte rozsah a dopad hrozeb a útoků, abyste zmírnili poškození narušení zabezpečení.

-   Identifikujte vzory útoků vizualizací odchozího škodlivého přenosu IP adres a typů škodlivých hrozeb. Pochopte stav zabezpečení celého prostředí bez ohledu na platformu.

-   Zachyťte všechna data protokolu a událostí potřebná pro audit zabezpečení nebo dodržování předpisů. Zkrátit čas a prostředky potřebné k zadání auditu zabezpečení s kompletní, prohledávatelné a exportovatelné protokolu a události datové sady.

<ul>
<li>Shromažďujte události související se zabezpečením, auditujte a analýzu porušení zabezpečení, abyste bedlivě sledovali své prostředky:</li>
<ul>
<li>Stav zabezpečení</li>
<li>Pozoruhodný problém</li>
<li>Souhrny hrozeb</li>
</ul>
</ul>

## <a name="next-steps"></a>Další kroky

- [Návrh a provozní bezpečnost](https://www.microsoft.com/trustcenter/security/designopsecurity)

Společnost Microsoft navrhuje své služby a software s ohledem na zabezpečení, aby zajistila, že její cloudová infrastruktura je odolná a chráněná před útoky.

- [Protokoly Azure Monitor | Dodržování předpisů & zabezpečení](https://www.microsoft.com/cloud-platform/security-and-compliance)

Pomocí bezpečnostních dat a analýz společnosti Microsoft můžete provádět inteligentnější a efektivnější detekci hrozeb.

- [Plánování a provoz Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md) Sada kroků a úkolů, které můžete sledovat při optimalizaci používání Centra zabezpečení na základě požadavků na zabezpečení vaší organizace a modelu správy cloudu.

