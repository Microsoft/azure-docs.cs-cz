---
title: Zabezpečení provozu Azure | Microsoft Docs
description: Další informace o Microsoft Operations Management Suite (OMS), jeho služby a jak to funguje.
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
ms.openlocfilehash: d5bea758a0b2a950f51ec443707d9d32dee9cbc4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-operational-security"></a>Provozní zabezpečení Azure
## <a name="introduction"></a>Úvod

### <a name="overview"></a>Přehled
Víme, že je zabezpečení úlohy, jeden v cloudu a jak důležité je, že zjistíte přesné a aktuální informace o zabezpečení Azure. Jedním z nejlepší důvodů, proč používat Azure pro vaše aplikace a služby je využít výhod širokou škálu zabezpečení nástroje a možnosti, které jsou k dispozici. Tyto nástroje a možnosti pomáhat ji možné vytvořit zabezpečený řešení na zabezpečené platformy Azure. Windows Azure, musíte zadat utajení, integrita a dostupnost dat zákazníka, a zároveň umožnit transparentní odpovědnosti za.

Chcete-li pomoc zákazníkům lépe pochopit implementovat řadu ovládacích prvků zabezpečení v rámci Microsoft Azure z obou zákazníka a Microsoft provozní perspektivy, tento dokument white paper, "Provozní zabezpečení Azure", který je zapsán poskytuje komplexní Podívejte se na provozní zabezpečení, které jsou k dispozici v systému Windows Azure.

### <a name="azure-platform"></a>Platformy Azure
Azure je platforma služby veřejného cloudu, která podporuje široký výběr operačních systémů, programovací jazyky, rozhraní, nástroje, databází a zařízení. Může probíhat Linux kontejnery s integrace Dockeru; vývoj aplikací pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js; sestavení back EndY pro iOS, Android a Windows zařízení. Cloudovou službu Azure podporuje stejné technologie miliony vývojářů a IT profesionály již spoléhají na a vztah důvěryhodnosti.

Sestavení nebo migrovat aktiva IT, poskytovatele služeb veřejného cloudu se spoléhat na dané organizace dalo k ochraně vašich aplikací a dat se službami a ovládací prvky slouží ke správě zabezpečení vaše cloudové prostředky.

Infrastruktury Azure a k aplikacím pro hostování miliony zákazníků současně slouží ze zařízení a poskytuje trustworthy foundation, na kterém může podnikům splňovat požadavky jejich zabezpečení. Azure navíc poskytuje širokou škálu možností konfigurovat zabezpečení a umožňuje řídit tak, aby zabezpečení ke splnění jedinečným požadavkům vaší organizace nasazení můžete přizpůsobit. Tento dokument se pomůže pochopit, jak Azure zabezpečení funkce vám může pomoct splnění těchto požadavků.

### <a name="abstract"></a>Abstraktní
Zabezpečení provozu Azure se odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svá data, aplikace a dalších prostředků ve službě Microsoft Azure. Zabezpečení provozu Azure je založený na rozhraní, které zahrnuje poznatky získané při různých možnostech, které jsou jedinečné pro společnosti Microsoft, včetně Microsoft SDL Security Development Lifecycle (), programu Microsoft Security Response Center a hloubkové povědomí o povahu hrozeb počítačové bezpečnosti.

Tento dokument popisuje přístup společnosti Microsoft k zabezpečení provozu Azure v rámci Cloudová platforma Microsoft Azure a obsahuje následující služby:
1.  [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite

Microsoft Operations Management Suite (OMS) je řešení pro správu IT pro hybridní cloud. Použitá samostatně nebo rozšířit existující nasazení produktu System Center, OMS vám dává flexibilní a řízení pro správu cloudové infrastruktury.

![Microsoft Operations Management Suite](./media/azure-operational-security/azure-operational-security-fig1.png)

S OMS můžete spravovat libovolnou instancí ve všech cloudu, včetně místní, Azure, AWS, Windows Server, Linux, VMware a OpenStack, při nižších nákladech, než konkurenční řešení. Vytvořené pro první cloudu world, OMS nabízí nové přístup ke správě vaší organizace tedy nejrychlejší a nákladově nejefektivnější způsob přizpůsobení nové úlohy, aplikace a cloudové prostředí a splňují nové obchodní příležitosti.

### <a name="oms-services"></a>Služby OMS

Základní funkce OMS poskytuje sada služeb, které běží v Azure. Každá služba poskytuje specifickou funkci správy a kombinováním služeb je možné dosahovat různých scénářů správy.

| Služba  | Popis|
| :------------- | :-------------|
| Log Analytics | Monitorování a analýza dostupnosti a výkonu různých prostředků včetně fyzických a virtuálních počítačů |
|Automation | Automatizace ručních procesů a vynucení konfigurací pro fyzické a virtuální počítače |
| Backup | Zálohování a obnovení důležitá data. |
| Site Recovery | Poskytnutí vysoké dostupnosti pro důležitá data |

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) poskytuje služby monitorování pro OMS získáváním dat ze spravovaných prostředků do centrálního úložiště. Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export.


Tato metoda umožňuje konsolidovat z různých zdrojů dat, takže můžete kombinovat data ze služeb Azure s vaší stávající místní prostředí. Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

Službu analýzy protokolů bezpečně spravuje vaše data založená na cloudu pomocí následujících metod:
-   Oddělení dat
-   uchovávání dat
-   Fyzické zabezpečení
-   Správa incidentů
-   Dodržování předpisů
-   certifikace standardy zabezpečení

### <a name="azure-backup"></a>Azure Backup

[Zálohování Azure](http://azure.microsoft.com/documentation/services/backup) poskytuje data zálohování a obnovení služby a je součástí sady OMS produktů a služeb.
Chrání data vaší aplikace a dlouhá léta je uchovává bez nutnosti velkých investic a s minimálními provozními náklady. Můžete zálohovat data ze fyzickými a virtuálními serverů Windows kromě zatížení aplikace jako SQL Server a SharePoint. Můžete použít také pomocí [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) replikace chráněných dat do Azure redundanci a dlouhodobé uložení.


Chráněná data ve službě Azure Backup se ukládají do trezoru záloh umístěného v konkrétní geografické oblasti. Data se replikují ve stejné oblasti a v závislosti na typu trezoru, může být také replikují do jiné oblasti pro další odolnost proti chybám.

### <a name="management-solutions"></a>Řešení pro správu
[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) je společnosti Microsoft založená na cloudu IT řešení správy, které pomáhá spravovat a chránit místní a cloudové infrastruktury.


[Řešení pro správu](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) jsou hotových sady logics, které implementují scénáři konkrétní správy pomocí jedné nebo více služeb OMS. Jsou dostupná různá řešení od Microsoftu a partnerů, která můžete snadno přidat do předplatného Azure ke zvýšení hodnoty investice do OMS. Jako partner můžete vytvořit vlastní řešení pro podporu aplikací a služeb a poskytněte uživatelům prostřednictvím Azure Marketplace nebo šablony rychlý Start.


![Řešení pro správu](./media/azure-operational-security/azure-operational-security-fig4.png)

Dobrým příkladem řešení, které využívá více služeb dodatečných funkcí je [řešení pro správu aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Toto řešení používá [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) agenta pro Windows a Linux shromáždit informace o požadovaných aktualizací každého agenta. Zapíše tato data do úložiště Log Analytics, kde je můžete analyzovat pomocí obsaženého řídicího panelu.

Při vytváření nasazení sady runbook v [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) slouží k instalaci požadovaných aktualizací. Celý tento proces spravujete na portálu a nemusíte se starat o podrobnosti.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomáhá chránit prostředky v Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure. V rámci služby, budete moci definovat zásady nejen pro svá předplatná Azure, ale i proti [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), aby mohli být podrobnější.

### <a name="security-policies-and-recommendations"></a>Zásady a doporučení zabezpečení

Zásady zabezpečení definují sadu ovládacích prvků doporučenou pro prostředky v rámci daného předplatného nebo skupiny prostředků.

V Security Center určíte zásady na základě toho, jaké má vaše společnost požadavky na zabezpečení, a podle typu aplikací nebo citlivosti dat.

![Zásady a doporučení zabezpečení](./media/azure-operational-security/azure-operational-security-fig5.png)


Zásady, které jsou povolené na úrovni předplatného automaticky rozšíří do všech skupin prostředků v rámci předplatného, jak je vidět v diagramu na pravé straně:


### <a name="data-collection"></a>Shromažďování dat

Security Center shromažďuje data z vašich virtuálních počítačů za účelem posouzení jejich stavu, poskytování doporučení zabezpečení a upozorňování na hrozby. Pokud je povolen vaše první přístup Security Center, shromažďování dat na všech virtuálních počítačích v rámci vašeho předplatného. Shromažďování dat je doporučené, ale můžete je zrušit vypnutím shromažďování dat v rámci zásad služby Security Center.

### <a name="data-sources"></a>Zdroje dat

- Azure Security Center analyzuje data z následujících zdrojů a poskytuje přehled o stavu vašeho zabezpečení, zjišťuje ohrožení zabezpečení a doporučuje způsoby zmírnění rizik a detekuje aktivní hrozby:

-   Služby Azure: Využívá informace o konfiguraci služeb Azure, které máte nasazené, tím, že komunikuje s poskytovatelem prostředků pro příslušnou službu.

- Síťový provoz: Využívá vzorkovaná metadata síťového provozu z infrastruktury společnosti Microsoft, jako je třeba zdrojová a cílová IP adresa/port, velikost paketu nebo síťový protokol.

-   Partnerská řešení: Využívá výstrahy zabezpečení ze všech integrovaných partnerských řešení, jako jsou třeba brány firewall a antimalwarová řešení.

-   Virtuální počítače: Využívá z vašich virtuálních počítačů konfigurační informace a informace o událostech zabezpečení, jako jsou třeba protokoly událostí a auditů systému Windows, protokoly IIS, zprávy syslog a soubory se stavem systému.

### <a name="data-protection"></a>Ochrana dat

Služba Azure Security Center pomáhá zákazníkům předcházet hrozbám, detekovat je a reagovat na ně tím, že shromažďuje a zpracovává data související se zabezpečením, včetně informací o konfiguraci, metadat, protokolů událostí, souborů se stavem systému a dalších dat. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb.

-   **Oddělení dat**: Data se v rámci služby ukládají logicky oddělená pro jednotlivé komponenty. Všechna data jsou označená podle organizace. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby.

-   **Přístup k datům**: K poskytování doporučení zabezpečení a prozkoumat potenciální ohrožení zabezpečení, může Microsoft pracovníky přístup k informace shromážděné nebo analyzovat služby Azure, včetně soubory se stavem systému, zpracování událostí vytváření, disku virtuálního počítače snímky a artefaktů, které můžou neúmyslně obsahovat Data zákazníků nebo osobní data z virtuálních počítačů. Jsme dodržovat [Microsoft Online Services podmínky a prohlášení o ochraně osobních údajů](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), která stanoví, že společnost Microsoft není používá Data zákazníků nebo odvození informací z něj pro obchodní účely reklamy nebo podobné.

-   **Použití dat**: Společnost Microsoft vylepšuje své schopnosti prevence a detekce pomocí schémat a analýzy hrozeb napříč několika klienty. Činíme tak v souladu se závazky k ochraně osobních údajů popsanými v našem [Prohlášení o zásadách ochrany osobních údajů](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

### <a name="data-location"></a>Umístění dat

Azure Security Center shromažďuje dočasné kopie souborů se stavem systému a analyzuje je za účelem detekce stop pokusů o napadení zabezpečení, neúspěšných i úspěšných. Azure Security Center provádí tuto analýzu v rámci stejné geografie jako pracovní prostor a po dokončení analýzy tyto dočasné kopie odstraní. Artefakty počítačů se ukládají centrálně ve stejné oblasti jako virtuální počítač.

-   **Účty úložiště**: účet úložiště je zadán pro každou oblast, kde jsou virtuální počítače spuštěné. To umožňuje ukládání dat v oblasti, kde se nachází virtuální počítač, ze kterého data jsou shromažďována.

-   **Azure Security Center Storage**: Informace o výstrahách zabezpečení, včetně partnerských výstrah, doporučení a stavu zabezpečení, se ukládají centrálně (v současnosti v USA). Tyto informace mohou podle potřeby zahrnovat související údaje o konfiguraci a událostech zabezpečení shromažďované z virtuálních počítačů, a to za účelem poskytování výstrah zabezpečení, doporučení nebo informací o stavu zabezpečení.


## <a name="azure-monitor"></a>Azure Monitor

[OMS zabezpečení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) a auditování řešení umožňuje IT aktivně sledovat všechny prostředky, které může pomoci minimalizovat dopad incidentů, zabezpečení. OMS zabezpečení a Audit mít zabezpečení domény, které lze použít ke sledování prostředků. Doména zabezpečení poskytuje rychlý přístup k možnosti, pro sledování zabezpečení z následujících domén jsou popsané v další podrobnosti:

-   posouzením malwaru
-   Posouzení aktualizací
-   Identit a přístupu.

Monitorování Azure poskytuje odkazy na informace na konkrétní typy prostředků. Nabízí vizualizace, dotaz, směrování, výstrahy, automatické škálování a automatizace na datům umístěným jak infrastrukturu Azure (protokol aktivit) a každý jednotlivých prostředků Azure (diagnostických protokolů).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Cloudové aplikace jsou komplexní s mnoha přesunutí částmi. Monitorování poskytuje data a ujistěte se, že vaše aplikace zůstává nahoru a spuštěna v dobrém stavu. Také pomáhá stave vypnout potenciální problémy nebo vyřešit potíže s uplynulou těch, které jsou.

Kromě toho můžete data monitorování a získáte přehled o hloubkové o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

### <a name="azure-activity-log"></a>Protokol činnosti Azure


Je protokol, který poskytuje vhled do činnosti, které byly provedeny v prostředky ve vašem předplatném. Protokol aktivit se dřív označovala jako "Protokoly auditu" nebo "Provozní protokoly," vzhledem k tomu, že oznámí události rovině řízení pro vaše předplatné.

![Protokol činnosti Azure](./media/azure-operational-security/azure-operational-security-fig7.png)

Pomocí protokolu činnosti, můžete určit ', kdo a kdy se pro všechny zápisu operace (PUT, POST, DELETE) na prostředky v rámci vašeho předplatného. Můžete také chápou stav operace a další relevantní vlastnosti. Protokol aktivit nezahrnuje operace čtení (GET) nebo operace pro prostředky, které používají Classic model.

### <a name="azure-diagnostic-logs"></a>Azure diagnostických protokolů

Tyto protokoly jsou vygenerované prostředek a nabízí bohatou a často data o operaci prostředku. Obsah tyto protokoly se liší podle typu prostředku.

Například protokoly událostí systému Windows jsou jednu kategorii protokolů diagnostiky pro virtuální počítače a objektů blob, table a queue protokoly jsou kategorie diagnostické protokoly pro účty úložiště.

Diagnostické protokoly se liší od [protokol aktivit (dříve označované jako protokol auditů nebo operační protokol)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Protokol aktivit poskytuje náhled do činnosti, které byly provedeny v prostředky ve vašem předplatném. Diagnostické protokoly poskytují přehled o operacích, které provedly samotné vaše prostředky.

### <a name="metrics"></a>Metriky

Azure monitorování umožňuje využívat telemetrie a získáte přehled o výkonu a stavu úlohy v Azure. Nejdůležitější typ Azure telemetrická data je metriky (také nazývané čítače výkonu) vysílaných prostředků nejvíce Azure. Monitorování Azure poskytuje několik způsobů, jak nakonfigurovat a využívat tyto [metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) pro monitorování a řešení potíží. Metriky jsou cenné zdroj telemetrie a vám umožňují provádět následující úlohy:

-   **Sledovat výkon** vaše prostředku (například počítač, web nebo logiku aplikace) vykreslení jeho metriky na portálu graf a Připnutí tento graf na řídicí panel.

-   **Upozorňování problému** , ovlivňuje výkon prostředku, když metriky překračuje určitou mez.

-   **Konfigurovat automatické akce**, jako je automatické škálování prostředku nebo když metriky překračuje určité prahovou hodnotu, která iniciovala sady runbook.

-   **Provádět pokročilé analýzy** nebo generování sestav na trendy výkonu a využití vaší prostředku.

-   **Archiv** historii výkon nebo stav prostředku pro dodržování předpisů nebo účely auditování.

### <a name="azure-diagnostics"></a>Diagnostika Azure

Je funkce v rámci Azure, která umožňuje shromažďování diagnostických dat na nasazené aplikace. Můžete použít rozšíření diagnostiky z různých různých zdrojů. Aktuálně podporované jsou [webové služby Azure Cloud a rolí pracovního procesu](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/overview) systémem Microsoft Windows, a [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Jinými službami Azure mají své vlastní samostatné diagnostiky.

## <a name="azure-network-watcher"></a>Sledovací proces sítě Azure

Auditování zabezpečení sítě je důležité pro zjišťování chyb zabezpečení sítě a zajištění dodržování zabezpečení IT a modelu regulačních zásad správného řízení. Pomocí zobrazení skupiny zabezpečení můžete načíst nakonfigurovaná skupina zabezpečení sítě a pravidel zabezpečení a pravidla efektivní zabezpečení. Seznam pravidel, použít můžete určit porty, které jsou otevřené a vyhodnocení ohrožení zabezpečení sítě.

[Sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) je místní služba, která umožňuje sledovat a diagnostikovat podmínky na úrovni sítě v, do a z Azure. Diagnostika sítě a k dispozici sledovací proces sítě vizualizace nástroje vám pomůžou pochopit, diagnostikovat a získáte přehled o k síti v Azure. Tato služba obsahuje zachytáváním paketů, další směrování, IP tok ověření, zobrazení skupiny zabezpečení, tok protokolů NSG. Scénář úrovně monitorování poskytuje pohledu koncové síťových prostředků, na rozdíl od monitorování jednotlivých síťových prostředků.

![Sledovací proces sítě Azure](./media/azure-operational-security/azure-operational-security-fig8.png)

Sledovací proces sítě aktuálně má následující možnosti:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Protokoly auditu</a>**-operace provedené v rámci konfigurace sítí přihlášeni. Tyto protokoly můžete zobrazit na portálu Azure nebo pomocí nástroje Microsoft, jako je Power BI nebo nástroje třetích stran. Protokoly auditu jsou k dispozici prostřednictvím portálu, prostředí PowerShell, rozhraní příkazového řádku a Rest API. Další informace o protokolů auditu najdete v tématu auditu operace s Resource Managerem. Protokoly auditu jsou k dispozici pro operace udělat na všechny síťové prostředky.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Ověřuje IP toku </a>**  – ověří, zda je paket povolený nebo zakázaný na základě toku informace 5 řazené kolekce členů paketu parametrů (cílovou IP adresu, zdrojové IP adresy, cílový Port, zdrojový Port a protokol). Pokud skupina zabezpečení sítě je zakázané paketu, vrátí se pravidlo a skupinu zabezpečení sítě, který odepřen paketu.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Další směrování</a>**  -určí další segment pro pakety směrovány v prostředcích infrastruktury sítě Azure umožňuje diagnostikovat žádné špatně nakonfigurovaný trasy definované uživatelem.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Zobrazení skupiny zabezpečení</a>**  -získá zabezpečení efektivní a použitých pravidel, která se použijí na virtuálním počítači.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Protokolování toku NSG</a>**  -toku protokoly pro skupinu zabezpečení sítě umožňují zaznamenat protokoly související s přenosy, které jsou povolené nebo zakázané pravidla zabezpečení ve skupině. Tok je definována informací o 5-n-tice – zdrojové adresy IP, cílovou IP adresu, zdrojový Port, cílový Port a protokol.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Analytika úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) můžete ukládat metriky, které zahrnují agregované transakce statistiky a kapacity data o požadavcích služby úložiště. Transakce jsou hlášeny na úrovni operace rozhraní API a na úrovni služby úložiště a hlásí kapacity na úrovni služby úložiště. Metriky dat slouží k analýze využití služby úložiště, diagnostikovat problémy s požadavky na služby úložiště a zlepšit výkon aplikací, které používají službu.

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště. Je k dispozici pro úložiště Analytics protokolování [službám Blob, fronty a tabulky](https://docs.microsoft.com/azure/storage/storage-introduction). Analytika úložiště protokoly podrobné informace o úspěšných a nezdařených požadavků a služba úložiště.

Tyto informace slouží k monitorování jednotlivých požadavků a diagnostikovat problémy s služby úložiště. Na základě typu best effort protokolované požadavky. Položky protokolu se vytvoří pouze v případě, že jsou požadavky na koncový bod služby. Pro příklad Pokud je účet úložiště svůj koncový bod objektu Blob, ale není v jeho tabulku nebo frontu koncových bodů, pouze protokoly se vytvoří, která se týkají služby objektů Blob.

Pokud chcete používat analytika úložiště, musíte povolit ji jednotlivě pro každou službu, kterou chcete monitorovat. Můžete ho povolit [portál Azure](https://portal.azure.com/); podrobnosti najdete v tématu [monitorování účtu úložiště na portálu Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Můžete také povolit Storage Analytics programově pomocí rozhraní REST API nebo knihovny klienta. Pomocí operace nastavit vlastnosti služby tak, aby Storage Analytics samostatně pro každou službu.

Agregovaná data se ukládají v dobře známé objektu blob (pro protokolování) a dobře známé tabulky (pro metriky), které můžete získat přístup pomocí služby objektů Blob a služby Table rozhraní API.

Analytika úložiště může mít 20 TB na množství uložených dat, která je nezávislá celkový limit pro váš účet úložiště. Všechny protokoly jsou uloženy v [objekty BLOB bloků](https://docs.microsoft.com/azure/storage/storage-analytics) v kontejneru nazvaném $logs, které automaticky vytvářejí, když je pro účet úložiště povolená analytika úložiště.

Fakturovatelný jsou tyto akce prováděné Storage Analytics:

-   Požadavky na vytvoření objektů blob pro protokolování
-   Požadavky na vytvoření entity tabulky pro metriky.

> [!Note]
> Další informace o fakturaci a zásad uchovávání dat najdete v tématu [Storage Analytics a fakturace](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Pro zajištění optimálního výkonu byste měli omezit počet vysoce využívané disky připojené k virtuálnímu počítači, aby se zabránilo možné omezení. Pokud všechny disky nejsou využívání vysoce ve stejnou dobu, účet úložiště může podporovat větší počet disků.

> [!Note]
> Další informace o limity účtu úložiště najdete v tématu [a cíle výkonnosti služby Azure Storage Scalability](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Následující typy požadavků na ověření a anonymní přihlášeni.

| Ověřeno  | Anonymní|
| :------------- | :-------------|
| Úspěšné požadavky | Úspěšné požadavky |
|Neúspěšné požadavky, včetně vypršení časového limitu, omezení šířky pásma, sítě, autorizace a dalších chyb | Požadavky pomocí sdíleného přístupového podpisu (SAS), včetně žádostí úspěšné a neúspěšné |
| Požadavky pomocí sdíleného přístupového podpisu (SAS), včetně žádostí úspěšné a neúspěšné |Chyby časového limitu pro klienta a serveru |
|   Požadavky na analytická data |    Neúspěšné požadavky GET s kódem chyby 304 (upraveno) |
| Požadavky na úložiště Analytics samostatně, jako je například protokol vytvoření nebo odstranění, se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolované](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [úložiště analýzy protokolů formátu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) témata. | Všechny ostatní selhání anonymních požadavků se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolované](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a [úložiště analýzy protokolů formátu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |
## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD také zahrnuje úplná sada funkcí správy identit, včetně vícefaktorového ověřování, registrace zařízení, hesla pomocí samoobslužné služby správy, samoobslužnou správu skupin, správy privilegovaného účtu, přístup na základě rolí ovládací prvek, sledování využití aplikací, bohaté auditování a monitorování zabezpečení a výstrahy.

-   Vylepšení aplikace zabezpečení pomocí vícefaktorového ověřování Azure AD a podmíněného přístupu.

-   Sledování využití aplikací a chránit vaši firmu před hrozbami pokročilé zabezpečení, monitorování a vytváření sestav.

Azure Active Directory (Azure AD) obsahuje různé sestavy zabezpečení, aktivit a auditu pro váš adresář. [Sestavy auditování Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) pomáhá zákazníkům k identifikaci privilegovaných akcí, které došlo k chybě v Azure Active Directory. Privilegované akce zahrnují změny zvýšení oprávnění (například role vytvoření nebo resetování hesla), změna konfigurace zásad (třeba zásady pro hesla) nebo změny konfigurace adresáře (například změny nastavení federace domén).

Sestavy poskytují záznam auditu z názvu události objektu actor, který provedl akci, cílový prostředek vliv na změny a datum a čas (ve formátu UTC). Zákazníci mohou načíst seznam událostí auditu pro Azure Active Directory pomocí [portál Azure](https://portal.azure.com/), jak je popsáno v [zobrazit protokoly auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Zde je seznam zahrnutých sestav:

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



Data z nich může být užitečné pro vaše aplikace, jako je například systémů SIEM, auditování a nástroje business intelligence. Vytvářením sestav Azure AD [rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) poskytují programový přístup k datům prostřednictvím sady založené na REST API. Tato rozhraní API můžete volat z různých programovacích jazyků a nástroje.

Události sestavy auditování Azure AD jsou uchovány na 180 dní.

> [!Note]
> Další informace o uchovávání dat v sestavách najdete v tématu [zásady uchování sestav Azure ve službě Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Pro zákazníky ukládat jejich [události auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) dobu uchování delší rozhraní API pro vytváření sestav lze pravidelně vyžádání události auditu do samostatné datové úložiště.

## <a name="summary"></a>Souhrn

Tento článek souhrny ochranu vašich osobních údajů a zabezpečení vašich dat při dodávání softwaru a služeb, které vám pomohou spravovat infrastrukturu IT vaší organizace. Microsoft rozpozná, že při jejich svěřit svoje data s jinými uživateli, vyžaduje tento vztah důvěryhodnosti přísných zabezpečení. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb. Zabezpečení a ochrana dat je nejvyšší prioritou ve společnosti Microsoft.

Tento článek vysvětluje

-   Jak dat shromažďovaných, zpracování a zabezpečené v Operations Management Suite (OMS).

-   Rychlá analýza událostí z různých zdrojů dat Identifikace bezpečnostních rizik a pochopení rozsahu a dopadů hrozeb a útoků s cílem omezit škody vzniklé narušením bezpečnosti

-   Identifikace vzorů útoků vizualizací odchozího škodlivého síťového provozu a typů bezpečnostních hrozeb Pochopení postavení zabezpečení celé prostředí bez ohledu na platformu.

-   Zaznamenejte všechny protokolů a událostí data potřebná pro audit zabezpečení nebo dodržování předpisů. Lomítko, čas a prostředky potřebné k poskytování úplné, vyhledávat a exportovatelný protokolu a sada dat události auditování zabezpečení.

<ul>
<li>Shromažďování událostí zabezpečení, auditování a analýzu porušení pečlivě sledovat vaše prostředky:</li>
<ul>
<li>Zabezpečení</li>
<li>Významné problém</li>
<li>Souhrny hrozeb</li>
</ul>
</ul>

## <a name="next-steps"></a>Další kroky

- [Návrh a provozního zabezpečení](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft návrhy svých služeb a softwaru, s důrazem na bezpečnost k zajištění, že jeho infrastruktura cloudu se odolné a před útoky.

- [Služby Operations Management Suite | Zabezpečení a dodržování předpisů](https://www.microsoft.com/cloud-platform/security-and-compliance)

Pomocí zabezpečení dat společnosti Microsoft a analýzy provádět inteligentního a efektivní detekce hrozeb.

- [Operace a plánování Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) sadu kroků a úloh, které můžete provést, abyste přizpůsobili použití služby Security Center na základě požadavků zabezpečení vaší organizace a modelu správy cloudu.

