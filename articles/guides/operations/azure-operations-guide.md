---
title: Příručka Začínáme pro operátory Azure IT | Dokumenty společnosti Microsoft
description: Příručka Začínáme pro operátory Azure IT
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 4f9da6cbfe8d1e6b92c39148b275de193730c8f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623581"
---
# <a name="get-started-for-azure-it-operators"></a>Začínáme pro operátory Azure IT

Tato příručka představuje základní koncepty související s nasazením a správou infrastruktury Microsoft Azure. Pokud s cloud computingem nebo samotným Azure teče teče, pomůže vám tato příručka rychle začít s koncepty, nasazením a podrobnostmi o správě. Mnoho částí této příručky diskutovat o operaci, jako je například nasazení virtuálního počítače a pak poskytnout odkaz pro podrobné technické podrobnosti.

## <a name="cloud-computing-overview"></a>Přehled cloud computingu

Cloud computing poskytuje moderní alternativu k tradičnímu místnímu datovému centru. Dodavatelé veřejného cloudu poskytují a spravují veškerou výpočetní infrastrukturu a základní software pro správu. Tito dodavatelé poskytují širokou škálu cloudových služeb. Cloudová služba v tomto případě může být virtuální počítač, webový server nebo databázový stroj hostovaný v cloudu. Jako zákazník poskytovatele cloudu si tyto cloudové služby pronajímáte podle potřeby. Tímto způsobem převedete kapitálové náklady údržby hardwaru na provozní náklady. Cloudová služba také poskytuje tyto výhody:

- Rychlé nasazení velkých výpočetních prostředí

- Rychlé deallokace systémů, které již nejsou zapotřebí

- Snadné nasazení tradičně složitých systémů, jako jsou vykladače zatížení

- Schopnost poskytovat flexibilní výpočetní kapacitu nebo škálování v případě potřeby

- Nákladově efektivnější výpočetní prostředí

- Přístup odkudkoli pomocí webového portálu nebo programové automatizace

- Cloudové služby pro většinu potřeb výpočetních prostředků a aplikací

S místní infrastrukturou máte úplnou kontrolu nad nasaditým hardwarem a softwarem. Historicky to vedlo k rozhodnutí o nákupu hardwaru, která se zaměřují na rozšiřování. Příkladem je nákup serveru s více jádry pro splnění potřeb špičkového výkonu. Bohužel tato infrastruktura může být nevyužité mimo okno poptávky. S Azure můžete nasadit jenom infrastrukturu, kterou potřebujete, a kdykoli ji upravit nahoru nebo dolů. To vede k zaměření na horizontální navýšení kapacity prostřednictvím nasazení dalších výpočetních uzlů k uspokojení potřeby výkonu. Škálování cloudových služeb je nákladově efektivnější než škálování prostřednictvím drahého hardwaru.

Microsoft nasadil mnoho datových center Azure po celém světě a plánuje se víc. Microsoft navíc zvyšuje suverénní mraky v oblastech, jako je Čína a Německo. Tímto způsobem můžou nasadit datacentra jen ty největší globální podniky, takže použití Azure usnadňuje podnikům jakékoli velikosti nasazovat své služby v blízkosti svých zákazníků.

Pro malé firmy Azure umožňuje nízkonákladový vstupní bod s možností rychlého škálování s rostoucí poptávkou po výpočetních výkonech. To zabraňuje velkým počátečním kapitálovým investicím do infrastruktury a podle potřeby poskytuje flexibilitu architektům a rearchitektrearchitect systémům. Použití cloud computingu dobře zapadá do škálovacího a rychle rychlého modelu růstu startupů.

Další informace o dostupných oblastech Azure najdete v [tématu oblasti Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Model cloud computingu

Azure používá model cloud computingu založený na kategoriích služeb poskytovaných zákazníkům. Tři kategorie služeb zahrnují infrastrukturu jako službu (IaaS), platformu jako službu (PaaS) a software jako službu (SaaS). Dodavatelé sdílejí část nebo veškerou odpovědnost za součásti v zásobníku výpočetní techniky v každé z těchto kategorií. Podívejme se na každou z kategorií pro cloud computing.
![Porovnání zásobníku cloud computingu](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktura jako služba

Dodavatel cloudu IaaS spouští a spravuje všechny fyzické výpočetní prostředky a požadovaný software, který umožňuje virtualizaci počítače. Zákazník této služby nasazuje virtuální počítače v těchto hostovaných datových centrech. Přestože virtuální počítače jsou umístěny v datovém centru mimo lokalitu, příjemce IaaS má kontrolu nad konfigurací a správou operačního systému a ponechá základní infrastrukturu dodavateli cloudu.

Azure obsahuje několik řešení IaaS, včetně virtuálních počítačů, škálovacích sad virtuálních počítačů a související síťové infrastruktury. Virtuální počítače jsou oblíbenou volbou pro počáteční migraci služeb do Azure, protože umožňují model migrace "lift and shift". Můžete nakonfigurovat virtuální počítač, jako je infrastruktura, která aktuálně běží služby ve vašem datovém centru, a pak migrovat software do nového virtuálního počítače. Možná budete muset provést aktualizace konfigurace, například adresy URL do jiných služeb nebo úložiště, ale tímto způsobem můžete migrovat mnoho aplikací.

Škálovací sady virtuálních počítačů jsou postavené na virtuálních počítačích Azure a poskytují snadný způsob nasazení clusterů s identickými virtuálními počítači. Škálovací sady virtuálních strojů také podporují automatické škálování, takže nové virtuální počítače se dá v případě potřeby nasadit automaticky. Díky tomu škálování virtuálních strojů nastavuje ideální platformu pro hostování výpočetních clusterů mikroslužeb vyšší úrovně, jako je Azure Service Fabric a Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platforma jako služba

S PaaS nasadíte aplikaci do prostředí, které poskytuje dodavatel cloudových služeb. Dodavatel provádí veškerou správu infrastruktury, takže se můžete soustředit na vývoj aplikací a správu dat.

Azure poskytuje několik výpočetních nabídek PaaS, včetně funkce Webové aplikace služby Azure App Service a Cloud Services Azure (webové a pracovní role). V obou případech vývojáři mají několik způsobů, jak nasadit svou aplikaci, aniž by věděli něco o matice a šrouby, které ji podporují. Vývojáři nemusí vytvářet virtuální počítače (VM), používat protokol RDP (Remote Desktop Protocol) k přihlášení ke každému z nich ani nainstalovat aplikaci. Stačí sáhnou tlačítko (nebo blízko k němu) a nástroje poskytované společností Microsoft zřídit virtuální chod a pak nasadit a nainstalovat aplikaci na ně.

#### <a name="saas-software-as-a-service"></a>SaaS: Software jako služba

SaaS je software, který je centrálně hostován a spravován. Obvykle je založen na víceklientské architektuře – jedna verze aplikace se používá pro všechny zákazníky. Lze jej škálovat na více instancí, aby byl zajištěn nejlepší výkon ve všech umístěních. Software SaaS je obvykle licencován prostřednictvím měsíčního nebo ročního předplatného. Dodavatelé softwaru SaaS jsou zodpovědní za všechny součásti zásobníku softwaru, takže vše, co spravujete, jsou poskytované služby.

Microsoft Office 365 je dobrým příkladem nabídky SaaS. Předplatitelé platí měsíční nebo roční poplatek za předplatné a jako službu získají Microsoft Exchange, Microsoft OneDrive a zbytek sady Microsoft Office. Předplatitelé vždy získat nejnovější verzi a Exchange server je spravován za vás. Ve srovnání s instalací a upgradem Office každý rok je to levnější a vyžaduje méně úsilí.

## <a name="azure-services"></a>Služby Azure

Azure nabízí mnoho služeb ve své platformě cloud computingu. Tyto služby zahrnují následující.

### <a name="compute-services"></a>Výpočetní služby

Služby pro hostování a spouštění úloh aplikací:

- Virtuální počítače Azure – Linux i Windows

- Služby aplikací (webové aplikace, mobilní aplikace, logické aplikace, aplikace API a aplikace funkcí)

- Azure Batch (pro rozsáhlé paralelní a dávkové výpočetní úlohy)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Datové služby

Služby pro ukládání a správu dat:

- Azure Storage (zahrnuje azure blob, frontu, tabulku a souborové služby)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Aplikační služby

Služby pro vytváření a provoz aplikací:

- Azure Active Directory (Azure AD)

- Azure Service Bus pro připojení distribuovaných systémů

- Azure HDInsight pro zpracování velkých objemů dat

- Aplikace Azure Logic Apps pro pracovní postupy integrace a orchestrace

- Mediální služby Azure

### <a name="network-services"></a>Síťové služby

Služby pro vytváření sítí v rámci Azure i mezi Azure a místními datovými centry:

- Azure Virtual Network

- Azure ExpressRoute

- DNS poskytované azurem

- Azure Traffic Manager

- Azure Content Delivery Network

Podrobnou dokumentaci ke službám Azure najdete v [dokumentaci ke službám Azure](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Klíčové koncepty Azure

### <a name="datacenters-and-regions"></a>Datová centra a oblasti

Azure je globální cloudová platforma, která je obecně dostupná v mnoha oblastech po celém světě. Když zřídíte službu, aplikaci nebo virtuální počítač v Azure, budete vyzváni k výběru oblasti. Vybraná oblast představuje konkrétní datové centrum, ve kterém je aplikace spuštěna. Další informace najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).

Jednou z výhod používání Azure je, že můžete nasadit své aplikace do různých datových center po celém světě. Vybraná oblast může ovlivnit výkon aplikace. Je optimální zvolit oblast, která je blíže k většině vašich zákazníků, snížit latenci v síťových požadavků. Můžete také vybrat oblast, která splňuje zákonné požadavky na distribuci aplikace v určitých zemích nebo oblastech.

### <a name="azure-portal"></a>portál Azure

Portál Azure je webová aplikace, kterou se dá použít k vytváření, správě a odebírání prostředků a služeb Azure. Portál Azure se nachází na [adrese portal.azure.com](https://portal.azure.com). Obsahuje přizpůsobitelný řídicí panel a nástroje pro správu prostředků Azure. Poskytuje také informace o fakturaci a předplatném. Další informace najdete v tématu [Přehled portálu Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) a Správa prostředků Azure [prostřednictvím portálu](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Prostředky

Prostředky Azure jsou individuální výpočetní prostředky, sítě, data nebo služby hostování aplikací, které byly nasazené do předplatného Azure. Některé běžné prostředky jsou virtuální počítače, účty úložiště nebo databáze SQL. Služby Azure se často skládají z několika souvisejících prostředků Azure. Virtuální počítač Azure může například zahrnovat virtuální počítač, účet úložiště, síťový adaptér a veřejnou IP adresu. Tyto prostředky lze vytvořit, spravovat a odstranit jednotlivě nebo jako skupinu. Prostředky Azure jsou podrobněji popsány dále v této příručce.

### <a name="resource-groups"></a>Skupiny prostředků

Skupina prostředků Azure je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může obsahovat všechny prostředky pro řešení nebo pouze prostředky, které chcete spravovat jako skupinu. Skupiny prostředků Azure jsou podrobněji popsány dále v této příručce.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

Šablona Azure Resource Manager je soubor Zápisu objektu JavaScript (JSON), který definuje jeden nebo více prostředků pro nasazení do skupiny prostředků. Definuje také závislosti mezi nasazenými prostředky. Šablony Správce prostředků jsou podrobněji popsány dále v této příručce.

### <a name="automation"></a>Automatizace

Kromě vytváření, správy a odstranění prostředků pomocí portálu Azure můžete tyto aktivity automatizovat pomocí PowerShellu nebo rozhraní příkazového řádku Azure (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell je sada modulů, které poskytují rutiny pro správu Azure. Rutiny můžete použít k vytvoření, správě a odebrání služeb Azure. Rutiny vám mohou pomoci dosáhnout konzistentních, opakovatelných a hands-off nasazení. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure je nástroj, který můžete použít k vytvoření, správě a odebrání prostředků Azure z příkazového řádku. Rozhraní příkazového příkazu Azure je k dispozici pro Linux, Mac OS X a Windows. Další informace a technické podrobnosti najdete [v tématu Instalace příkazového příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>Rozhraní REST API

Azure je postavenna sadě rest API, které podporují ui portálu Azure. Většina těchto rozhraní REST API jsou také podporovány, aby vám umožní programově zřizování a správu prostředků a aplikací Azure z libovolného zařízení s podporou internetu. Další informace naleznete v [odkazu na azure rest sdk .](https://docs.microsoft.com/rest/api/index)

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Správci mají přístup k Azure PowerShellu a Rozhraní příkazového uživatelského rozhraní Azure prostřednictvím prostředí přístupného prohlížeči s názvem Azure Cloud Shell. Toto interaktivní rozhraní poskytuje flexibilní nástroj pro správce Linuxu a Windows používat jejich rozhraní příkazového řádku volby, buď Bash nebo PowerShell. Azure Cloud Shell může být přístup prostřednictvím portálu , jako samostatné webové rozhraní na [shell.azure.com](https://shell.azure.com)nebo z řady dalších přístupových bodů. Další informace najdete v [tématu Přehled Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Předplatná Azure

Předplatné je logické seskupení služeb Azure, které je propojené s účtem Azure. Jeden účet Azure může obsahovat více předplatných. Fakturace pro služby Azure se provádí na základě předplatného. Předplatná Azure mají správce účtu, který má plnou kontrolu nad předplatným, a správce služeb, který má kontrolu nad všemi službami v předplatném. Informace o klasických správcích předplatného najdete v tématu [Přidání nebo změna správců předplatného Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). Kromě správců může být jednotlivým účtům uděleno podrobné řízení prostředků Azure pomocí [řízení přístupu na základě rolí (RBAC).](../../role-based-access-control/overview.md)

### <a name="select-and-enable-an-azure-subscription"></a>Výběr a povolení předplatného Azure

Než budete moci pracovat se službami Azure, potřebujete předplatné. K dispozici je několik typů předplatného.

**Bezplatné účty**: Odkaz pro registraci bezplatného účtu je na [webu Azure](https://azure.microsoft.com/). To vám dává kredit v průběhu 30 dnů vyzkoušet libovolnou kombinaci prostředků v Azure. Pokud částku kreditu překročíte, bude váš účet pozastaven. Na konci zkušební verze jsou vaše služby vyřazeny z provozu a již nebudou fungovat. Předplatné s průběžným platbou můžete kdykoli upgradovat.

**Předplatná MSDN**: Pokud máte předplatné MSDN, získáte každý měsíc určitou částku v kreditu Azure. Například pokud máte Předplatné Microsoft Visual Studio Enterprise s MSDN, získáte \$150 za měsíc kreditu Azure.

Pokud částku kreditu překročíte, bude služba až do začátku následujícího měsíce zakázána. Limit útraty můžete vypnout a přidat kreditní kartu, která bude použita pro dodatečné náklady. Některé z těchto nákladů jsou diskontovány pro účty MSDN. Například platíte cenu Linuxu za virtuální počítače se systémem Windows Server a za servery microsoftu, jako je například Microsoft SQL Server, se neúčtují žádné další poplatky. Díky tomu jsou účty MSDN ideální pro scénáře vývoje a testování.

**Účty BizSpark**: Program Microsoft BizSpark poskytuje mnoho výhod pro spuštění. Jednou z těchto výhod je přístup ke všem softwarem společnosti Microsoft pro vývojová a testovací prostředí až pro pět účtů MSDN. Získáte $150 v kreditu Azure pro každý z těchto pěti účtů MSDN a platíte snížené sazby za několik služeb Azure, jako jsou virtuální počítače.

**Průběžně zdatný**: S tímto předplatným platíte za to, co používáte, připojením kreditní nebo debetní karty k účtu. Pokud jste organizace, můžete být také schváleni pro fakturaci.

**Podnikové smlouvy**: S podnikovou smlouvou se zavazujete používat určitý počet služeb v Azure v průběhu příštího roku a tuto částku zaplatíte předem. Závazek, který učiníte, se spotřebovává po celý rok. Pokud překročíte částku závazku, můžete nadlimitní část zaplatit nedoplatky. V závislosti na výši závazku získáte slevu na služby v Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Udělení přístupu pro správu k předplatnému Azure

RBAC má několik předdefinovaných rolí, které můžete použít k přiřazení oprávnění. Chcete-li z uživatele udělat správce předplatného Azure, přiřaďte mu roli [vlastníka](../../role-based-access-control/built-in-roles.md#owner) v oboru předplatného. Role Vlastník poskytuje uživateli úplný přístup ke všem prostředkům v předplatném, včetně práva delegovat přístup na ostatní.

Další informace najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Zobrazení fakturačních údajů na webu Azure Portal

Důležitou součástí používání Azure je možnost zobrazit fakturační údaje. Portál Azure poskytuje podrobný přehled o fakturačních údajích Azure.

Další informace najdete v tématu [Jak stáhnout fakturační fakturu Azure a údaje o denním využití](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Získání fakturačních údajů z fakturačních api

Kromě zobrazení fakturace na portálu můžete získat přístup k fakturačním údajům pomocí skriptu nebo programu prostřednictvím api Azure Billing REST:

- K načtení dat o využití můžete použít rozhraní API využití Azure. Informace o využití fakturace můžete doladit označením souvisejících prostředků Azure. Můžete například označit každý zdroj ve skupině zdrojů názvem oddělení nebo projektem a potom sledovat náklady konkrétně na tuto jednu značku.

- Rozhraní AZURE Rate Card API můžete použít k zobrazení seznamu všech dostupných prostředků spolu s metadaty a informacemi o cenách o každém z těchto prostředků.

Další informace najdete v článku [Získání přehledu o spotřebě prostředků Microsoft Azure](../../cost-management-billing/manage/usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognóza nákladů pomocí cenové kalkulačky

Ceny pro každou službu v Azure se liší. Mnoho služeb Azure poskytuje základní, standardní a prémiové úrovně. Obvykle má každá úroveň několik úrovní ceny a výkonu. Pomocí [online cenové kalkulačky](https://azure.microsoft.com/pricing/calculator)můžete vytvářet odhady cen. Kalkulačka obsahuje flexibilitu při odhadu nákladů na jeden zdroj nebo skupinu zdrojů.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager je mechanismus nasazení, správy a organizace pro prostředky Azure. Pomocí Správce prostředků můžete do skupiny prostředků spojit mnoho jednotlivých prostředků.

Správce prostředků také zahrnuje možnosti nasazení, které umožňují přizpůsobitelné nasazení a konfiguraci souvisejících prostředků. Například pomocí Správce prostředků můžete nasadit aplikaci, která se skládá z více virtuálních počítačů, nástroj pro vyrovnávání zatížení a databáze SQL jako jednu jednotku. Tato nasazení můžete vyvíjet pomocí šablony Správce prostředků.

Resource Manager poskytuje několik výhod:

- Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

- Můžete opakovaně nasadit řešení v průběhu životního cyklu vývoje a mít jistotu, že vaše prostředky jsou nasazeny v konzistentním stavu.

- Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

- Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

- Můžete použít řízení přístupu pro všechny služby ve vaší skupině prostředků, protože RBAC je nativně integrován do platformy pro správu.

- Můžete použít značky na prostředky logicky uspořádat všechny prostředky v předplatném.

- Fakturaci v organizaci můžete objasnit zobrazením nákladů pro skupinu prostředků, které sdílejí stejnou značku.

### <a name="tips-for-creating-resource-groups"></a>Tipy pro vytváření skupin zdrojů

Při rozhodování o skupinách zdrojů zvažte tyto tipy:

- Všechny prostředky ve skupině prostředků by měly mít stejný životní cyklus.

- Zdroj můžete přiřadit pouze jedné skupině najednou.

- Prostředek můžete kdykoli přidat nebo odebrat ze skupiny prostředků. Každý prostředek musí patřit do skupiny prostředků. Pokud tedy odeberete prostředek z jedné skupiny, musíte jej přidat do jiné.

- Většinu typů prostředků můžete kdykoli přesunout do jiné skupiny prostředků.

- Prostředky ve skupině prostředků mohou být v různých oblastech.

- Skupinu prostředků můžete použít k řízení přístupu k prostředkům v ní.

### <a name="building-resource-manager-templates"></a>Vytváření šablon Správce prostředků

Šablony Správce prostředků deklarativně definují konfigurace prostředků a prostředků, které budou nasazeny do jedné skupiny prostředků. Šablony Správce prostředků můžete použít k orchestraci složitých nasazení bez nutnosti nadměrného skriptování nebo ruční konfigurace. Po vytvoření šablony ji můžete nasadit vícekrát – pokaždé se stejným výsledkem.

Šablona Správce prostředků se skládá ze čtyř částí:

- **Parametry**: Jedná se o vstupy pro nasazení. Hodnoty parametrů mohou být poskytnuty lidským nebo automatizovaným procesem. Příkladem parametru může být uživatelské jméno správce a heslo pro virtuální počítače se systémem Windows. Hodnoty parametrů se používají v průběhu nasazení, když jsou zadány.

- **Proměnné**: Tyto hodnoty se používají k uložení hodnoty, které se používají v průběhu nasazení. Na rozdíl od parametrů není v době nasazení k dispozici hodnota proměnné. Místo toho je pevně zakódován nebo dynamicky generován.

- **Prostředky**: Tato část šablony definuje prostředky, které mají být nasazeny, jako jsou virtuální počítače, účty úložiště a virtuální sítě.

- **Výstup**: Po dokončení nasazení správce prostředků může vrátit data, jako jsou dynamicky generované připojovací řetězce.

Pro automatizaci nasazení jsou k dispozici následující mechanismy:

- **Funkce**: V šablonách Správce prostředků můžete použít několik funkcí. Patří mezi ně operace, jako je například převod řetězce na malá písmena, nasazení více instancí definovaného prostředku a dynamické vrácení cílové skupiny prostředků. Funkce Správce prostředků pomáhají vytvářet dynamická nasazení.

- **Závislosti prostředků**: Při nasazování více prostředků budou mít některé prostředky závislost na ostatních. Chcete-li usnadnit nasazení, můžete použít deklaraci závislostí tak, aby závislé prostředky jsou nasazeny před ostatními.

- **Propojení šablon**: V rámci jedné šablony Správce prostředků můžete vytvořit odkaz na jinou šablonu. To umožňuje rozklad nasazení do sady cílových šablon specifických pro daný účel.

Šablony Správce prostředků můžete vytvářet v libovolném textovém editoru. Sada Azure SDK pro Visual Studio však obsahuje nástroje, které vám pomohou. Pomocí sady Visual Studio můžete přidat prostředky do šablony prostřednictvím průvodce a potom nasadit a ladit šablonu přímo z visual studia. Další informace najdete [v tématu Vytváření šablon Azure Resource Manager .](../../resource-group-authoring-templates.md)

Nakonec můžete převést existující skupiny prostředků na opakovaně použitelnou šablonu z webu Azure Portal. To může být užitečné, pokud chcete vytvořit nasaditšablonu existující skupiny prostředků, nebo chcete pouze prozkoumat základní JSON. Chcete-li exportovat skupinu prostředků, vyberte tlačítko **Automatizační skript** v nastavení skupiny prostředků.

## <a name="security-of-azure-resources-rbac"></a>Zabezpečení prostředků Azure (RBAC)

Provozní přístup k uživatelským účtům můžete udělit v zadaném oboru: předplatné, skupina prostředků nebo jednotlivé prostředky. To znamená, že můžete nasadit sadu prostředků do skupiny prostředků, jako je například virtuální počítač a všechny související prostředky, a udělit oprávnění konkrétnímu uživateli nebo skupině. Tento přístup omezuje přístup pouze prostředky, které patří do cílové skupiny prostředků. Můžete také udělit přístup k jednomu prostředku, jako je například virtuální počítač nebo virtuální síť.

Chcete-li udělit přístup, přiřadíte roli uživateli nebo skupině uživatelů. Existuje mnoho předdefinovaných rolí. Můžete také definovat vlastní role.

Tady je několik příkladů [předdefinovaných rolí v Azure:](../../role-based-access-control/built-in-roles.md)

- **Vlastník**: Uživatel s touto rolí může spravovat vše, včetně přístupu.

- **Čtečka**: Uživatel s touto rolí může číst prostředky všech typů (kromě tajných kódů), ale nemůže provádět změny.

- **Přispěvatel virtuálního počítače**: Uživatel s touto rolí může spravovat virtuální počítače, ale nemůže spravovat virtuální síť, ke které jsou připojeny, nebo účet úložiště, ve kterém se nachází soubor Virtuálního pevného disku.

- **Přispěvatel SQL DB**: Uživatel s touto rolí může spravovat databáze SQL, ale ne jejich zásady související se zabezpečením.

- **Správce zabezpečení SQL**: Uživatel s touto rolí může spravovat zásady související se zabezpečením serverů SQL a databází.

- **Přispěvatel účtu úložiště**: Uživatel s touto rolí může spravovat účty úložiště, ale nemůže spravovat přístup k účtům úložiště.

Další informace najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Virtuální počítače Azure je jednou z centrálních služeb IaaS v Azure. Virtuální počítače Azure podporují nasazení virtuálních počítačů s Windows nebo Linuxem v datovém centru Microsoft Azure. S virtuálními počítači Azure máte úplnou kontrolu nad konfigurací virtuálních počítačů a jste zodpovědní za veškerou instalaci, konfiguraci a údržbu softwaru.

Když nasazujete virtuální počítač Azure, můžete vybrat image z Azure Marketplace nebo můžete poskytnout vlastní generalizovanou image. Tato bitová kopie slouží k použití operačního systému a počáteční konfigurace. Během nasazení bude Správce prostředků zpracovávat některá nastavení konfigurace, například přiřazení názvu počítače, pověření pro správu a konfigurace sítě. Rozšíření virtuálních počítačů Azure můžete použít k další automatizaci konfigurací, jako je instalace softwaru, konfigurace antivirového softwaru a řešení monitorování.

Můžete vytvářet virtuální počítače v mnoha různých velikostech. Velikost virtuálního počítače určuje přidělení prostředků, jako je zpracování, paměť a kapacita úložiště. V některých případech jsou specifické funkce, jako jsou síťové adaptéry s podporou RDMA a disky SSD, k dispozici pouze s určitými velikostmi virtuálních počítačů. Úplný seznam velikostí a možností virtuálních počítačů najdete v tématu "Velikosti pro virtuální počítače v Azure" pro [Windows](../../virtual-machines/windows/sizes.md) a [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Případy použití

Vzhledem k tomu, že virtuální počítače Azure nabízejí úplnou kontrolu nad konfigurací, jsou ideální pro širokou škálu serverových úloh, které se nevejdou do modelu PaaS. Serverové úlohy, jako jsou databázové servery (SQL Server, Oracle nebo MongoDB), Windows Server Active Directory, Microsoft SharePoint a mnoho dalších, je možné spustit na platformě Microsoft Azure. Pokud je to žádoucí, můžete přesunout tyto úlohy z místního datového centra do jedné nebo více oblastí Azure, bez velkého množství rekonfigurace.

### <a name="deployment-of-virtual-machines"></a>Nasazení virtuálních počítačů

Virtuální počítače Azure můžete nasadit pomocí portálu Azure, pomocí automatizace s modulem Azure PowerShell nebo pomocí automatizace s rozhraním příkazového příkazu pro více platforem.

#### <a name="portal"></a>Portál

Nasazení virtuálního počítače pomocí portálu Azure vyžaduje jenom aktivní předplatné Azure a přístup k webovému prohlížeči. Můžete vybrat mnoho různých bitových kopií operačního systému s různými konfiguracemi. Všechny požadavky na úložiště a sítě jsou konfigurovány během nasazení. Další informace najdete v tématu "Vytvoření virtuálního počítače na webu Azure Portal" pro [Windows](../../virtual-machines/windows/quick-create-portal.md) a [Linux](../../virtual-machines/linux/quick-create-portal.md).

Kromě nasazení virtuálního počítače z portálu Azure můžete nasadit šablonu Azure Resource Manager z portálu. Tím se nasadí a nakonfiguruje všechny prostředky, jak jsou definovány v šabloně. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a portálu Azure](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Nasazení virtuálního počítače Azure pomocí PowerShellu umožňuje úplnou automatizaci nasazení všech souvisejících prostředků virtuálních strojů, včetně úložiště a sítí. Další informace naleznete [v tématu Vytvoření virtuálního aplikace windows pomocí Správce prostředků a prostředí PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Kromě individuálního nasazení výpočetních prostředků Azure můžete použít modul Azure PowerShell k nasazení šablony Azure Resource Manager. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Rozhraní příkazového řádku (CLI)

Stejně jako u modulu PowerShell poskytuje rozhraní příkazového řádku Azure automatizaci nasazení a dá se použít v systémech Windows, OS X nebo Linux. Když používáte příkaz **rychlého vytvoření virtuálního** počítače Azure CLI, nasadí se všechny související prostředky virtuálních strojů (včetně úložiště a sítě) a samotný virtuální počítač. Další informace najdete [v tématu Vytvoření virtuálního počítače s Linuxem v Azure pomocí příkazového příkazového příkazu](../../virtual-machines/linux/quick-create-cli.md).

Podobně můžete použít Azure CLI k nasazení šablony Azure Resource Manager. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Přístup a zabezpečení virtuálních počítačů

Přístup k virtuálnímu počítači z Internetu vyžaduje, aby bylo přidružené síťové rozhraní nebo nástroj pro vyrovnávání zatížení nakonfigurováno s veřejnou IP adresou. Veřejná IP adresa obsahuje název DNS, který se přeloží na virtuální počítač nebo nástroj pro vyrovnávání zatížení. Další informace najdete v tématu [IP adresy v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Přístup k virtuálnímu počítači prostřednictvím veřejné IP adresy můžete spravovat pomocí prostředku skupiny zabezpečení sítě (NSG). Skupina zabezpečení sítě funguje jako brána firewall a umožňuje nebo zakazuje provoz v síťovém rozhraní nebo podsíti na sadě definovaných portů. Například chcete-li vytvořit relaci vzdálené plochy s virtuálním počítačem Azure, musíte nakonfigurovat soubor zabezpečení sítě tak, aby povoloval příchozí provoz na portu 3389. Další informace najdete [v tématu Otevírání portů k virtuálnímu počítači v Azure pomocí portálu Azure](../../virtual-machines/windows/nsg-quickstart-portal.md).

Nakonec, stejně jako u správy jakéhokoli počítačového systému, měli byste zajistit zabezpečení virtuálního počítače Azure v operačním systému pomocí pověření zabezpečení a softwarových firewallů.

## <a name="azure-storage"></a>Azure Storage

Azure Storage je služba spravovaná microsoftem, která poskytuje trvalé, škálovatelné a redundantní úložiště. Účet úložiště Azure můžete přidat jako prostředek do libovolné skupiny prostředků pomocí libovolné metody nasazení prostředků. Azure zahrnuje čtyři typy úložiště: úložiště objektů blob, úložiště souborů, úložiště tabulek a úložiště front. Při nasazování účtu úložiště jsou k dispozici dva typy účtů, obecné účely a úložiště objektů blob. Účet úložiště pro obecné účely umožňuje přístup ke všem čtyřem typům úložiště. Účty úložiště objektů blob jsou podobné účtům pro obecné účely, ale obsahují specializované objekty BLOB, které obsahují úrovně přístupu za tepla a za studena. Další informace o úložišti objektů blob najdete v [tématu Azure Blob storage](../../storage/blobs/storage-blob-storage-tiers.md).

Účty úložiště Azure lze nakonfigurovat s různými úrovněmi redundance:

- **Místně redundantní úložiště** poskytuje vysokou dostupnost tím, že zajišťuje, že tři kopie všech dat jsou provedeny synchronně před zápisje považován za úspěšný. Tyto kopie jsou uloženy v jednom zařízení v jedné oblasti. Repliky jsou umístěny v samostatných doménách selhání a upgradovacích doménách. To znamená, že data jsou k dispozici i v případě, že uzel úložiště, který je držitelem dat selže nebo je převzat do offline aktualizovat.

- **Geograficky redundantní úložiště** vytvoří tři synchronní kopie dat v primární oblasti pro vysokou dostupnost a pak asynchronně vytvoří tři repliky ve spárované oblasti pro zotavení po havárii.

- **Geograficky redundantní úložiště pro přístup ke čtení** je geograficky redundantní úložiště a možnost číst data v sekundární oblasti. Tato schopnost je vhodná pro částečné zotavení po havárii. Pokud dojde k potížím s primární oblastí, můžete změnit aplikaci tak, aby měla přístup jen pro čtení do spárované oblasti.

### <a name="use-cases"></a>Případy použití

Každý typ úložiště má jiný případ použití.

#### <a name="blob-storage"></a>Blob Storage

Slovo *objekt blob* je zkratka pro *binární velký objekt*. Objekty BLOB jsou nestrukturované soubory, jako jsou soubory, které ukládáte do počítače. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů. Azure Blob storage také obsahuje datové disky virtuálních počítačů Azure.

Azure Storage podporuje tři druhy objektů blob:

- **Objekty BLOB bloku** se používají k uložení běžných souborů o velikosti až 195 GB (4 MB × 50 000 bloků). Objekty blob bloku se primárně používají pro úložiště souborů, které se čtou od začátku do konce, jako jsou například mediální soubory nebo soubory obrázků pro weby. Jsou pojmenovány objekty BLOB bloku, protože soubory větší než 64 MB musí být odeslány jako malé bloky. Tyto bloky jsou pak konsolidovány (nebo potvrzeny) do konečného objektu blob.

- **Objekty BLOB stránky** se používají k uložení souborů s náhodným přístupem o velikosti až 1 TB. Objekty BLOB stránky se používají především jako záložní úložiště pro virtuální disky, které poskytují trvalé disky pro virtuální počítače Azure, výpočetní službu IaaS v Azure. Označují se jako objekty blob stránky, protože poskytují náhodný přístup pro čtení/zápis k 512bajtovým stránkám.

- **Připojit objekty BLOB** se skládají z bloků, jako jsou objekty BLOB bloku, ale jsou optimalizované pro operace připojení. Ty se často používají pro protokolování informací z jednoho nebo více zdrojů do stejného objektu blob. Můžete například zapsat všechny protokolování trasování do stejného objektu blob připojení pro aplikaci, která běží na více virtuálních počítačích. Jeden doplňovací objekt blob může mít až 195 GB.

Další informace najdete [v tématu Začínáme s úložištěm objektů blob Azure pomocí rozhraní .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Azure File Storage je služba, která nabízí sdílené složky v cloudu pomocí standardního protokolu Server Message Block (SMB). Služba podporuje smb 2.1 a SMB 3.0. Díky úložišti souborů Azure můžete migrovat aplikace, které jsou závislé na sdílených složek, do Azure rychle a bez nákladných přepsání. Aplikace spuštěné na virtuálních počítačích Azure, v cloudových službách nebo od místních klientů můžou v cloudu připojit sdílenou složku. To je podobné tomu, jak desktopová aplikace připojí typickou sdílenou složku SMB. Potom může sdílenou složku File Storage připojit a používat libovolný počet aplikací.

Vzhledem k tomu, že sdílená složka úložiště souborů je standardní sdílená složka SMB, aplikace spuštěné v Azure mají přístup k datům ve sdílené složce prostřednictvím vstupně-nosových api systému souborů. Vývojáři proto mohou použít svůj stávající kód a dovednosti k migraci existujících aplikací. It profesionálové můžou pomocí rutin Prostředí PowerShell vytvářet, připojovat a spravovat sdílené složky úložiště souborů jako součást správy aplikací Azure.

Další informace najdete [v tématu Začínáme s úložištěm souborů Azure ve Windows](../../storage/files/storage-how-to-use-files-windows.md) nebo Jak používat úložiště souborů Azure s [Linuxem](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Úložiště Table

Azure Table Storage je služba, která ukládá strukturovaná data typu NoSQL v cloudu. Úložiště tabulek je úložiště klíčů a atributů s návrhem bez schématu. Vzhledem k tomu, že úložiště table je bez schématu, je snadné přizpůsobit data podle potřeby vaší aplikace vyvíjet. Přístup k datům je rychlý a nákladově efektivní pro všechny typy aplikací. Využívání úložiště Table Storage obvykle znamená výrazně nižší náklady než tradiční SQL pro podobné objemy dat.

Úložiště Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení a dalších typů metadat, které vaše služba vyžaduje. Do tabulky můžete uložit libovolný počet entit. Účet úložiště může obsahovat libovolný počet tabulek až do limitu kapacity účtu úložiště.

Další informace najdete [v tématu Začínáme s úložištěm Azure Table](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Úložiště Azure Queue zajišťuje cloudový přenos zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování jsou součásti aplikace často odděleny tak, aby je bylo možné škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Další informace najdete [v tématu Začínáme s úložištěm Fronty Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Nasazení účtu úložiště

Existuje několik možností pro nasazení účtu úložiště.

#### <a name="portal"></a>Portál

Nasazení účtu úložiště pomocí portálu Azure vyžaduje jenom aktivní předplatné Azure a přístup k webovému prohlížeči. Nový účet úložiště můžete nasadit do nové nebo existující skupiny prostředků. Po vytvoření účtu úložiště můžete pomocí portálu vytvořit kontejner s objektem blob nebo sdílenou složku. Entity úložiště tabulka a fronty můžete vytvořit programově. Další informace najdete v článku o [vytvoření účtu úložiště](../../storage/common/storage-account-create.md).

Kromě nasazení účtu úložiště z webu Azure Portal můžete nasadit šablonu Azure Resource Manager z portálu. Tím se nasadí a nakonfiguruje všechny prostředky definované v šabloně, včetně všech účtů úložiště. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a portálu Azure](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Nasazení účtu úložiště Azure pomocí PowerShellu umožňuje úplnou automatizaci nasazení účtu úložiště. Další informace najdete [v tématu Používání Azure PowerShellu s Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Kromě individuálního nasazení prostředků Azure můžete použít modul Azure PowerShell k nasazení šablony Azure Resource Manager. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Rozhraní příkazového řádku (CLI)

Stejně jako u modulu PowerShell poskytuje rozhraní příkazového řádku Azure automatizaci nasazení a dá se použít v systémech Windows, OS X nebo Linux. Pomocí příkazu vytvoření účtu účtu nastavení **příkazu** Azure CLI můžete vytvořit účet úložiště. Další informace najdete [v tématu použití azure cli s Azure Storage.](../../storage/common/storage-azure-cli.md)

Podobně můžete použít Azure CLI k nasazení šablony Azure Resource Manager. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Přístup a zabezpečení pro Azure Storage

K Azure Storage se přistupuje různými způsoby, včetně portálu Azure, během vytváření a provozu virtuálních počítačích a z knihoven klientských služeb úložiště.

#### <a name="virtual-machine-disks"></a>Disky virtuálních počítačů

Při nasazování virtuálního počítače je také potřeba vytvořit účet úložiště pro uložení disku operačního systému virtuálního počítače a všech dalších datových disků. Můžete vybrat existující účet úložiště nebo vytvořit nový. Vzhledem k tomu, že maximální velikost objektu blob je 1 024 GB, má jeden disk virtuálního počítače maximální velikost 1 023 GB. Chcete-li nakonfigurovat větší datový disk, můžete do virtuálního počítače prezentovat více datových disků a sdružovat je jako jeden logický disk. Další informace najdete v tématu "Správa disků Azure" pro [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) a [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Nástroje pro ukládání

K účtům úložiště Azure se dá přistupovat prostřednictvím mnoha různých průzkumníků úložiště, jako je Visual Studio Cloud Explorer. Tyto nástroje umožňují procházet účty úložiště a data. Další informace a seznam dostupných průzkumníků úložiště najdete v tématu [nástroje klienta Azure Storage](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Rozhraní API úložiště

Prostředky úložiště lze přistupovat libovolný jazyk, který může provádět požadavky HTTP/HTTPS. Azure Storage dále nabízí programovací knihovny pro několik oblíbených jazyků. Tyto knihovny zjednodušují práci s Azure Storage zpracováním podrobností, jako je synchronní a asynchronní vyvolání, dávkování operací, správa výjimek a automatické opakování. Další informace najdete v tématu [Azure Storage service REST API reference](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Přístupové klíče úložiště

Každý účet úložiště má dva ověřovací klíče, primární a sekundární. Buď lze použít pro operace přístupu k úložišti. Tyto klíče úložiště se používají k zabezpečení účtu úložiště a jsou vyžadovány pro programový přístup k datům. Existují dva klíče, které umožňují příležitostné převrácení klíčů pro zvýšení zabezpečení. Je důležité, aby klíče v bezpečí, protože jejich držení, spolu s názvem účtu, umožňuje neomezený přístup k libovolným datům v účtu úložiště.

#### <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Pokud potřebujete uživatelům povolit řízený přístup k prostředkům úložiště, můžete vytvořit sdílený přístupový podpis. Sdílený přístupový podpis je token, který lze připojit k adrese URL, která umožňuje delegovaný přístup k prostředku úložiště. Každý, kdo má token, má přístup k prostředku, na který odkazuje, s oprávněními, která určuje, po dobu, po kterou je platný. Další informace naleznete v tématu [Použití sdílených přístupových podpisů](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuální sítě jsou nezbytné pro podporu komunikace mezi virtuálními počítači. Můžete definovat podsítě, vlastní IP adresu, nastavení DNS, filtrování zabezpečení a vyrovnávání zatížení. Azure podporuje různé případy použití: sítě pouze pro cloud nebo hybridní virtuální sítě.

### <a name="cloud-only-virtual-networks"></a>Virtuální sítě pouze pro cloud

Virtuální síť Azure je ve výchozím nastavení přístupná jenom pro prostředky uložené v Azure. Prostředky připojené ke stejné virtuální síti mohou vzájemně komunikovat. Síťová rozhraní virtuálních počítačů a nástroje pro vyrovnávání zatížení můžete přidružit k veřejné IP adrese, aby byl virtuální počítač přístupný přes Internet. Můžete pomoci zabezpečit přístup k veřejně exponovaným prostředkům pomocí skupiny zabezpečení sítě.

![Virtuální síť Azure pro dvouvrstvou webovou aplikaci](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybridní virtuální sítě

Místní síť můžete připojit k virtuální síti Azure pomocí ExpressRoute nebo připojení VPN mezi lokalitami. V této konfiguraci virtuální síť Azure je v podstatě cloudové rozšíření vaší místní sítě.

Vzhledem k tomu, že virtuální síť Azure je připojena k místní síti, musí virtuální sítě mezi místními používat jedinečnou část adresního prostoru, který vaše organizace používá. Stejným způsobem, že různým podnikovým umístěním je přiřazena konkrétní podsíť IP, azure se stane jiným umístěním při rozšiřování sítě.
Existuje několik možností pro nasazení virtuální sítě.

- [Portál](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Rozhraní příkazového řádku (CLI)](../../virtual-network/quick-create-cli.md)

- Šablony Azure Správce prostředků

> **Kdy použít**: Kdykoli pracujete s virtuálními počítači v Azure, budete pracovat s virtuálními sítěmi. To umožňuje segmentaci virtuálních počítačů do veřejných a soukromých podsítí podobných místních datových center.
>
> **Začínáme**: Nasazení virtuální sítě Azure pomocí portálu Azure vyžaduje jenom aktivní předplatné Azure a přístup k webovému prohlížeči. Novou virtuální síť můžete nasadit do nové nebo existující skupiny prostředků. Když vytváříte nový virtuální počítač z portálu, můžete vybrat existující virtuální síť nebo vytvořit novou. Začínáme a [vytvořte virtuální síť pomocí portálu Azure](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Přístup a zabezpečení pro virtuální sítě

Můžete pomoci zabezpečit virtuální sítě Azure pomocí skupiny zabezpečení sítě. Sítě zabezpečení sítě obsahují seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zapírají síťový provoz na instance virtuálních počítačů ve virtuální síti. Skupiny nsg můžete přidružit k podsítím nebo jednotlivým instancívirtuálním virtuálním ms v rámci této podsítě. Když přidružíte skupinu síťových spojů k podsíti, pravidla acl se vztahují na všechny instance virtuálních virtuálních mkén v této podsíti. Kromě toho můžete dále omezit provoz na jednotlivé virtuální ms tím, že přisuzuje nsg přímo s tímto virtuálním cením. Další informace viz [Filtrování provozu sítě s použitím skupin zabezpečení sít](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Další kroky

- [Vytvoření virtuálního počítače s Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Vytvoření virtuálního počítače s Linuxem](../../virtual-machines/linux/quick-create-portal.md)
