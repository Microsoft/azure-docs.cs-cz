---
title: Provozní zabezpečení Azure | Dokumentace Microsoftu
description: Další informace o Microsoft Azure Log Analytics, jejích služeb a jak to funguje.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 2ae2ea14bc712563867f32b83eddbd9d4129ac1d
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409488"
---
# <a name="azure-operational-security"></a>Provozní zabezpečení Azure
## <a name="introduction"></a>Úvod

### <a name="overview"></a>Přehled
Víme, že je zabezpečení stavbách v cloudu a jak důležité je, že zjistíte přesné a aktuální informace o zabezpečení Azure. Jedním z nejlepších důvody pro použití Azure pro vaše aplikace a služby je využít širokou škálu nástrojů zabezpečení a možnosti, které jsou k dispozici. Tyto nástroje a možnosti nápovědy umožňují vytvářet bezpečná řešení na zabezpečené platformě Azure. Windows Azure musíte zadat důvěrnost, integritu a dostupnost zákaznických dat, a zároveň umožnit transparentní zodpovědnost.

Abychom zákazníkům lépe pochopili škálu kontrolních mechanismů pro zabezpečení implementovaná v rámci Microsoft Azure z obou zákazníka a Microsoft provozní perspektivy, tento dokument white paper, "Provozní zabezpečení Azure", je zapsán, která poskytuje komplexní Podívejte se na provozní zabezpečení přináší Windows Azure.

### <a name="azure-platform"></a>Platforma Azure
Azure je platforma služby veřejného cloudu, který podporuje širokou škálu operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení. Může probíhat kontejnery Linuxu s integrací Dockeru; Vytvářejte aplikace pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js, Tvořte back EndY pro iOS, Android a Windows zařízení. Cloudovou službu Azure podporuje technologie miliony vývojářů a IT profesionály už spoléhají a kterým důvěřují.

Při sestavení nebo migraci prostředků IT k, poskytovatele služeb veřejného cloudu jste závislí na schopnosti této organizace chránit vaše aplikace a data se službami a ovládací prvky poskytují ke správě zabezpečení vašich cloudových prostředků.

Infrastruktura Azure je navržená ze zařízení do aplikace pro hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro na kterém můžou podniky vyhovuje jejich požadavkům na zabezpečení. Azure navíc poskytuje širokou škálu možností konfigurovatelné zabezpečení a umožňuje jim tak, aby zabezpečení jedinečným požadavkům vaší organizace nasazení můžete přizpůsobit. Tento dokument pomáhá pochopit, jak Azure security možnosti můžete splnit tyto požadavky.

### <a name="abstract"></a>Abstraktní
Azure Operational Security odkazuje na služby, ovládací prvky a funkce, které jsou dostupné pro uživatele k ochraně svých dat, aplikací a dalších prostředků ve službě Microsoft Azure. Provozní zabezpečení Azure je založen na rozhraní, která zahrnuje poznatky získané při různé schopnosti, které jsou jedinečné pro Microsoftu, mezi které patří Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Center a deep povědomí o zabezpečení.

Tento dokument white paper popisuje přístup Microsoftu k provozní zabezpečení Azure v rámci cloudové platformě Microsoft Azure a zahrnuje následující služby:
1.  [Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-azure-log-analytics"></a>Microsoft Azure Log Analytics

Microsoft Azure Log Analytics je řešení pro správu IT pro hybridní cloud. Používat samostatně nebo rozšířit existující nasazení nástroje System Center Log Analytics poskytuje maximální flexibilitu a kontrolu pro cloudovou správu infrastruktury.

![Log Analytics](./media/azure-operational-security/azure-operational-security-fig1.png)

Pomocí služby Log Analytics můžete spravovat všechny instance v libovolném cloudu, včetně místních, Azure, AWS, Windows Server, Linux, VMware a OpenStack, s nižšími náklady než konkurenční řešení. Určená pro cloudového světa, Log Analytics nabízí nový přístup ke správě vaší organizace, která je cenově nejvýhodnější, nejrychlejší způsob, jak nové obchodní výzvy a zvládnutí nových úloh, aplikací a cloudových prostředí.

### <a name="log-analytics-services"></a>Služby log Analytics

Základní funkce služby Log Analytics poskytuje sadu služeb, které běží v Azure. Každá služba poskytuje specifickou funkci správy a kombinováním služeb je možné dosahovat různých scénářů správy.

| Služba  | Popis|
| :------------- | :-------------|
| Log Analytics | Monitorování a analýza dostupnosti a výkonu různých prostředků včetně fyzických a virtuálních počítačů |
|Automation | Automatizace ručních procesů a vynucení konfigurací pro fyzické a virtuální počítače |
| Backup | Zálohování a obnovení kritických dat. |
| Site Recovery | Poskytnutí vysoké dostupnosti pro důležitá data |

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) zajišťuje služby monitorování získáváním dat ze spravovaných prostředků do centrálního úložiště. Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export.


Tato metoda umožňuje konsolidovat data z různých zdrojů, takže můžete kombinovat data ze služeb Azure s vaší stávající místní prostředí. Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

Služba Log Analytics spravuje vaše data založené na cloudu bezpečně pomocí následujících metod:
-   oddělení dat
-   uchovávání dat
-   fyzické zabezpečení
-   Správa incidentů
-   dodržování předpisů
-   certifikace standardů zabezpečení

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](http://azure.microsoft.com/documentation/services/backup) poskytuje data zálohování a obnovení služby a je součástí sady Log Analytics produktů a služeb.
Chrání data vaší aplikace a dlouhá léta je uchovává bez nutnosti velkých investic a s minimálními provozními náklady. To můžete zálohovat data z fyzických a virtuálních serverů Windows kromě úloh aplikací, jako je SQL Server a SharePoint. Je také možné pomocí [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) k replikaci chráněných dat do Azure pro redundance a dlouhodobého úložiště.


Chráněná data ve službě Azure Backup se ukládají do trezoru záloh umístěného v konkrétní geografické oblasti. Data se replikují v rámci stejné oblasti a v závislosti na typu trezoru se můžou replikovat také do jiné oblasti pro zajištění vyšší odolnosti.

### <a name="management-solutions"></a>Řešení pro správu
[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) je společnosti Microsoft cloudové řešení pro správu IT, která pomáhá spravovat a chránit místní a cloudovou infrastrukturu.


[Řešení pro správu](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) jsou předpřipravené sady logics, které implementují konkrétní scénáře správy s použitím nejméně jedna služba Log Analytics. Jiná řešení jsou k dispozici od Microsoftu a partnerů můžete snadno přidat do předplatného Azure ke zvýšení hodnoty investice do Log Analytics. Jako partner můžete vytvořit svoje vlastní řešení pro podporu vašich aplikací a služeb a poskytnout je uživatelům prostřednictvím Azure Marketplace nebo šablon pro rychlý Start.


![Řešení pro správu](./media/azure-operational-security/azure-operational-security-fig4.png)

Dobrým příkladem řešení, které používá více služeb k poskytnutí dalších funkcí je [řešení Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Toto řešení používá [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) agenta pro Windows a Linux ke shromažďování informací o požadovaných aktualizací na každém agentovi. Zapíše tato data do úložiště Log Analytics, kde je můžete analyzovat pomocí obsaženého řídicího panelu.

Když vytvoříte nasazení s runbooky v [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) se používají k instalaci požadovaných aktualizací. Celý tento proces spravujete na portálu a nemusíte se starat o podrobnosti.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center k ochraně vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure. V rámci služby, budete moct definovat zásady nejen pro svá předplatná Azure, ale také vůči [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), takže může být podrobnější.

### <a name="security-policies-and-recommendations"></a>Zásady a doporučení zabezpečení

Zásady zabezpečení definují sadu ovládacích prvků doporučenou pro prostředky v rámci daného předplatného nebo skupiny prostředků.

V Security Center určíte zásady na základě toho, jaké má vaše společnost požadavky na zabezpečení, a podle typu aplikací nebo citlivosti dat.

![Zásady a doporučení zabezpečení](./media/azure-operational-security/azure-operational-security-fig5.png)


Zásady povolené na úrovni předplatného automaticky rozšíří do všech skupin prostředků v rámci tohoto předplatného, jak je znázorněno na diagramu na pravé straně:


### <a name="data-collection"></a>Shromažďování dat

Security Center shromažďuje data z vašich virtuálních počítačů za účelem posouzení jejich stavu, poskytování doporučení zabezpečení a upozorňování na hrozby. Při první přístup Security Center, shromažďování dat zapnutá pro všechny virtuální počítače v rámci vašeho předplatného. Shromažďování dat je doporučené, ale můžete je zrušit vypnutím shromažďování dat v rámci zásad služby Security Center.

### <a name="data-sources"></a>Zdroje dat

- Azure Security Center analyzuje data z následujících zdrojů a poskytuje přehled o stavu vašeho zabezpečení, zjišťuje ohrožení zabezpečení a doporučuje způsoby zmírnění rizik a detekuje aktivní hrozby:

-   Služby Azure: Využívá informace o konfiguraci služeb Azure, které máte nasazené, tím, že komunikuje s poskytovatelem prostředků pro příslušnou službu.

- Síťový provoz: Využívá vzorkovaná metadata síťového provozu z infrastruktury společnosti Microsoft, jako je třeba zdrojová a cílová IP adresa/port, velikost paketu nebo síťový protokol.

-   Partnerská řešení: Využívá výstrahy zabezpečení ze všech integrovaných partnerských řešení, jako jsou třeba brány firewall a antimalwarová řešení.

-   Virtuální počítače: Využívá z vašich virtuálních počítačů konfigurační informace a informace o událostech zabezpečení, jako jsou třeba protokoly událostí a auditů systému Windows, protokoly IIS, zprávy syslog a soubory se stavem systému.

### <a name="data-protection"></a>Ochrana dat

Služba Azure Security Center pomáhá zákazníkům předcházet hrozbám, detekovat je a reagovat na ně tím, že shromažďuje a zpracovává data související se zabezpečením, včetně informací o konfiguraci, metadat, protokolů událostí, souborů se stavem systému a dalších dat. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb.

-   **Oddělení dat**: Data se v rámci služby ukládají logicky oddělená pro jednotlivé komponenty. Všechna data jsou označená podle organizace. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby.

-   **Přístup k datům**: K poskytování doporučení zabezpečení a prošetřovat potenciální ohrožení zabezpečení, mají pracovníci společnosti Microsoft přístup k informacím o shromážděných nebo analyzovaným službami Azure, včetně souborů se stavem systému, zpracování události vytváření disku virtuálního počítače snímky a artefakty, které mohou náhodně zahrnovat zákaznických dat ani osobní data z vašich virtuálních počítačů. Dodržujeme [Microsoft Online Services podmínky a prohlášení o zásadách](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), která stanoví, že společnost Microsoft použije zákaznická Data nebo získávání informací pro reklamní nebo podobné obchodní účely.

-   **Použití dat**: Společnost Microsoft vylepšuje své schopnosti prevence a detekce pomocí schémat a analýzy hrozeb napříč několika klienty. Činíme tak v souladu se závazky k ochraně osobních údajů popsanými v našem [Prohlášení o zásadách ochrany osobních údajů](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Umístění dat

Azure Security Center shromažďuje dočasné kopie souborů se stavem systému a analyzuje je za účelem detekce stop pokusů o napadení zabezpečení, neúspěšných i úspěšných. Azure Security Center provádí tuto analýzu v rámci stejné geografie jako pracovní prostor a po dokončení analýzy tyto dočasné kopie odstraní. Artefakty počítačů se ukládají centrálně ve stejné oblasti jako virtuální počítač.

-   **Vaše účty úložiště**: pro každou oblast, ve kterém jsou spuštěné virtuální počítače je určen účet úložiště. To umožňuje ukládání dat v oblasti, kde se nachází virtuální počítač, ze kterého data jsou shromažďována.

-   **Azure Security Center Storage**: Informace o výstrahách zabezpečení, včetně partnerských výstrah, doporučení a stavu zabezpečení, se ukládají centrálně (v současnosti v USA). Tyto informace mohou podle potřeby zahrnovat související údaje o konfiguraci a událostech zabezpečení shromažďované z virtuálních počítačů, a to za účelem poskytování výstrah zabezpečení, doporučení nebo informací o stavu zabezpečení.


## <a name="azure-monitor"></a>Azure Monitor

[Log Analytics zabezpečení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) a auditovat řešení umožňují IT aktivně monitoruje všechny prostředky, které může pomoci minimalizovat dopad incidentů zabezpečení. Log Analytics zabezpečení a Audit mít domény zabezpečení, které lze použít pro monitorování prostředků. Domény zabezpečení poskytuje rychlý přístup k možnostem, monitorování zabezpečení z následujících domén se věnují další podrobnosti:

-   Posouzení malwaru
-   Posouzení aktualizací
-   Identita a přístup.

Platforma Azure Monitor poskytuje odkazy na informace na konkrétní typy prostředků. Nabízí vizualizace, dotazování, směrování, výstrahy, automatické škálování a automatizace na data jak z infrastruktury Azure (protokol aktivit) a jednotlivé prostředky Azure (diagnostické protokoly).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Cloudové aplikace jsou složité s řadu pohyblivých částí. Monitorování poskytuje data k zajištění, že systém zůstane vaší aplikace v provozu a spuštěná v dobrém stavu. Také pomáhá stave vypnout potenciální problémy a řešení potíží s poslední těch, které jsou.

Kromě toho můžete data monitorování získat podrobný přehled o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

### <a name="azure-activity-log"></a>Protokol aktivit Azure


Je protokol, který poskytuje podrobné informace o operacích provedených na prostředky ve vašem předplatném. Protokol aktivit se dřív označovalo jako "Protokolů auditu" nebo "Provozní protokoly,", protože hlásí události rovina řízení pro vaše předplatná.

![Protokol aktivit Azure](./media/azure-operational-security/azure-operational-security-fig7.png)

Použití protokolu aktivit, můžete určit "co, kdo a kdy" veškerých operací (PUT, POST, DELETE) provedených ve prostředků ve vašem předplatném zápisu. Můžete také zjištění stavu operace a další relevantní vlastnosti. Protokol aktivit neobsahuje operace čtení (GET) ani operace pro prostředky, které používají Classic model.

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

Tyto protokoly jsou emitovány prostředek a poskytují bohaté a časté informace o fungování tohoto prostředku. Obsah tyto protokoly se liší podle typu prostředku.

Například protokoly událostí systému Windows jsou jednu kategorii protokol diagnostiky pro virtuální počítače a objektů blob, tabulky a fronty protokoly jsou kategorie diagnostické protokoly pro účty úložiště.

Diagnostické protokoly se liší od [protokolu aktivit (dříve označovaných jako operační protokol protokolu auditu)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Protokol aktivit poskytuje podrobné informace o operacích provedených na prostředky ve vašem předplatném. Diagnostické protokoly poskytují přehled o operacích, které provedly samotné vaše prostředky.

### <a name="metrics"></a>Metriky

Azure Monitor umožňuje využívat telemetrii s cílem získat přehled o výkonu a stavu vašich úloh v Azure. Metriky (také nazývané čítače výkonu) většinu služeb Azure prostředky emitovány je nejdůležitější typu Azure telemetrická data. Platforma Azure Monitor poskytuje několik způsobů, jak nakonfigurovat a používat [metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) pro monitorování a řešení potíží. Metriky jsou cenné zdroj telemetrie a umožňují provádět následující úlohy:

-   **Sledování výkonu** vašeho prostředku (například virtuálního počítače, web nebo logiky aplikace) bude vykreslení jeho metrik na portálu grafu a připnete graf na řídicí panel.

-   **Buďte informovaní o problém** , který má vliv na výkon vašeho prostředku, pokud metrika překročí určitou prahovou hodnotu.

-   **Konfigurace automatických akcí**, jako je automatické škálování prostředků nebo aktivaci sady runbook, pokud metrika překročí určitou prahovou hodnotu.

-   **Provádění pokročilých analýz** nebo vytváření sestav na trendy, výkon nebo využití prostředku.

-   **Archiv** historii výkon nebo stav prostředku pro dodržování předpisů a auditování.

### <a name="azure-diagnostics"></a>Diagnostika Azure

To je funkce v Azure, která umožňuje shromažďování diagnostických dat v nasazené aplikaci. Diagnostické rozšíření můžete použít z různých různých zdrojů. V tuto chvíli nepodporuje jsou [Azure Cloud Service Web a role pracovního procesu](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/overview) systémem Microsoft Windows a [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Další služby Azure mají své vlastní samostatný diagnostiky.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Auditování zabezpečení sítě je důležité pro zjištění ohrožení zabezpečení sítě a zajištění dodržování předpisů, zabezpečení IT a model dodržování zásad správného řízení. Pomocí zobrazení skupin zabezpečení můžete pak získat nakonfigurovanou skupinu zabezpečení sítě a pravidel zabezpečení a platná pravidla zabezpečení. Pomocí seznamu pravidla platila můžete určit porty, které jsou otevřené a posouzení ohrožení zabezpečení sítě.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového v, do a z Azure. Pro diagnostiku sítě a vizualizačních nástrojů, které jsou k dispozici pomocí služby Network Watcher pomáhají porozumět, diagnostice a získání přehledu o vaší síti v Azure. Tato služba zahrnuje zachycení paketu, další směrování, IP tok ověřit, zobrazení skupin zabezpečení, protokoly toků NSG. Scénář úrovně monitorování poskytuje komplexní přehled síťové prostředky, na rozdíl od sledování jednotlivých síťových prostředků.

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

Network Watcher je aktuálně má následující možnosti:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Protokoly auditu</a>**-operace provedené jako součást konfigurace sítě jsou protokolovány. Tyto protokoly můžete zobrazit na webu Azure Portal nebo pomocí nástrojů společnosti Microsoft, jako je Power BI nebo nástrojů třetích stran načíst. Protokoly auditu jsou k dispozici prostřednictvím portálu, Powershellu, rozhraní příkazového řádku a rozhraní Rest API. Další informace týkající se protokolů auditu najdete v tématu Audit operací pomocí Resource Manageru. Protokoly auditu jsou k dispozici pro operace provést pro všechny síťové prostředky.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP tok ověří </a>**  – zkontroluje, jestli je paket povolený nebo zakázaný na základě toku informace 5 řazené kolekce členů paketů parametrů (cílová IP adresa, Zdrojová IP adresa, cílový Port, zdrojový Port a protokol). Pokud paketu je skupina zabezpečení sítě, je vrácena pravidlo a skupinu zabezpečení sítě, které paket zamítlo.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Další směrování</a>**  -určí další segment pro pakety směrovány v prostředcích infrastruktury sítě Azure, umožňuje vám to zjistit všechny správně nakonfigurovaný. trasy definované uživatelem.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Zobrazení skupin zabezpečení</a>**  -získá pravidla efektivní a použité zabezpečení, které se použijí na virtuálním počítači.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Protokolování toku NSG Flow</a>**  – protokoly toků pro skupiny zabezpečení sítě umožňují zachytit protokoly týkající se přenosů, které jsou povolené nebo zakázané pravidla zabezpečení ve skupině. Tok je definována informace 5 řazené kolekce členů – Zdrojová IP adresa, cílová IP adresa, zdrojový Port, cílový Port a protokol.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Analýza úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) můžete ukládat metriky, včetně agregované transakce statistiky a dat o kapacitě požadavků na službu úložiště. Transakce jsou zaznamenány na úrovni operace rozhraní API a na úrovni služby úložiště, a je kapacita ohlášená na úrovni služby úložiště. Můžete například měřená data slouží k analýze využití služeb úložiště, Diagnostika potíží s požadavky na službu úložiště a zlepšit výkon aplikace, které používají službu.

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště. Je k dispozici pro Storage Analytics protokolování [služby objektů Blob, Queue a Table](https://docs.microsoft.com/azure/storage/storage-introduction). Analýza úložiště protokoluje podrobné informace o úspěšné i neúspěšné požadavky do služby úložiště.

Tyto informace slouží ke sledování jednotlivých požadavků a Diagnostikujte problémy pomocí služby storage. Žádosti o přihlášení snažíme maximálně vyhovět. Položky protokolu se vytvoří pouze v případě, že jsou požadavky na koncový bod služby. Například pokud účet úložiště je v její koncový bod služby Blob, ale ne v jeho tabulek nebo front koncové body, pouze protokoly se vytvoří vztahující se na službu Blob service.

Chcete-li použít funkci analýzy úložiště, musíte povolit ji jednotlivě pro každou službu, kterou chcete monitorovat. Můžete povolit v [webu Azure portal](https://portal.azure.com/); podrobnosti najdete v tématu [monitorování účtu úložiště na webu Azure Portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Můžete také povolit Storage Analytics prostřednictvím kódu programu přes rozhraní REST API nebo knihovny klienta. Pomocí operace nastavit vlastnosti služby můžete povolit Storage Analytics jednotlivě pro každou službu.

Agregovaná data se ukládají v dobře známý objekt blob (pro protokolování) a dobře známých tabulek (pro metriky), které lze získat přístup pomocí služby Blob service a Table service rozhraní API.

Analýza úložiště má limit 20 TB na množství uložených dat, která je nezávislá celkový limit pro váš účet úložiště. Všechny protokoly se ukládají v [objekty BLOB bloku](https://docs.microsoft.com/azure/storage/storage-analytics) v kontejneru nazvaném $logs, které automaticky vytvářejí, když je pro účet úložiště povolená analytika úložiště.

Následující akce prováděné analytika úložiště se účtují:

-   Požadavky na vytvoření objektů blob pro protokolování
-   Požadavky na vytvoření entity tabulky pro metriky.

> [!Note]
> Další informace o fakturaci a zásad uchovávání dat najdete v tématu [analytika úložiště a fakturace](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Pro optimální výkon budete chtít omezit počet vysoce využívaných disků připojených k virtuálnímu počítači, abyste zabránili možnému omezení. Pokud všechny disky nejsou vysoce využívané ve stejnou dobu, může účet úložiště podporovat větší počet disků.

> [!Note]
> Další informace o omezení účtů úložiště najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Následující typy ověřený a anonymní žádosti jsou protokolovány.

| Ověřeno  | Anonymní|
| :------------- | :-------------|
| Úspěšné požadavky | Úspěšné požadavky |
|Neúspěšné požadavky, včetně vypršení časového limitu, omezování, sítě, autorizace a dalších chyb | Požadavky pomocí sdílený přístupový podpis (SAS), včetně úspěšné a neúspěšné požadavky |
| Požadavky pomocí sdílený přístupový podpis (SAS), včetně úspěšné a neúspěšné požadavky |Chyby časového limitu pro klienta a serveru |
|   Požadavky na analytických dat |    Neúspěšné požadavky GET s kódem chyby 304 (Neupraveno) |
| Storage Analytics, jako je protokol vytvoření nebo odstranění, žádosti se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolovanými operacemi](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [formát Log Analytics úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) témata. | Všechny ostatní neúspěšné anonymní žádosti se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolovanými operacemi](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [formát Log Analytics úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |
## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD také zahrnuje kompletní sadu možností pro správu identit včetně ověření službou Multi-Factor Authentication, registrace zařízení, Samoobslužná správa hesel, Samoobslužná správa skupin, správu privilegovaných účtů, přístup na základě rolí ovládací prvek, sledování využití aplikací, bohaté auditování a monitorování zabezpečení a upozorňování.

-   Zlepšení zabezpečení aplikací pomocí vícefaktorového ověřování Azure AD a podmíněného přístupu.

-   Monitorování využití aplikací a chránit vaši firmu před pokročilými hrozbami zabezpečení monitorování a vytváření sestav.

Azure Active Directory (Azure AD) obsahuje různé sestavy zabezpečení, aktivit a auditu pro váš adresář. [Sestavy auditování Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) pomáhá zákazníkům identifikovat privilegovaných akcí, ke kterým došlo v Azure Active Directory. Privilegované akce zahrnují změny zvýšení oprávnění (například role vytvoření nebo resetování hesla), změny konfigurace zásad (například zásady pro hesla) nebo změny v konfiguraci adresáře (například změny nastavení federace domény).

Sestavy poskytují záznam auditu pro název události objektu actor, který provedl akci, cílový prostředek měla vliv na změnu, datum a čas (ve standardu UTC). Zákazníci mají možnost načíst seznam událostí auditu, které pro Azure Active Directory prostřednictvím [webu Azure portal](https://portal.azure.com/), jak je popsáno v [zobrazit protokoly auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Zde je seznam zahrnutých sestav:

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
| |Aktivity resetování hesla |   | |



Data z těchto sestav může být užitečná pro vaše aplikace, jako jsou systémy SIEM, audit a nástroje business intelligence. Vytváření sestav Azure AD [rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) poskytují programový přístup k datům prostřednictvím sady založené na protokolu REST API. Tato rozhraní API můžete volat z různé programovací jazyky a nástroje.

Události sestavy auditování Azure AD se uchovávají po dobu 180 dnů.

> [!Note]
> Další informace o uchovávání dat v sestavách, naleznete v tématu [zásady uchování sestav Azure ve službě Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Pro zákazníky, kteří chtějí ukládání jejich [události auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) delší dobu uchování, API pro generování sestav je možné pravidelně do samostatného úložiště dat. o přijetí změn událostí auditu.

## <a name="summary"></a>Souhrn

Tento článek souhrny, ochraně osobních údajů a zabezpečení vašich dat při dodávání softwaru a služeb, které vám pomůžou spravovat infrastrukturu IT vaší organizace. Společnost Microsoft si uvědomuje, že když svěřují svá data na ostatní, tato důvěra vyžaduje striktní bezpečnostní. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb. Zabezpečení a ochrana dat je hlavní prioritou společnosti Microsoft.

Tento článek vysvětluje

-   Jak data shromažďují, zpracování a zabezpečené v Log Analytics suite.

-   Rychlá analýza událostí z různých zdrojů dat Identifikace bezpečnostních rizik a pochopení rozsahu a dopadů hrozeb a útoků s cílem omezit škody vzniklé narušením bezpečnosti

-   Identifikace vzorů útoků vizualizací odchozího škodlivého síťového provozu a typů bezpečnostních hrozeb Přehled o stavu zabezpečení celého prostředí bez ohledu na platformu.

-   Zachycení všech protokolů a událostí data požadovaná pro audit zabezpečení nebo dodržování předpisů. Lomítko, čas a prostředky potřebné k poskytování auditu zabezpečení díky úplným, prohledávatelným a exportovatelným protokolů a událostí datové sady.

<ul>
<li>Shromážděte události související se zabezpečením, audit a analýzy porušení zabezpečení pečlivě sledovat vaše prostředky:</li>
<ul>
<li>Stav zabezpečení</li>
<li>Významný problém</li>
<li>Shrnutí hrozby</li>
</ul>
</ul>

## <a name="next-steps"></a>Další kroky

- [Celkové řešení a provozní zabezpečení](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft navrhuje svůj služeb a softwaru zabezpečení k zajištění, že své cloudové infrastruktury je odolné a před útoky.

- [Log Analytics | Zabezpečení a dodržování předpisů](https://www.microsoft.com/cloud-platform/security-and-compliance)

Pomocí Microsoft zabezpečení dat a analýza provádět inteligentní a efektivní detekce hrozeb.

- [Azure Security Center plánováním a provozem](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) sadu kroků a úloh, které můžete provést, abyste přizpůsobili použití služby Security Center na základě požadavků na zabezpečení vaší organizace a modelu správy cloudu.

