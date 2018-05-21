---
title: Příručka pro operátory Azure IT Začínáme | Microsoft Docs
description: Úvodní příručka pro operátory IT Azure
services: ''
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: nepeters
ms.openlocfilehash: b63beffdd3b10071c6d18128e78a949374c171b2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="introduction-to-cloud-computing-and-microsoft-azure"></a>Úvod k řešení cloud computing a Microsoft Azure

Tento průvodce představuje základní koncepty týkající se nasazení a správa infrastruktury Microsoft Azure. Pokud jste ještě na cloud computing nebo Azure samostatně, tento průvodce pomáhá rychle vám pomůžou začít s koncepty, nasazení a správu podrobnosti. Mnoho části této příručky popisují operace, jako je nasazení virtuálního počítače a pak zadejte odkaz pro podrobné technické podrobnosti.


## <a name="cloud-computing-overview"></a>Cloud computing – přehled

Cloud computing poskytuje moderní alternativa k tradiční místní datacentra. Veřejný cloud dodavatelé poskytují a spravovat všechny výpočetní infrastruktury a základní software pro správu. Tyto dodavatelé poskytují širokou škálu cloudové služby. Cloudové služby v takovém případě může být virtuální počítač, webový server nebo hostovaných v cloudu databázového stroje. Jako zákazník zprostředkovatele cloudu zapůjčení tyto cloudové služby na podle potřeby. Při tom můžete převést kapitálové výdaje hardwaru údržby provozními výdaji. Cloudové služby také poskytuje následující výhody:

-   Rychlé nasazení velkých výpočetních prostředích

-   Rychlé navrácení systémů, které se už nevyžadují

-   Snadné nasazení tradičně složitých systémů, jako nástroje pro vyrovnávání zatížení

-   Možnost poskytnout flexibilní výpočetní kapacitu nebo určený počet číslic v případě potřeby

-   Více nákladově efektivní výpočetních prostředí

-   Přistupovat z libovolného místa s webový portál nebo programové automatizace

-   Cloudové služby pro potřeby většiny výpočty a aplikace

S místní infrastrukturou máte plnou kontrolu nad hardwaru a softwaru, který je nasazen. V minulosti to vedlo k rozhodnutí nákup hardwaru které se zaměřují na vertikálním navýšení kapacity. Příklad je nákup na server s více jader, aby splňovalo potřeby výkon ve špičce. Tato infrastruktura může bohužel nevyužité mimo okno vyžádání. S Azure můžete nasadit pouze infrastrukturu, která potřebujete a upravit tato nahoru nebo dolů kdykoli. To vede k zaměřená na škálování prostřednictvím nasazení další výpočetní uzly splňovat potřeba výkonu. Škálování cloudové služby je cenově výhodnější než vertikálním navýšení kapacity prostřednictvím nákladné hardwaru.

Microsoft má mnoho datových centrech Azure po celém světě, nasazuje s více plánované. Kromě toho Microsoft roste svrchovaných cloudy v oblastech, jako je Čína a Německu. Pouze největší globální podniky nasadit datových centrech tímto způsobem, aby se pomocí Azure usnadňuje podnikům jakékoli velikosti jejich služby blízko zákazníkům nasadit.

Pro malé firmy Azure umožňuje pro nízkonákladové vstupní bod, s možností škálování rychle jako požadavek pro výpočetní zvyšuje. Zabrání se tak velký počáteční kapitálové investici do infrastruktury a poskytuje flexibilitu a architektury přepracování systémy podle potřeby. Použití technologie cloud computing pro rozlišení i s modelem fast škálování a selhání fast nárůst spuštění.

Další informace o dostupné oblasti Azure, najdete v části [oblastí Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-is-classified-into-three-categories-saas-paas-and-iaas"></a>Cloud computing je rozdělit do tří kategorií: SaaS, IaaS a PaaS.

#### <a name="saas-software-as-a-service"></a>SaaS: Software jako služba

SaaS je software, který je centrálně hostované a spravované. Obvykle je založena na víceklientské architektury – jedna verze aplikace se používá pro všechny zákazníky. Škálovat lze na více instancí k zajištění nejlepšího výkonu dosáhnete ve všech umístěních. SaaS software je obvykle licencován prostřednictvím měsíční nebo roční předplatné.

Microsoft Office 365 je dobrým příkladem SaaS, nabídky. Odběratelé platit měsíční nebo roční předplatné a získají Microsoft Exchange, Microsoft OneDrive a rest sady Microsoft Office jako služba. Odběratelé vždycky získat nejnovější verzi a spravovaná serveru Exchange. Ve srovnání s instalace a upgradu systému Office každý rok, to je levnější a vyžaduje menší úsilí.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platforma jako služba 

S PaaS nasadit aplikace do prostředí, které poskytuje dodavatelem cloudové služby. Dodavatele nepodporuje všechny správu infrastruktury, můžete se zaměřit na vývoj aplikací.

Azure poskytuje že několik PaaS výpočetní nabídek, včetně funkce Web Apps služby Azure App Service a Azure Cloud Services (webové a pracovní role). V obou případech Vývojáři mají několik způsobů pro nasazení své aplikace bez znalosti nic o základní které to podporují. Vývojáři nemusíte vytvářet virtuální počítače (VM), použijte protokol RDP (Remote Desktop) pro přihlášení ke každé z nich nebo instalovat aplikaci. Právě stiskněte tlačítko (nebo na ji zavřít) a nástroje poskytované společností Microsoft, zřizovat virtuální počítače a pak nasaďte a nainstalovat aplikaci na nich.

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktury jako služby

Dodavatele cloudu IaaS spustí a spravuje všechny fyzické výpočetní prostředky a požadovaný software, který chcete povolit virtualizaci počítače. Zákazník této služby nasadí virtuální počítače v těchto hostované datových centrech. I když virtuální počítače jsou umístěné v datacentrum mimo pracoviště, má uživatel IaaS řízení konfigurace a správy z nich.

Azure obsahuje několik řešení IaaS, včetně virtuálních počítačů sady škálování virtuálního počítače a související síťové infrastruktury. Virtuální počítače jsou oblíbených volbou pro původně migrace služeb na Azure, protože umožní model migrace "navýšení a posunutí". Můžete nakonfigurovat virtuální počítač jako infrastruktury aktuálně spuštěna vaše služby ve vašem datovém centru a potom migrovat váš software do nového virtuálního počítače. Možná budete muset provést aktualizace konfigurace, jako je například adresy URL pro ostatní služby nebo úložiště, ale mnoho aplikace tímto způsobem můžete migrovat.

Sady škálování virtuálního počítače jsou postavené na virtuálních počítačích Azure a poskytují snadný způsob, jak nasadit clustery identické virtuálních počítačů. Sady škálování virtuálního počítače také podporují automatické škálování, aby bylo nové virtuální počítače můžete nasadit automaticky v případě potřeby. Díky tomu sady škálování virtuálního počítače ideální platformu na vyšší úrovni mikroslužbu výpočetní clustery hostitelů, například Azure Service Fabric a Azure Container Service.

## <a name="azure-services"></a>Služby Azure

Azure nabízí mnoho služeb v jeho Cloudová výpočetní platforma. Tyto služby patří.

### <a name="compute-services"></a>Výpočetní služby

Služby hostování a spuštění úlohy aplikace:

-   Virtuální počítače Azure, Linux a Windows

-   Aplikace služeb (webové aplikace, mobilní aplikace, aplikace logiky, aplikace API a funkce aplikace)

-   Azure Batch (pro rozsáhlé paralelní a dávkové úlohy výpočetní)

-   Azure Service Fabric

-   Azure Container Service

### <a name="data-services"></a>Datové služby

Služby pro ukládání a správě dat:

-   Úložiště Azure (zahrnuje služby Azure Blob, fronty, tabulky a souboru)

-   Azure SQL Database

-   Azure Cosmos DB

-   Microsoft Azure StorSimple

-   Azure Redis Cache

### <a name="application-services"></a>Aplikační služby

Služby pro vytváření a provozování aplikací:

-   Azure Active Directory (Azure AD)

-   Azure Service Bus pro připojení distribuovaných systémů

-   Azure HDInsight pro zpracování velkých objemů dat

-   Azure Scheduler

-   Azure Media Services

### <a name="network-services"></a>Síťové služby

Služba pro sítě v rámci Azure i mezi datovými centry Azure a místními:

-   Azure Virtual Network

-   Azure ExpressRoute

-   Azure DNS

-   Azure Traffic Manager

-   Azure Content Delivery Network

Podrobnou dokumentaci ke službám Azure, najdete v části [dokumentaci služby Azure](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Klíčové koncepty Azure

### <a name="datacenters-and-regions"></a>Datová centra a oblastí


Azure je globální Cloudová platforma, která je obecně k dispozici v mnoha oblastech po celém světě. Při zřizování služby, aplikace nebo virtuální počítač v Azure, budete vyzváni k výběru oblasti. Vybrané oblasti představuje speciﬁc datacenter, kde vaše aplikace běží. Další informace najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).

Jedním z beneﬁts používání Azure je, že můžete nasadit aplikace do různých datových centrech po celém světě. Oblast, kterou zvolíte můžete aﬀect výkon vaší aplikace. Je optimální vyberte oblast, která bude co nejblíže ke nejvíce zákazníkům, snížit latenci v síťové požadavky. Může také vyberte oblast pro splnění zákonných požadavků na distribuci aplikace v některých zemích.

### <a name="azure-portal"></a>Azure Portal


Portál Azure je webové aplikace, která slouží k vytváření, správu a odebrat prostředky Azure a služby. Portál Azure se nachází v https://portal.azure.com. Zahrnuje přizpůsobitelný řídicí panel a nástrojů pro správu prostředků Azure. Nabízí taky informace fakturace a předplatného. Další informace najdete v tématu [přehled portálu Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) a [Azure spravovat prostředky prostřednictvím portálu](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Zdroje a prostředky

Prostředky Azure jsou jednotlivé výpočetní, sítě, data nebo aplikace hostitelských služeb, které jsou nasazené do předplatného Azure. Některé běžné prostředky jsou virtuální počítače, účty úložiště nebo databází SQL. Služby Azure se často skládat z několika souvisejících prostředků Azure. Virtuální počítač Azure může zahrnovat virtuální počítač, účet úložiště, síťový adaptér a veřejnou IP adresu. Tyto prostředkům můžete vytvořit, spravovat a odstranit samostatně nebo jako skupinu. Prostředky Azure jsou podrobněji popsány dále v této příručce.

### <a name="resource-groups"></a>Skupiny prostředků

Skupinu prostředků Azure je kontejner, který obsahuje související prostředky pro řešení s Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení, nebo jenom prostředky, které chcete spravovat jako skupinu. Skupiny prostředků Azure jsou podrobněji popsány dále v této příručce.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

Šablonu Azure Resource Manager je soubor JavaScript Object Notation (JSON), který definuje jeden nebo více prostředků pro nasazení do skupiny prostředků. Definuje také závislosti mezi nasazené prostředky. Šablony Resource Manageru jsou podrobněji popsány dále v této příručce.

### <a name="automation"></a>Automation


Kromě vytváření, správu a odstraňování prostředky pomocí portálu Azure můžete tyto aktivity automatizovat pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure (CLI).

**Azure PowerShell**

Prostředí Azure PowerShell je sada modulů, které obsahují rutiny pro správu Azure. Rutiny můžete použít k vytváření, správu a odebrání služby Azure. Rutiny můžete dosáhnout nasazení bezobslužnou, opakovatelné a konzistentní. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

**rozhraní příkazového řádku Azure**

Rozhraní příkazového řádku Azure je nástroj, který slouží k vytváření, správu a odebrat prostředky Azure z příkazového řádku. Rozhraní příkazového řádku Azure je k dispozici pro Linux, Mac OS X a Windows. Další informace a podrobné technické informace najdete v tématu [nainstalovat Azure CLI](/cli/azure/install-azure-cli.md).

**REST API** Azure je založený na sadu rozhraní REST API, která podporuje uživatelské rozhraní portálu Azure. Většina těchto rozhraní REST API jsou podporovány také umožnit vám prostřednictvím kódu programu zřizovat a spravovat z libovolného zařízení využívajících Internet vašich prostředků Azure a aplikací. Další informace najdete v tématu [referenční informace sady SDK REST Azure](https://docs.microsoft.com/rest/api/index).


## <a name="azure-subscriptions"></a>Předplatná Azure


Předplatné je logické seskupení služeb Azure, které je propojena k účtu Azure. Jednom účet Azure může obsahovat víc předplatných. Fakturace služby Azure se provádí na základě za předplatné. Předplatná Azure mít účet správce, který má plnou kontrolu nad předplatné a Správce služby, který má kontrolu nad všechny služby v rámci předplatného. Kromě správci, lze udělit jednotlivých účtů podrobnou kontrolu prostředků Azure pomocí RBAC.

### <a name="select-and-enable-an-azure-subscription"></a>Vyberte a povolit předplatné Azure

Než začnete pracovat se službami Azure, je třeba odběr. K dispozici je několik typů předplatného.

**Uvolněte účty**: odkaz na zaregistrovat bezplatný účet je na [webu Azure](https://azure.microsoft.com/). To vám dává kreditní během 30 dnů a zkuste to libovolnou kombinaci prostředků v Azure. Pokud vaše částka Dal, váš účet je pozastaven. Na konci tuto zkušební verzi vaše služby jsou vyřazeny z provozu a přestane fungovat. Můžete upgradovat na průběžnými platbami předplatné kdykoli.

**Předplatné MSDN**: Pokud máte předplatné MSDN, získáte určitou velikostí Azure kredit ve výši každý měsíc. Například pokud máte Microsoft Visual Studio Enterprise s předplatné MSDN, získáte \$150 USD měsíčně v kreditu Azure.

Pokud částka Dal, služby jsou zakázané, dokud do následujícího měsíce spustí. Můžete vypnout limitu útraty a přidat platební kartu, který se má použít pro další náklady. Některé z těchto náklady jsou na webu MSDN účty slevou. Například platíte cenu Linux pro virtuální počítače s Windows serverem a je bez dalších poplatků pro servery Microsoft, například Microsoft SQL Server. Díky tomu MSDN účty ideální pro scénáře vývoje a testování.

**Účty BizSpark**: program BizSpark Microsoft poskytuje řadu výhod pro startupy. Jednou z těchto výhod je přístup k veškerého softwaru společnosti Microsoft pro vývojové a testovací prostředí pro účty až pět MSDN. Získat 150 USD kreditu Azure pro každou z těchto pět účty MSDN a nižší sazby platíte za několik služeb Azure, jako jsou virtuální počítače.

**Průběžné platby**: předplatné, platíte za použití kreditní nebo debetní karty připojením k účtu. Pokud jste v organizaci, můžete je také schválené pro fakturaci.

**Smlouvy Enterprise**: ke smlouvě enterprise, provedete pomocí počet služeb v Azure přes do následujícího roku a platíte tato šířka předem. Úsilí, které vytváříte obsazením po celý rok. Pokud jste překročit částku závazku, platíte můžete Nadlimitní zpětně. V závislosti na množství závazku můžete získat tak slevu v rámci služeb v Azure.



### <a name="grant-administrative-access-to-an-azure-subscription"></a>Udělit přístup správce k předplatnému Azure


Několik rolí správce účtu jsou k dispozici a můžete kdykoli změnit. Jsou dvě klíčové úlohy:

-   **Správce služeb**: Tato role je oprávnění ke správě služby Azure. Standardně je povolen přístup k stejný účet jako správce účtu.

-   **Spolusprávcem**: Tato role má stejný přístup jako správce služeb. Tato role však nelze změnit přidružení předplatné do adresáře Azure.

Další informace najdete v tématu [postup přidání nebo změna role Správce služby Azure](../../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Zobrazení fakturačních informací na portálu Azure


Důležitou součást pomocí Azure je možnost zobrazit fakturační informace. Portál Azure poskytuje podrobný přehled o Azure fakturační informace.

Další informace najdete v tématu [stažení vaší Azure fakturace faktury a denní data o využití](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Získání fakturační informace z fakturace rozhraní API


Kromě zobrazování fakturaci na portálu, můžete přístup ke fakturační informace pomocí skriptu nebo programu prostřednictvím rozhraní REST API fakturace Azure:

-   Využití rozhraní API služby Azure můžete načíst data o využití. Fakturační informace o využití můžete upřesnit tak označování souvisejících prostředků Azure. Můžete například označit všechny prostředky ve skupině prostředků s názvem oddělení nebo název projektu a pak sledování nákladů speciálně pro tento jednu značku.

-   Seznam všech dostupných prostředků, spolu s metadaty a ceny informace o každé z těchto prostředků, můžete použít rozhraní API služby Azure míra karty.

Další informace najdete v tématu [proniknout do vaší spotřeby prostředků Microsoft Azure](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognóza nákladů pomocí cenové kalkulačky

Ceny pro každou službu v Azure se liší. Mnoho služeb Azure zadejte úrovně Basic, Standard a Premium. Každá úroveň obvykle má několik cenové a výkonové úrovně. Pomocí [online cenové kalkulačky](http://azure.microsoft.com/pricing/calculator), cenovou odhady je možné vytvořit. Rozhraní kalkulačky zahrnuje možnost odhadnout náklady na jediném prostředku nebo skupině prostředků.

### <a name="set-up-billing-alerts"></a>Nastavení upozornění fakturace

Po nasazení vaší aplikace nebo řešení v Azure, můžete vytvořit výstrahy, které odesílání že e-mailem při přístupu limitech útraty definované ve výstraze. Další informace najdete v tématu [nastavit výstrahy pro vaše předplatné Microsoft Azure billing](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager je nasazení, správu a organizace mechanismus pro prostředky Azure. Pomocí Správce prostředků můžete shromáždit mnoho jednotlivé prostředky ve skupině prostředků.

Správce prostředků obsahuje také možnosti nasazení, které umožňují přizpůsobit nasazení a konfigurace související prostředky. Pomocí Správce prostředků, například můžete nasadit aplikaci, která se skládá z více virtuálních počítačů, nástroj pro vyrovnávání zatížení a SQL database jako na jednu jednotku. Tato nasazení vyvíjíte pomocí šablony Resource Manageru.

Resource Manager poskytuje několik výhod:

-   Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

-   Můžete opakovaně nasadit řešení v průběhu životního cyklu a mít jistotu, že jsou vaše prostředky nasazené v konzistentním stavu.

-   Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

-   Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

-   Řízení přístupu můžete použít ke všem službám ve vaší skupině prostředků a protože RBAC je nativně integrováno do platformy pro správu.

-   Značky můžete použít na materiály, které logicky uspořádat všechny prostředky ve vašem předplatném.

-   Můžete zpřehlednit fakturaci vaší organizace zobrazením náklady pro skupinu prostředků, které sdílejí stejnou značku.

### <a name="tips-for-creating-resource-groups"></a>Tipy pro vytvoření skupiny prostředků


Pokud jste při rozhodování o vaší skupiny prostředků, zvažte tyto tipy:

-   Všechny prostředky ve skupině prostředků musí mít stejný životní cyklus.

-   Prostředek můžete přiřadit jenom jedné skupiny najednou.

-   Můžete přidat nebo odebrat zdroje ze skupiny prostředků kdykoli. Každý prostředek musí patřit do skupiny prostředků. Takže pokud odeberete prostředek z jedné skupiny, musíte ji přidat do jiné.

-   Většina typů prostředků, můžete přesunout k jiné skupině prostředků kdykoli.

-   Prostředky ve skupině prostředků může být v různých oblastech.

-   Skupiny prostředků můžete použít k řízení přístupu pro prostředky v ní.

### <a name="building-resource-manager-templates"></a>Vytváření šablon Resource Manageru

Šablony Resource Manageru deklarativně definice prostředků a konfigurace prostředků, které se nasadí do jedna skupina prostředků. Šablony Resource Manageru můžete použít k orchestraci komplexních nasazení bez nutnosti skriptování nadbytečné nebo ruční konfigurace. Po můžete vytvořit šablonu, abyste ji mohli nasadit několikrát – pokaždé, když se identické výsledek.

Šablony Resource Manageru se skládá ze čtyř částí:

-   **Parametry**: Jedná se o vstupy do nasazení. Hodnoty parametrů lze zadat lidské nebo automatizovaného procesu. Příklad parametru může být správce uživatelské jméno a heslo pro virtuální počítač s Windows. Hodnoty parametru se používají v nasazení, když se zadal.

-   **Proměnné**: ty se používají k uložení hodnoty, které se používají v rámci nasazení. Na rozdíl od parametry hodnota proměnné není k dispozici v době nasazení. Místo toho je pevný programového nebo dynamicky vygenerovat.

-   **Prostředky**: Tato část šablony definuje prostředky, které mají být nasazeny, například virtuální počítače, účty úložiště a virtuální sítě.

-   **Výstup**: Po dokončení nasazení Resource Manager může vrátit data, jako jsou dynamicky generovaném připojovací řetězce.

Tyto mechanismy jsou dostupné pro automatizaci nasazení:

-   **Funkce**: můžete použít několik funkce v šablonách Resource Manageru. Jedná se o operací, jako je převod řetězce na malá písmena, nasazení více instancí definované prostředků a dynamicky vrácení cílová skupina prostředků. Správce prostředků funkce usnadňující tvorbu dynamických nasazení.

-   **Závislosti prostředků**: když nasazujete více zdrojů, některé prostředky, bude mít závislost na ostatní. Pro usnadnění nasazení, můžete použít deklaraci závislost tak, aby před jiné jsou nasazeny závislé prostředky.

-   **Propojování šablony**: Z v rámci jedné šablony Resource Manageru, můžete se propojit k jinou šablonu. To umožňuje nasazení rozložením na sadu cílových, zaměřené na konkrétní účel šablony.

Můžete vytvořit šablony Resource Manageru v každém textovém editoru. Azure SDK pro Visual Studio však obsahuje nástroje, které pomáhají. Pomocí sady Visual Studio můžete přidat prostředky do šablony v průvodci, pak nasazení a ladění šablony přímo z Visual Studia. Další informace najdete v tématu [šablon pro tvorbu Azure Resource Manageru](../../resource-group-authoring-templates.md).

Nakonec můžete převést existující skupiny prostředků do opakovatelně použitelných šablonu z portálu Azure. To může být užitečné, pokud chcete vytvořit šablonu využít existující skupiny prostředků, nebo jenom chcete prozkoumat základní JSON. Pokud chcete exportovat skupiny prostředků, vyberte **skriptu pro automatizaci** tlačítko z nastavení skupiny prostředků.

## <a name="security-of-azure-resources-rbac"></a>Zabezpečení prostředků Azure (RBAC)

Můžete udělit provozní přístup k uživatelské účty v zadaném oboru: předplatné, skupinu prostředků nebo jednotlivých prostředků. To znamená, že můžete nasadit sadu prostředků do skupiny prostředků, jako je například virtuální počítač a všechny související prostředky a udělení oprávnění pro konkrétního uživatele nebo skupiny. Tento přístup omezuje přístup pouze na prostředky, které patří do cílová skupina prostředků. Můžete také udělit přístup k jediný zdroj, například virtuální počítač nebo virtuální sítě.

Pokud chcete udělit přístup, přiřadíte roli uživatele nebo skupiny uživatelů. Existuje mnoho předdefinovaných rolí. Můžete také definovat vlastní role.

Zde je několik rolí příklad integrovaný do Azure:

-   **Vlastník**: uživatel k této roli můžou spravovat všechno včetně přístupu.

-   **Čtečka**: uživatel k této roli mohou číst prostředky všech typů (s výjimkou tajné údaje), ale nemůžou dělat změny.

-   **Přispěvatel virtuálních počítačů**: uživatel k této roli můžete spravovat virtuální počítače, ale nemohou spravovat virtuální sítě do které jsou připojeny nebo účet úložiště, které se nachází soubor virtuálního pevného disku.

-   **Přispěvatel databází SQL**: uživatel k této roli můžete spravovat databáze SQL, ale není jejich zásady zabezpečení.

-   **Správce zabezpečení SQL**: uživatel k této roli můžete spravovat zásady vztahující se k zabezpečení SQL serverů a databází.

-   **Přispěvatel účet úložiště**: uživatel k této roli můžete spravovat účty úložiště, ale nemohou spravovat přístup k účtům úložiště.

Další informace najdete v tématu [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Virtuální počítače Azure je jedním z centrální služeb IaaS v Azure. Virtuální počítače Azure podporuje nasazení systému Windows nebo Linux virtuálních počítačů v datacentru společnosti Microsoft Azure. Pomocí Azure Virtual Machines, celkový počet ovládat konfiguraci virtuálních počítačů a jsou zodpovědní za všechny instalace softwaru, konfigurace a údržby.

Pokud nasazujete virtuální počítač Azure, můžete vybrat bitovou kopii z Azure Marketplace, nebo je můžete zadat vlastní zobecněný bitové kopie. Tato image slouží k použití operačního systému a počáteční konfiguraci. Během nasazení Resource Manager zpracovává některá nastavení konfigurace, jako je například přiřazení názvu počítače, pověření pro správu a konfiguraci sítě. Rozšíření virtuálního počítače Azure můžete použít k automatizaci další konfigurace, třeba instalace softwaru, antivirový konfigurace a sledování řešení.

Vytvoření virtuálních počítačů v mnoha různých velikostí. Velikost virtuálního počítače stanoví, přidělení prostředků, jako je například zpracování, paměti a úložnou kapacitu. V některých případech se konkrétní funkce například podporou RDMA síťových adaptérů a disky SSD jsou k dispozici pouze v určité velikosti virtuálních počítačů. Úplný seznam velikosti virtuálních počítačů a možností, najdete v části "Velikosti virtuálních počítačů v Azure" pro [Windows](../../virtual-machines/windows/sizes.md) a [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Případy použití

Virtuální počítače Azure nabízí úplnou kontrolu nad konfigurace, proto jsou ideální pro širokou škálu úloh serveru, které se nehodí do modelu PaaS. Úlohy serveru jako jsou třeba servery databáze (SQL Server, Oracle nebo MongoDB), Windows Server Active Directory, Microsoft SharePoint a mnohé další se lze spustit na platformě Microsoft Azure. V případě potřeby můžete přesunout tyto úlohy z datacenter místní na jeden nebo více oblastech Azure, bez velké množství Rekonfigurace.

### <a name="deployment-of-virtual-machines"></a>Nasazení virtuálních počítačů

Virtuální počítače Azure můžete nasadit pomocí portálu Azure, pomocí automatizace modulu Azure PowerShell nebo pomocí rozhraní příkazového řádku pro různé platformy automatizace.

**Azure Portal**

Nasazení virtuálního počítače pomocí portálu Azure vyžaduje pouze aktivní předplatné Azure a přístup k webovým prohlížečem. Můžete vybrat celou řadu imagí jiného operačního systému s různými konfiguracemi. Všechny požadavky na síť a úložiště jsou nakonfigurované během nasazení. Další informace najdete v části "Vytvoření virtuálního počítače na portálu Azure" pro [Windows](../../virtual-machines/windows/quick-create-portal.md) a [Linux](../../virtual-machines/linux/quick-create-portal.md).

Kromě nasazení virtuálního počítače z portálu Azure, můžete nasadit šablonu Azure Resource Manageru z portálu. To nasadí a nakonfiguruje všechny prostředky, jak jsou definovány v šabloně. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a portálu Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).


**PowerShell**

Nasazení virtuálního počítače Azure pomocí prostředí PowerShell umožňuje k dokončení nasazení automatizaci všechny související virtuální počítač prostředků, včetně úložiště a sítě. Další informace najdete v tématu [vytvořit virtuální počítač s Windows pomocí Resource Manageru a prostředí PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Kromě nasazení výpočetní prostředky Azure jednotlivě, můžete použít modul Azure PowerShell nasadit šablonu Azure Resource Manager. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Rozhraní příkazového řádku (CLI)**

Stejně jako u modulu prostředí PowerShell, rozhraní příkazového řádku Azure poskytuje automatizaci nasazení a dá se v systémech Windows, OS X nebo Linux. Pokud používáte Azure CLI **rychle vytvořit virtuální počítač** příkazů, všechny související prostředky virtuálního počítače (včetně úložiště a sítě) a jsou nasazeny samotného virtuálního počítače. Další informace najdete v tématu [vytvořte virtuální počítač s Linuxem v Azure pomocí rozhraní příkazového řádku](../../virtual-machines/linux/quick-create-cli.md).

Podobně můžete rozhraní příkazového řádku Azure Pokud chcete nasadit šablonu Azure Resource Manager. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a rozhraní příkazového řádku Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Přístup a zabezpečení pro virtuální počítače

Přístup k virtuálnímu počítači z Internetu vyžaduje přidružených síťových rozhraní, nebo pokud jsou k dispozici, aby byla nakonfigurována s veřejnou IP adresu nástroj pro vyrovnávání zatížení. Veřejná IP adresa obsahuje název DNS, který bude odkazující na virtuální počítač nebo službu Vyrovnávání zatížení. Další informace najdete v tématu [IP adresách v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Můžete spravovat přístup k virtuálnímu počítači přes veřejnou IP adresu pomocí prostředku skupiny (NSG) zabezpečení sítě. Skupina NSG funguje jako brána firewall a povolit nebo odepřít provoz přes rozhraní sítě nebo podsítě na sadu definované porty. Například pokud chcete vytvořit relaci vzdálené plochy se virtuální počítač Azure, musíte nakonfigurovat NSG, které chcete povolit příchozí přenosy na portu 3389. Další informace najdete v tématu [otevřít porty pro virtuální počítač v Azure pomocí webu Azure portal](../../virtual-machines/windows/nsg-quickstart-portal.md).

Nakonec stejně jako u správu všechny počítače, měli byste poskytnout zabezpečení pro virtuální počítač Azure na operační systém pomocí zabezpečovací přihlašovací údaje a software brány firewall.

## <a name="azure-storage"></a>Azure Storage

Azure Storage je služba spravovaných společností Microsoft, která poskytuje odolné, škálovatelné a redundantní úložiště. Účet úložiště Azure jako prostředek můžete přidat do žádné skupiny prostředků pomocí libovolné metody nasazení prostředků. Azure obsahuje čtyři typy úložiště: objekt Blob úložiště, úložiště File, úložiště Table a Queue storage. Pokud nasazujete účet úložiště, dva typy účtů jsou k dispozici, pro obecné účely a úložiště objektů blob. Účet úložiště pro obecné účely poskytuje přístup pro všechny čtyři typy úložiště. Účty úložiště BLOB jsou podobné účtům pro obecné účely, ale obsahovat specializované objekty BLOB, které zahrnují úrovní úrovněmi horkého a studeného přístupu. Další informace o úložiště objektů blob najdete v tématu [úložiště objektů Azure Blob](../../storage/blobs/storage-blob-storage-tiers.md).

Účty úložiště Azure se dá nakonfigurovat s různými úrovněmi redundance:

-   **Místně redundantní úložiště** poskytuje vysokou dostupnost zajištěním tři kopie všechna data jsou před zápis se považuje úspěšné vytvářeny synchronně. Tyto kopie jsou uloženy v jednom zařízení v jedné oblasti. Repliky jsou umístěné v domén samostatné selhání a upgradu domény. To znamená, že jsou data k dispozici, i když uzel úložiště, není podržíte vaše data selže nebo je offline. Tím se aktualizovat.

-   **Geograficky redundantní úložiště** vytváří tři synchronních kopií dat v primární oblasti pro vysokou dostupnost a potom asynchronně provádí tři repliky v spárované oblasti pro obnovení po havárii.

-   **Geograficky redundantní úložiště s přístupem pro čtení** je geograficky redundantní úložiště plus umožňuje číst data v sekundární oblasti. Tato možnost je vhodný pro zotavení po havárii částečné. Pokud dojde k problému s primární oblasti, můžete změnit aplikace mají přístup jen pro čtení k spárované oblast.

### <a name="use-cases"></a>Případy použití 

Každý typ úložiště má případu použití různých.

**Blob Storage** 

Slovo *blob* je zkratka pro *binární rozsáhlý objekt*. Objekty BLOB jsou nestrukturovaných soubory, jako jsou ty, které ukládáte ve vašem počítači. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů. Úložiště objektů Blob Azure také obsahuje datové disky virtuálních počítačů Azure.

Úložiště Azure podporuje tři typy objektů blob:

-   **Objekty BLOB bloků** jsou používané pro udržení běžném provozu soubory až 195 GB (4 MB × 50 000 bloků) velikost. Případem primárního použití pro objekty BLOB bloku je úložiště souborů, které jsou číst od začátku do konce, například soubory médií nebo soubory bitových kopií pro weby. Protože soubory větší než 64 MB, musí se nahrát jako malé bloky budou jsou pojmenované objekty BLOB bloku. Tyto bloky jsou pak konsolidované (nebo potvrzené) do konečné objektu blob.

-   **Objekty BLOB stránek** jsou používané pro udržení náhodný přístup soubory velikost až 1 TB. Objekty BLOB stránky se používají primárně jako úložiště zálohování pro virtuální pevné disky, které poskytují trvanlivý disky pro virtuální počítače Azure, IaaS výpočetní služby v Azure. Objekty BLOB stránky se jsou pojmenované, protože poskytují náhodné čtení a zápis na stránky o velikosti 512 bajtů.

-   **Doplňovací objekty BLOB** skládá z bloků jako objekty BLOB bloku, ale jsou optimalizované pro doplňovací operace. Ty se často používají pro protokolování informací z jednoho nebo více zdrojů na stejný objekt blob. Například všechny vaše protokolování trasování může zapisovat do stejné doplňovací objekt blob pro aplikaci, která běží na víc virtuálních počítačů. Jeden doplňovací objekt blob může být až 195 GB.

Další informace najdete v tématu [Začínáme s Azure Blob storage pomocí rozhraní .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

**Úložiště souborů**

Úložiště Azure File je služba, která nabízí sdílené složky v cloudu pomocí standardní protokol Server Message Block (SMB). Služba podporuje SMB 2.1 a SMB 3.0. S Azure File storage můžete migrovat aplikace, které spoléhají na sdílené složky Azure rychle a bez nákladných přepisů. Aplikace běžící na virtuálních počítačích Azure, v cloudové služby, nebo z místního klienti připojit sdílenou složku v cloudu. Toto je podobná jak desktopová aplikace připojí typickou sdílenou složku SMB. Potom může sdílenou složku File Storage připojit a používat libovolný počet aplikací.

Protože sdílené úložiště je standardní sdílené složky SMB, aplikace běžící v Azure můžou k datům ve sdílené složce přistupovat přes systém souborů vstupně-výstupních operací rozhraní API. Vývojáři mohou použít proto svoje dovednosti a znalosti kódu při migraci stávajících aplikací. IT specialisté můžou použít rutiny prostředí PowerShell k vytvoření, připojovat a spravovat sdílené složky úložiště v rámci správy aplikací Azure.

Další informace najdete v tématu [Začínáme s Azure File storage ve Windows](../../storage/files/storage-how-to-use-files-windows.md) nebo [postup používání Azure File storage s Linuxem](../../storage/files/storage-how-to-use-files-linux.md).

**Table Storage**

Azure Table Storage je služba, která ukládá strukturovaná data typu NoSQL v cloudu. Úložiště Table je úložiště klíčů/atributů s návrhem bez schématu. Vzhledem k tomu, Table storage bez schématu, je snadné data přizpůsobovat potřebám vaší aplikace měnícím. Přístup k datům je rychlý a cenově výhodný pro všechny typy aplikací. Využívání úložiště Table Storage obvykle znamená výrazně nižší náklady než tradiční SQL pro podobné objemy dat.

Úložiště Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení a dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit. Účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

Další informace najdete v tématu [Začínáme s Azure Table storage](../../cosmos-db/table-storage-how-to-use-dotnet.md).

**Queue Storage**

Úložiště Azure Queue zajišťuje cloudový přenos zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování, jsou součásti aplikací často odpojené tak, aby bylo možné škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Další informace najdete v tématu [Začínáme s Azure Queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Nasazení účtu úložiště

Existuje několik možností pro nasazení účtu úložiště.

**Azure Portal**

Nasazení účtu úložiště pomocí portálu Azure vyžaduje pouze aktivní předplatné Azure a přístup k webovým prohlížečem. Nový účet úložiště můžete nasadit do skupiny nové nebo existující prostředek. Po vytvoření účtu úložiště, můžete vytvořit objekt blob kontejneru nebo ve sdílené složce pomocí portálu. Můžete vytvořit tabulku a fronty úložiště entity prostřednictvím kódu programu. Další informace najdete v tématu [vytvořit účet úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

Kromě nasazení účtu úložiště na portálu Azure, můžete nasadit šablonu Azure Resource Manageru z portálu. To nasadí a nakonfiguruje všechny prostředky, jak jsou definovány v šabloně, včetně všech účtů úložiště. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a portálu Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Nasazení účtu úložiště Azure pomocí prostředí PowerShell umožňuje automatizace dokončení nasazení účtu úložiště. Další informace najdete v tématu [použití Azure Powershellu s Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Kromě nasazení prostředků Azure jednotlivě, můžete použít modul Azure PowerShell nasadit šablonu Azure Resource Manager. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Rozhraní příkazového řádku (CLI)**

Stejně jako u modulu prostředí PowerShell, rozhraní příkazového řádku Azure poskytuje automatizaci nasazení a dá se v systémech Windows, OS X nebo Linux. Můžete použít rozhraní příkazového řádku Azure **vytvořit účet úložiště** příkaz pro vytvoření účtu úložiště. Další informace najdete v tématu [použití Azure CLI s Azure Storage.](../../storage/common/storage-azure-cli.md)

Podobně můžete rozhraní příkazového řádku Azure Pokud chcete nasadit šablonu Azure Resource Manager. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a rozhraní příkazového řádku Azure](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Přístup a zabezpečení pro Azure Storage

Úložiště Azure, se získají různými způsoby, včetně ale portálu Azure při vytváření virtuálních počítačů a operaci a z knihovny klienta úložiště. 

**Disky virtuálního počítače**

Pokud nasazujete virtuální počítač, musíte taky vytvořit účet úložiště pro uložení disku operačního systému virtuálního počítače a jakýchkoli dalších datových disků. Můžete vybrat existující účet úložiště nebo vytvořte novou. Protože maximální velikost objektu blob je 1024 GB, jeden disk virtuálního počítače má maximální velikost 1,023 GB. Ke konfiguraci větší datový disk, můžete k dispozici více datových disků k virtuálnímu počítači a fondu společně jako jednoho logického disku. Další informace najdete v tématu "Správa disky Azure" pro [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) a [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

**Nástroje pro úložiště**

Účty služby Azure storage je přístupná prostřednictvím mnoha průzkumníci jiného úložiště, jako je například Průzkumník cloudu Visual Studio. Tyto nástroje umožňují procházet účty úložiště a data. Další informace a seznam průzkumníci úložiště k dispozici, najdete v části [nástrojích klienta Azure Storage](../../storage/common/storage-explorers.md).

**Úložiště rozhraní API**

Úložiště prostředků jsou přístupné přes jakýkoli jazyk, který umí vytvářet požadavky HTTP/HTTPS. Azure Storage dále nabízí programovací knihovny pro několik oblíbených jazyků. Tyto knihovny zjednodušují práci s Azure Storage podle starají o drobnosti jako synchronní a asynchronní vyvolání, dávkování operací, řízení výjimek a Automatické opakované pokusy. Další informace najdete v tématu [odkazu k REST API služby Azure Storage](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

**Přístupové klíče k úložišti**

Každý účet úložiště má dvě ověřovací klíče, primární a sekundární. Buď můžete použít pro operace přístupu úložiště. Tyto klíče úložiště se používají k zabezpečení účtu úložiště a jsou vyžadovány pro prostřednictvím kódu programu přístup k datům. Existují dva klíče, aby bylo možné příležitostně výměny klíčů pro zvýšení zabezpečení. Je důležité k lepšímu zabezpečení klíčů, protože mít k dispozici, a to společně s název účtu, umožňuje neomezený přístup k veškerým datům v účtu úložiště.

**Sdílené přístupové podpisy**

Pokud potřebujete, aby uživatelé mohli mít řízený přístup ke svým prostředkům úložiště, můžete vytvořit sdílený přístupový podpis. Sdílený přístupový podpis je token, který může být připojí k adrese URL, který umožní Delegovaný přístup k prostředku úložiště. Každý, kdo má token má přístup k prostředku, který odkazuje s oprávněními, aby určovala, pro dobu, po které je platný. Další informace najdete v tématu [pomocí sdílené přístupové podpisy](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network


Virtuální sítě jsou nezbytné pro podporu komunikace mezi virtuálními počítači. Můžete definovat podsítě, vlastní IP adresu, nastavení DNS, zabezpečení, filtrování a vyrovnávání zatížení. S použitím brány VPN nebo okruhem ExpressRoute, můžete připojit virtuální sítě Azure do místní sítě.

### <a name="use-cases"></a>Případy použití

Existují případy použití v odlišných pro sítě Azure.

**Jenom pro cloud virtuálních sítí**

Virtuální síť Azure, ve výchozím nastavení, je přístupné pouze pro prostředky, které jsou uložené v Azure. Prostředky, které jsou připojené ke stejné virtuální síti můžete vzájemně komunikovat. Můžete přidružit síťová rozhraní virtuálních počítačů a s veřejnou IP adresu, která zpřístupněte virtuálního počítače přes Internet Vyrovnávání zatížení. Můžete líp zabezpečit přístup do veřejně vystavené prostředky pomocí skupiny zabezpečení sítě.

**Mezi různými místy virtuální sítě**

Místní sítě můžete připojit k virtuální síti Azure pomocí ExpressRoute nebo připojení site-to-site VPN. V této konfiguraci je virtuální síť Azure v podstatě založená na cloudu rozšířením místní sítě.

Protože virtuální síť Azure je připojen k síti na pracovišti, mezi různými místy, že virtuální sítě musíte použít jedinečnou část adresního prostoru, který vaše organizace používá. Stejným způsobem, který různých sídlech společnosti jsou přiřazeny určité podsítě IP Azure stane jiného umístění, jako rozšíření vaší sítě.

### <a name="deploying-a-virtual-network"></a>Nasazení virtuální sítě

Existuje několik možností pro nasazení virtuální sítě.

**Azure Portal**

Nasazení virtuální sítě Azure pomocí portálu Azure vyžaduje pouze aktivní předplatné Azure a přístup k webovým prohlížečem. Můžete nasadit nové virtuální sítě do skupiny nové nebo existující prostředek. Při vytváření nového virtuálního počítače z portálu, můžete vybrat existující virtuální síť nebo vytvořte novou. Další informace najdete v tématu [vytvoření virtuální sítě pomocí portálu Azure](../../virtual-network/quick-create-portal.md).

Kromě nasazení virtuální sítě Azure z portálu Azure, můžete nasadit šablonu Azure Resource Manageru z portálu. To nasadí a nakonfiguruje všechny prostředky, jak jsou definovány v šabloně, včetně všechny prostředky, virtuální sítě. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a portálu Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Nasazení virtuální sítě Azure pomocí prostředí PowerShell umožňuje automatizace dokončení nasazení účtu úložiště. Další informace najdete v tématu [vytvoření virtuální sítě pomocí prostředí PowerShell](../../virtual-network/quick-create-powershell.md).

Kromě nasazení prostředků Azure jednotlivě, můžete použít modul Azure PowerShell nasadit šablonu Azure Resource Manager. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Rozhraní příkazového řádku (CLI)**

Stejně jako u modulu prostředí PowerShell, rozhraní příkazového řádku Azure poskytuje automatizaci nasazení a dá se v systémech Windows, OS X nebo Linux. Můžete použít rozhraní příkazového řádku Azure **vytvoření sítě vnet** příkaz pro vytvoření virtuální sítě. Další informace najdete v tématu [vytvoření virtuální sítě pomocí rozhraní příkazového řádku Azure](../../virtual-network/quick-create-cli.md).

Podobně můžete rozhraní příkazového řádku Azure Pokud chcete nasadit šablonu Azure Resource Manager. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a rozhraní příkazového řádku Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-networks"></a>Přístup a zabezpečení pro virtuální sítě

Zabezpečený virtuální sítě Azure může pomoct pomocí skupiny zabezpečení sítě. Skupiny Nsg obsahují seznam pravidel seznamu ACL řízení přístupu, která povolí nebo zakážou provoz sítě instancím virtuálních počítačů ve virtuální síti. Skupiny Nsg můžete přidružit podsítě nebo jednotlivým instancím virtuálních počítačů v této podsíti. Pokud přidružíte skupinu NSG podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti. Kromě toho lze dále omezit provoz do konkrétního virtuálního počítače tím, že přidružíte skupinu NSG přímo s tohoto virtuálního počítače. Další informace najdete v tématu [filtrování provozu sítě přenosů se skupinami zabezpečení sítě](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Další postup

- [Vytvoření virtuálního počítače Windows](/virtual-machines/windows/quick-create-portal.md)
- [Vytvoření virtuálního počítače s Linuxem](../../virtual-machines/linux/quick-create-portal.md)
