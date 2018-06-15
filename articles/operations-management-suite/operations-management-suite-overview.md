---
title: Přehled Operations Management Suite (OMS) | Dokumentace Microsoftu
description: Microsoft Operations Management Suite (OMS) je cloudové řešení společnosti Microsoft pro správu IT, které pomáhá se správou a ochranou místních a cloudových infrastruktur.  Tento článek vysvětluje výhody OMS, identifikuje různé služby a nabídky zahrnuté v OMS a poskytuje odkazy na podrobné informace.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 94dedebe48060441cd3167fea87f6b721eb14517
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2017
ms.locfileid: "26684010"
---
# <a name="what-is-operations-management-suite-oms"></a>Co je Operations Management Suite (OMS)?
Tento článek obsahuje úvod k sadě Operations Management Suite (OMS), včetně stručného přehledu výhod, které poskytuje, řešení správy a služeb, které zahrnuje, a nabídek, které zahrnují různé služby a řešení.  Zahrnuje také odkazy na podrobnou dokumentaci k nasazení jednotlivých služeb a řešení.

## <a name="from-on-premises-to-the-cloud"></a>Z místního prostředí do cloudu
Microsoft už dlouho poskytuje produkty pro správu podnikových prostředí.  V roce 2007 se několik produktů pro správu konsolidovalo do sady System Center.  Patřil sem Configuration Manager, který poskytuje funkce, jako je inventarizace a distribuce softwaru, Operations Manager, který poskytuje monitorování systémů a aplikací, Orchestrator, který zahrnuje runbooky pro automatizaci ručních procesů, a Data Protection Manager pro zálohování a obnovení důležitých dat.

Vzhledem k tomu, jak se do cloudu přesouvá stále víc výpočetních prostředků, získaly produkty System Center víc cloudových funkcí, jako třeba Operations Manager a Orchestrator spravují prostředky v Azure.  V podstatě ale byly navržené jako místní řešení a vyžadují značné investice při nasazování a údržbě místního prostředí pro správu.  Pro zajištění kompletního využití cloudu a podpory budoucích aplikací bylo potřeba ke správě přistoupit novým způsobem.

## <a name="introducing-operations-management-suite"></a>Představujeme Operations Management Suite
Operations Management Suite (používá se také zkratka OMS) je kolekce služeb pro správu, které byly od prvopočátku navržené pro cloud.  Namísto nasazení a správy místních prostředků jsou komponenty OMS výhradně hostované v Azure.  Konfigurace je minimální a během několika minut můžete začít pracovat.  

- **Minimální náklady a složitost nasazení.**  Protože jsou veškerá data a komponenty pro OMS uložené v Azure, můžete téměř okamžitě začít pracovat bez složitých místních komponent a investice do nich.
- **Škálování na úrovních cloudu.**  Nemusíte si dělat starosti s placením za výpočetní prostředky, které nepotřebujete, ani s nedostatkem místa v úložišti, protože vám cloud umožňuje platit pouze za to, co skutečně používáte, a jde provést snadné škálování na vyžadovanou zátěž.  Můžete pro začátek spravovat několik prostředků, a potom vertikálně navýšit kapacitu na celé prostředí.
- **Využijte nejnovější funkce.**  Funkce ve službách OMS se průběžně přidávají a aktualizují.  Máte nepřetržitě přístup k nejnovějším funkcím bez nutnosti nasazovat aktualizace.
- **Integrované služby.**  Ačkoli každá služba OMS je vysoce hodnotná sama o sobě, mohou spolupracovat při řešení komplexních scénářů správy.  Runbook ve službě Azure Automation může například řídit proces převzetí služeb při selhání s Azure Site Recovery a poté zaznamenat informace do Log Analytics pro vygenerování výstrahy.
- **Globální znalosti.**  Řešení pro správu v OMS mají nepřetržitý přístup k nejnovějším informacím.  Řešení Zabezpečení a audit může například provádět analýzu hrozeb s využitím nejnovějších hrozeb zjišťovaných po celém světě.
- **Přístup odkudkoli.**  Přistupujte ke svému prostředí pro správu odkudkoli, kde máte prohlížeč.  Nainstalujte si aplikaci OMS na smartphone, abyste měli rychlý přístup k datům monitorování.

### <a name="is-it-just-for-the-cloud"></a>Je pouze pro cloud?
To, že služby OMS běží v cloudu, neznamená, že nemohou efektivně spravovat místní prostředí.  Umístěte agenta na libovolný počítač s Windows nebo Linuxem v datovém centru, který bude odesílat data do Log Analytics, kde se mohou analyzovat spolu s dalšími daty shromážděnými z cloudu nebo místních služeb.  Použijte Azure Backup a Azure Site Recovery pro využití cloudu k zálohování a zajištění vysoké dostupnosti místních prostředků.  
Runbooky v cloudu nemohou běžně přistupovat k místním prostředkům, ale můžete nainstalovat agenta na jeden nebo více počítačů, které budou hostovat runbooky v datovém centru.  Když spustíte runbook, stačí určit, jestli ho chcete spustit v cloudu, nebo na místním pracovním procesu.

## <a name="hybrid-management-with-system-center"></a>Hybridní správa s nástrojem System Center
Pokud máte existující instalaci nástroje System Center, můžete integrovat tyto komponenty se službami OMS a poskytnout tak hybridního řešení pro místní i cloudové prostředí s využitím relativních výhod každého produktu.  Pokud chcete analyzovat spravované agenty v cloudu, připojte existující skupinu správy Operations Manageru k Log Analytics.  Použijte existující proces zálohování s Data Protection Managerem k zálohování dat do cloudu.  


## <a name="oms-services"></a>Služby OMS
Základní funkce OMS poskytuje sada služeb, které běží v Azure.  Každá služba poskytuje specifickou funkci správy a kombinováním služeb je možné dosahovat různých scénářů správy.

|| Služba | Popis |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | Monitorování a analýza dostupnosti a výkonu různých prostředků včetně fyzických a virtuálních počítačů |
| ![Azure Automation](media/operations-management-suite-overview/icon-automation.png) | Automation | Automatizace ručních procesů a vynucení konfigurací pro fyzické a virtuální počítače |
| ![Azure Backup](media/operations-management-suite-overview/icon-backup.png) | Zálohování | Zálohování a obnovení důležitých dat |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | Poskytnutí vysoké dostupnosti pro důležitá data |

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) poskytuje služby monitorování pro OMS získáváním dat ze spravovaných prostředků do centrálního úložiště.  Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export.  Tato metoda vám umožňuje konsolidovat data z různých zdrojů, takže můžete kombinovat data ze služeb Azure s existujícím místním prostředím.  Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.  

![Přehled služby Log Analytics](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>Shromažďování dat
Existuje široká škála způsobů, kterými můžete dostat data do úložiště, aby je mohla služba Log Analytics analyzovat.

- **Virtuální počítače a počítače s Windows nebo Linuxem.**  Nainstalujte Microsoft Monitoring Agent na virtuální počítače nebo počítače s [Windows](../log-analytics/log-analytics-windows-agent.md) nebo [Linuxem](../log-analytics/log-analytics-linux-agents.md), ze kterých chcete shromažďovat data.  Agent automaticky stáhne konfiguraci z Log Analytics, která definuje události a výkonnostní data, která se mají shromažďovat.  S využitím webu Azure Portal můžete snadno nainstalovat agenta na virtuální počítače spuštěné v Azure.  Pokud máte existující prostředí Operations Manageru, můžete připojit skupinu správy k Log Analytics a automaticky zahájit shromažďování dat ze všech existujících agentů.
- **Služby Azure.**  Log Analytics shromažďuje telemetrie z [Diagnostiky Azure a monitorování Azure](../log-analytics/log-analytics-azure-storage.md) do úložiště, takže můžete monitorovat prostředky Azure.
- **Rozhraní API kolekce dat.**  Log Analytics má rozhraní [REST API pro naplňování dat z libovolného klienta](../log-analytics/log-analytics-data-collector-api.md).  To vám umožní shromažďovat data z aplikací třetích stran nebo implementovat vlastní scénáře správy.  Běžnou metodou je použít runbook v Azure Automation ke shromáždění dat a potom použít rozhraní API kolekce dat k jejich zapsání do úložiště.

#### <a name="reporting-and-analyzing-data"></a>Vytváření sestav a analýza dat
Log Analytics zahrnuje účinný dotazovací jazyk k extrakci dat uložených v úložišti.  Protože jsou data ze všech zdrojů uložená jako záznamy, můžete analyzovat data z více zdrojů v jediném dotazu.
  
Mimo ad hoc analýzy navíc Log Analytics poskytuje několik způsobů vytváření sestav a analýzy dat z dotazu.

- **Zobrazení a řídicí panely.**  [Zobrazení](../log-analytics/log-analytics-view-designer.md) a [řídicí panely](../log-analytics/log-analytics-dashboards.md) vizualizují výsledky dotazu v portálu.  Řešení pro správu obvykle zahrnují zobrazení, která analyzují data z řešení.  Můžete také vytvořit vlastní zobrazení pro analýzu dat a zajistit její snadnou dostupnost na vlastním portálu.
- **Export.**  Máte možnost exportovat výsledky libovolného dotazu, aby je bylo možné analyzovat mimo Log Analytics.  Můžete dokonce naplánovat pravidelný export do služby [Power BI](../log-analytics/log-analytics-powerbi.md), která poskytuje značné možnosti vizualizace a analýzy.
- **Rozhraní API pro prohledávání protokolů.**  Log Analytics má rozhraní [REST API pro shromažďování dat z libovolného klienta](../log-analytics/log-analytics-log-search-api.md).  To vám umožňuje programově zpracovávat data shromážděná v úložišti nebo k nim přistupovat z jiného monitorovacího nástroje.

#### <a name="alerting"></a>Zobrazení výstrah
Log Analytics vám může [proaktivně zobrazovat výstrahy](../log-analytics/log-analytics-alerts.md) nebo provádět nápravné akce, když zjistí problém.  Stejně jako všechny ostatní analýzy v Log Analytics se to provádí na základě prohledávání protokolu.  Toto prohledávání se spouští podle pravidelného plánu, a pokud výsledky splňují určitá kritéria, vytvoří se výstraha.

![Výstrahy Log Analytics](media/operations-management-suite-overview/overview-alerts.png)

Kromě vytváření záznamů o výstrahách v úložišti Log Analytics mohou výstrahy navíc také provádět následující akce.

- **E-mail.**  Odešle se e-mail, který vás proaktivně upozorní na zjištění problém.
- **Runbook.**  Výstraha v Log Analytics může spustit runbook v Azure Automation.  To se obvykle provádí při pokusu o napravení zjištěného problému.  Runbook může být spuštěný v cloudu pro případ problému v Azure nebo v jiném cloudu, nebo může být spuštěný v lokálním agentu pro případ problému s fyzickým nebo virtuálním počítačem.
- **Webhook.**  Výstraha může spustit webhook a předat mu data z výsledků prohledávání protokolu.  To umožňuje integraci s externími službami, jako jsou alternativní systémy pro výstrahy, nebo může dojít k pokusu o nápravnou akci pro externí web.

### <a name="azure-automation"></a>Azure Automation
[Azure Automation](http://azure.microsoft.com/documentation/services/automation) poskytuje automatizaci procesů a správu konfigurace pro OMS.  Automatizuje ruční procesy a pomáhá vynucovat konfigurace pro fyzické a virtuální počítače.  

#### <a name="process-automation"></a>Automatizace procesů
Azure Automation automatizuje ruční procesy s využitím [runbooků](../automation/automation-runbook-types.md), které jsou založené na skriptu PowerShellu nebo pracovním postupu PowerShellu.  Také zahrnuje prostředky podporující runbooky, jako jsou proměnné, které jde sdílet mezi více runbooky, a přihlašovací údaje a připojení, která vám umožňují ukládat šifrované informace, které mohou vyžadovat runbooky k ověření.
Runbooky nabízejí automatizaci procesů pro ostatní služby v sadě.  Vzhledem k tomu, že ke všem ostatním službám lze přistupovat pomocí PowerShellu nebo prostřednictvím rozhraní REST API, můžete vytvářet runbooky pro provádění takových funkcí, jako je shromažďování dat o správě v Log Analytics nebo inicializace zálohování s Azure Backup.

##### <a name="accessing-resources"></a>Přístup k prostředkům
Vzhledem k tomu, že jsou runbooky založené na PowerShellu, můžou spravovat všechny prostředky, ke kterým jde přistupovat pomocí rutin PowerShellu.  Když [načtete modul](../automation/automation-integration-modules.md) do účtu Automation, zpřístupní se všem runbookům v tomto účtu. 
 
Když jsou runbooky spuštěné v cloudu, můžou přistupovat ke všem prostředkům přístupným z cloudu.  To můžou být prostředky v předplatném Azure, v jiném cloudu, jako je AWS (Amazon Web Services), nebo ve službě přístupné prostřednictvím rozhraní REST API.  Runbooky v cloudu nejsou spuštěné pod žádnými přihlašovacími údaji, ale mohou k ověření u prostředků, ke kterým přistupují, využívat prostředky služby Automation, jako jsou přihlašovací údaje, připojení a certifikáty.

K prostředkům ve vašem datovém centru pravděpodobně nejde přistupovat z runbooku spuštěného v cloudu.  Můžete ale nainstalovat jeden nebo více procesů [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) v datacentru, aby bylo možné spustit runbooky, které vyžadují přístup k místním prostředkům.  Když spouštíte runbook, určujete, jestli se má spustit v cloudu, nebo ve specifickém pracovním procesu.

![Runbooky Azure Automation](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Spuštění runbooku
Runbooky je možné [spustit několika způsoby](../automation/automation-starting-a-runbook.md), takže je jde zahrnout do široké škály scénářů správy.  

- **Azure Portal.**  Stejně jako ostatní služby Azure může být služba Azure Automation spravovaná z webu Azure Portal.  Kromě spouštění runbooků můžete navíc importovat nebo vytvářet své vlastní.
- **Naplánované.**  Můžete naplánovat runbooky tak, aby se spouštěly v pravidelných intervalech.  To vám umožňuje automaticky opakovat pravidelný proces správy nebo shromažďovat data pro Log Analytics.
- **PowerShell a rozhraní API.**  Můžete spustit runbooky a předat jim vyžadované parametry z rutiny PowerShellu nebo rozhraní REST API Azure Automation.  
- **Webhook.**  Webhook jde vytvořit pro každý runbook, který povoluje spuštění z externích aplikací nebo webů.
- **Výstraha Log Analytics.**  Výstraha v Log Analytics může automaticky spustit runbook, aby se pokusil o napravení problému identifikovaného výstrahou.

#### <a name="configuration-management"></a>Správa konfigurace
[Konfigurace požadovaného stavu PowerShell (DSC)](../automation/automation-dsc-overview.md) je platforma správy ve Windows PowerShellu, která umožňuje nasadit a vynutit konfiguraci fyzických a virtuálních počítačů.  Azure Automation spravuje konfigurace DSC a poskytuje v cloudu server na vyžádání, ke kterému mohou přistupovat agenti a získávat požadované konfigurace.

![Azure Automation DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure Backup a Azure Site Recovery
Azure Backup a Azure Site Recovery přispívají ke kontinuitě podnikových procesů a zotavení po havárii.  Mají funkce, které vám pomohou zajistit, že aplikace zůstanou dostupné i během výpadků a vrátí se k běžnému provozu, jakmile jsou systémy znovu online.  Obě služby pomáhají splnit cíle plánovaných bodů obnovení (RPO) a plánované doby obnovení (RTO) definované pro vaši organizaci. RPO definuje limit přijatelné doby, po kterou nejsou během výpadku dostupná data, a RTO omezuje přijatelnou dobu, po kterou není během výpadku dostupná služba nebo aplikace.

#### <a name="azure-backup"></a>Azure Backup
[Azure Backup](http://azure.microsoft.com/documentation/services/backup) poskytuje služby zálohování a obnovení dat pro OMS.  Chrání data vaší aplikace a dlouhá léta je uchovává bez nutnosti velkých investic a s minimálními provozními náklady.  Kromě úloh aplikací, jako jsou například SQL Server a SharePoint, umožňuje zálohovat i data z fyzických a virtuálních serverů Windows.  Službu může využít i System Center Data Protection Manager (DPM) k replikaci chráněných dat do Azure pro zajištění redundance a dlouhodobého uložení.

Chráněná data ve službě Azure Backup se ukládají do trezoru záloh umístěného v konkrétní geografické oblasti. Data se replikují v rámci stejné oblasti a v závislosti na typu trezoru se můžou replikovat také do jiné oblasti pro zajištění vyšší odolnosti.

Azure Backup obsahuje tři základní scénáře.

- **Počítač s Windows a agentem služby Azure Backup.** Zálohuje soubory a složky z jakéhokoli klienta nebo serveru s Windows přímo do vašeho trezoru záloh Azure.<br><br>![Počítač s Windows a agentem služby Azure Backup](media/operations-management-suite-overview/overview-backup-01.png)
- **Server System Center Data Protection Manageru (DPM) nebo Microsoft Azure Backup Server.** Využívá server DPM nebo Microsoft Azure Backup Server kromě zálohování úloh aplikací, jako jsou SQL a SharePoint, i k zálohování souborů a složek do místního úložiště a jejich následné replikaci do vašeho trezoru záloh Azure. Podporuje virtuální počítače s Windows a Linuxem na technologii Hyper-V nebo VMware.<br><br>![Server System Center Data Protection Manageru (DPM) nebo Microsoft Azure Backup Server](media/operations-management-suite-overview/overview-backup-02.png)
- **Rozšíření virtuálního počítače Azure.** Zálohuje virtuální počítače s Windows nebo Linuxem v Azure do Azure Backup Vault.<br><br>![Rozšíření virtuálního počítače Azure](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) poskytuje kontinuitu podnikových procesů tím, že orchestruje replikaci místních virtuálních a fyzických počítačů do Azure nebo do sekundární lokality. Pokud není vaše primární lokalita dostupná, proběhne převzetí služeb při selhání sekundárním umístěním, aby mohli uživatelé pokračovat v práci, a po obnovení funkce systémů proběhne navrácení služeb. 

Azure Site Recovery nabízí vysokou dostupnost pro servery a aplikace.  Přispívá ke strategii kontinuity podnikových procesů a zotavení po havárii (BCDR) tím, že orchestruje replikaci, převzetí služeb při selhání a obnovení místních virtuálních počítačů Hyper-V a VMware a fyzických serverů s Windows nebo Linuxem. Počítače můžete replikovat do sekundárního datového centra nebo rozšířit své datové centrum jejich replikací do Azure. Site Recovery také poskytuje možnosti jednoduchého převzetí služeb při selhání úloh a jejich obnovení. Integruje se s mechanismy zotavení po havárii, jako je SQL Server AlwaysOn, a poskytuje plány obnovení pro snadné převzetí služeb při selhání úloh vrstvených napříč více počítači.

Azure Site Recovery obsahuje tři základní scénáře replikace.

- **Replikace virtuálních počítačů Hyper-V.**  Pokud jsou virtuální počítače Hyper-V spravovány v cloudech VMM, je možná replikace do sekundárního datového centra nebo do úložiště Azure. Replikace do Azure probíhá přes zabezpečené internetové připojení. Replikace do sekundárního datového centra probíhá přes síť LAN.  Pokud se virtuální počítače Hyper-V nespravují ve VMM, je možná jenom replikace do Azure Storage. Replikace do Azure probíhá přes zabezpečené internetové připojení.<br><br>![Replikace virtuálních počítačů Hyper-V](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **Replikace virtuálních počítačů VMware.**  Virtuální počítače VMware můžete replikovat do sekundárního datového centra, na kterém běží VMware, nebo do úložiště Azure. Replikace do Azure může probíhat přes síť VPN typu Site-to-Site nebo Azure ExpressRoute nebo přes zabezpečené internetové připojení. Replikace do sekundárního datového centra probíhá přes kanál nástroje InMage Scout.<br><br>![Replikace virtuálních počítačů VMware](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **Replikace fyzických serverů s Windows nebo Linuxem**  Fyzické servery můžete replikovat do sekundárního datového centra nebo do úložiště Azure. Replikace do Azure může probíhat přes síť VPN typu Site-to-Site nebo Azure ExpressRoute nebo přes zabezpečené internetové připojení. Replikace do sekundárního datového centra probíhá přes kanál nástroje InMage Scout. Azure Site Recovery obsahuje řešení OMS, které zobrazuje omezenou statistiku, ale pro všechny operace je nutné použít Azure Portal.<br><br>![Replikace fyzických serverů s Windows nebo Linuxem](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Site Recovery ukládá metadata do trezorů umístěných v konkrétní oblasti Azure. V samotné službě Site Recovery se replikovaná data neukládají.

## <a name="management-solutions"></a>Řešení pro správu
[Řešení pro správu](operations-management-suite-solutions.md) jsou předpřipravené sady logik, které implementují konkrétní scénáře správy s využitím jedné nebo více služeb OMS.  Jsou dostupná různá řešení od Microsoftu a partnerů, která můžete snadno přidat do předplatného Azure ke zvýšení hodnoty investice do OMS.  Jako partner můžete vytvořit vlastní řešení pro podporu aplikací a služeb a poskytnout je uživatelům prostřednictvím Azure Marketplace nebo šablon rychlého startu.

Dobrým příkladem řešení využívajícího více služeb k poskytnutí dalších funkcí je [řešení správy aktualizací](oms-solution-update-management.md).  Toto řešení používá agenta Log Analytics pro Windows a Linux ke shromažďování informací o požadovaných aktualizacích u jednotlivých agentů.  Zapíše tato data do úložiště Log Analytics, kde je můžete analyzovat pomocí obsaženého řídicího panelu.  Při vytvoření nasazení se runbooky v Azure Automation použijí k instalaci požadovaných aktualizací.  Celý tento proces spravujete na portálu a nemusíte se starat o žádné podrobnosti.

![Řešení](media/operations-management-suite-overview/overview-solution.png)

Většina řešení může provádět jednu nebo více následujících funkcí.

- Shromažďování dalších informací.  Log Analytics shromažďuje širokou škálu dat z klientů a služeb včetně výkonnostních dat a událostí.  Řešení pro správu může, obvykle s využitím runbooků Azure Automation, shromažďovat další informace, které nejsou dostupné z jiných datových zdrojů.
- Poskytování další analýzy získaných informací.  Řešení pro správu zahrnují řídicí panely a zobrazení, které poskytují analýzu a vizualizaci dat.  Obsahují odkazy zpět na předdefinované prohledávání protokolu a umožňují hlubokou analýzu podrobných dat.  Mohou také provádět analýzu dat, která již byla shromážděna do úložiště, jako je například hledání vzorců v rámci událostí zabezpečení, které indikují hrozbu.
- Přidávání funkcí.  Některá řešení poskytovaná Microsoftem mohou být založená na možnostech základních služeb pro poskytnutí dalších funkcí.  Mapa služby například nabízí vlastní konzolu ke zjišťování a mapování závislostí serverů a procesů v reálném čase.
Microsoft a partneři pravidelně přidávají do OMS další řešení, což vám umožňuje neustále zvyšovat hodnotu investice.  Můžete procházet a instalovat řešení Microsoftu prostřednictvím katalogu řešení na portálu OMS nebo procházet a instalovat řešení Microsoftu i partnerů prostřednictvím Azure Marketplace na Azure Portal.  

![Galerie řešení](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>Další kroky
* Další informace o [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Další informace o [Azure Automation](../automation/automation-intro.md).
* Další informace o [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Další informace o [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).
* Objevte [řešení, která jsou dostupná](../log-analytics/log-analytics-add-solutions.md) v různých nabídkách OMS. 

