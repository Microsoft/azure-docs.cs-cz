---
title: Příručka Začínáme pro operátory Azure IT | Dokumentace Microsoftu
description: Získání Příručka Začínáme pro operátory Azure IT
services: ''
documentationcenter: ''
author: themichaelbender-ms
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 08/24/2018
ms.author: mibender
ms.openlocfilehash: 28eeae8906480a5a160bfe11386da96b646f7427
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296852"
---
# <a name="get-started-for-azure-it-operators"></a>Začínáme pro operátory Azure IT

Tato příručka představuje základní koncepty související s nasazením a správa infrastruktury Microsoft Azure. Pokud jste ještě ke cloud computingu, nebo v Azure, tento průvodce pomůže rychle vám pomůžou začít s koncepty, nasazení a správu podrobnosti. Mnoho oddíly této příručky popisují určité operace, například nasazení virtuálního počítače a pak zadejte odkaz na hlubší technické podrobnosti.

## <a name="cloud-computing-overview"></a>Přehled cloud computingu

Cloud computingu, poskytuje moderní alternativu tradičních místních Datacenter. Dodavatelé veřejného cloudu poskytovat a spravovat všechny výpočetní infrastrukturu a software pro správu základní. Tyto dodavatelé poskytují celou řadu cloudových služeb. Cloudové služby v tomto případě pravděpodobně virtuální počítač, webový server nebo hostované v cloudu databázového stroje. Jako zákazník poskytovatele cloudu zapůjčení těchto cloudových služeb na základě podle potřeby. Přitom převedete investičních výdajů za údržbu hardwaru do provozní výdaje. Cloudová služba taky poskytuje následující výhody:

- Rychlé nasazení velkých výpočetních prostředí

- Rychlé zrušení přidělení systémů, které se už nevyžadují

- Snadné nasazení tradičně komplexních systémů, jako jsou nástroje pro vyrovnávání zatížení

- Schopnost poskytovat flexibilní výpočetní kapacity nebo určený počet číslic v případě potřeby

- Více nákladově efektivní výpočetních prostředí

- Přistupovat z libovolného místa s webový portál nebo Automatizace prostřednictvím kódu programu

- Cloudové služby, které splňují většina nároky na výpočetní prostředky a aplikace

Pomocí místní infrastruktury máte plnou kontrolu nad hardware a software, který je nasazený. V minulosti to vedlo k rozhodnutí o nákup hardwaru, které se zaměřují na vertikální navýšení kapacity. Příkladem je nákup serveru s více jádry podle potřeb výkonu ve špičce. Tato infrastruktura může bohužel nevyužité mimo časové období poptávky. S Azure můžete nasadit jenom infrastruktury potřebné a upravit toto směrem nahoru nebo dolů v každém okamžiku. To vede k zaměřením na horizontální navýšení kapacity až po nasazení dalších výpočetních uzlů k uspokojení Internetu věcí a potřebovali výkonu. Horizontální navýšení kapacity cloudových služeb začne být cenově výhodnější než vertikální navýšení kapacity pomocí drahého hardware.

Microsoft má mnoho datových centrech Azure po celém světě, nasadit další plánované. Kromě toho Microsoft roste suverénních cloudů v oblastech, jako je Číny a Německa. Pouze po největší globální podniky nasadit datových centrech tímto způsobem, aby pomocí Azure usnadňuje podnikům jakékoli velikosti pro nasazení služeb co nejblíž zákazníkům.

Pro malé firmy umožňuje Azure pro položku s nízkými náklady, s možností škálování rychle, jak poptávka po výpočetních zvýšení. To zabraňuje velké počáteční kapitálových investic do infrastruktury a poskytuje flexibilitu při navrhovat a podle potřeby upravovat architekturu systémy. Použití cloud computingu přizpůsobí s modelem Rychlé škálování a typu fail-fast spuštění růstu.

Další informace o dostupných oblastí Azure najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Cloudový model computingu

Azure využívá cloud computingu modelu na základě kategorií služby k dispozici zákazníkům. Tři kategorie služby patří infrastruktura jako služba (IaaS), platforma jako služba (PaaS) a Software jako služba (SaaS). Dodavatelé sdílení některých nebo všech odpovědnost za součástí výpočetní zásobníku v každém z těchto kategorií. Pojďme se podívat na jednotlivé kategorie pro cloud computingu.
![Cloud Computing porovnání zásobníku](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktura jako služba

Na dodavatele cloudu IaaS spouští a spravuje všechny fyzické výpočetní prostředky a požadovaný software, který chcete povolit virtualizaci počítače. Zákazník tato služba nasadí virtuální počítače v těchto hostovaných datacentrech. I když virtuální počítače jsou umístěné v datovém centru mimo pracoviště, uživatel IaaS má kontrolu nad konfiguraci a správu operačního systému byste museli opustit základní infrastruktury pro dodavatele cloudu.

Azure obsahuje několik řešení IaaS, včetně virtuálních počítačů, škálovací sady virtuálních počítačů a související síťové infrastruktury. Jsou virtuální počítače oblíbenými volbou pro počáteční migraci služeb na Azure, protože umožňuje modelu migrace "metodou lift and shift". Můžete nakonfigurovat virtuální počítač jako infrastrukturu aktuálně spuštěné služby ve vašem datovém centru a potom migrovat vašeho softwaru do nového virtuálního počítače. Možná budete muset provést aktualizace konfigurace, jako jsou adresy URL k jiným službám nebo úložiště, ale mnoho aplikací tímto způsobem můžete migrovat.

Škálovací sady virtuálních počítačů jsou postavené na Azure Virtual Machines a poskytují snadný způsob, jak nasadit clustery identických virtuálních počítačů. Škálovací sady virtuálních počítačů také podporují automatické škálování tak, aby nové virtuální počítače je možné nasadit automaticky, pokud to vyžaduje. Díky tomu škálovací sady virtuálních počítačů ideální platformu na vyšší úrovni mikroslužeb výpočetní clustery hostitelů, jako je Azure Service Fabric a Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platforma jako služba

Modelu paas nasazení aplikace do prostředí, které poskytuje dodavatel cloudové služby. Dodavatele nemá všechny správy infrastruktury, abyste se mohli soustředit na vývoj aplikací a data správy.

Azure poskytuje že několik PaaS compute nabídek, včetně funkcí Web Apps v Azure App Service a Azure Cloud Services (webové a pracovní role). V obou případech se vývojáři mají několik způsobů, jak nasazovat aplikace bez znalosti nic o nuts a bolty, které ji podporují. Vývojáři nemuseli k vytvoření virtuálních počítačů (VM), přihlaste se ke každé z nich pomocí protokolu RDP (Remote Desktop) nebo instalaci aplikace. Jen klikněte tlačítko (nebo ji zavřít) a nástroje poskytované společností Microsoft, zřídit virtuální počítače a pak nasaďte a instalaci aplikace na ně.

#### <a name="saas-software-as-a-service"></a>SaaS: Software jako služba

SaaS je software, který je centrálně hostované a spravované. Obvykle je založen na víceklientské architektury – jedna verze aplikace se používá pro všechny zákazníky. To lze škálovat na více instancí pro zajištění nejlepšího výkonu ve všech umístěních. SaaS software je obvykle licencován prostřednictvím měsíční nebo roční předplatné. SaaS software je obvykle licencován prostřednictvím měsíční nebo roční předplatné. Dodavatelé softwaru SaaS zodpovídají za všechny součásti softwaru zásobníku, takže všechno, co můžete spravovat služby poskytované.

Microsoft Office 365 je dobrým příkladem SaaS, nabízí. Předplatitelé zaplatit poplatek, měsíční nebo roční předplatné, a dostanou Microsoft Exchange, Microsoft OneDrive a zbývající sady Microsoft Office jako služba. Předplatitelé získají vždy na nejnovější verzi a serveru Exchange server se spravuje za vás. Ve srovnání s instalací a upgradem Office každý rok, to je levnější a vyžaduje menší úsilí.

## <a name="azure-services"></a>Služby Azure

Azure nabízí mnoho služeb v jeho platforma pro cloud computing. Tyto služby patří.

### <a name="compute-services"></a>Výpočetní služby

Služby hostování a spuštění aplikace úlohy:

- Azure Virtual Machines – Linux a Windows

- App Services (Web Apps, Mobile Apps, Logic Apps, API Apps a aplikace Function App)

- Azure Batch (pro rozsáhlých paralelních a dávkových výpočetních úloh)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Datové služby

Služby pro ukládání a správu dat:

- Azure Storage (zahrnuje služby Azure Blob, fronty, tabulky a souboru)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Redis Cache

### <a name="application-services"></a>Aplikační služby

Služby pro vytváření a provozování aplikací:

- Azure Active Directory (Azure AD)

- Azure Service Bus pro připojení distribuovaných systémů

- Azure HDInsight ke zpracování velkých objemů dat

- Azure Scheduler

- Azure Media Services

### <a name="network-services"></a>Síťové služby

Služby v rámci Azure i mezi datacentry Azure a místní sítě:

- Azure Virtual Network

- Azure ExpressRoute

- Poskytuje Azure DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Podrobnou dokumentaci na služby Azure, najdete v části [dokumentace ke službě Azure service](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Klíčové koncepty Azure

### <a name="datacenters-and-regions"></a>Datovými centry a oblastmi

Azure je globální Cloudová platforma, která je obecně dostupná v mnoha oblastech po celém světě. Když si zřídíte službu, aplikace nebo virtuálního počítače v Azure, budete vyzváni k výběru oblasti. Vybraná oblast představuje speciﬁc datacenter, ve kterém vaše aplikace spuštěná. Další informace najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).

Jednou z beneﬁts pomocí Azure je, že můžete nasadit aplikace do různých datových centrech po celém světě. Oblast, kterou zvolíte můžete aﬀect výkon vaší aplikace. Je optimální vyberte oblast, která je blíž ke nejvíce svým zákazníkům snížit latenci v síťové požadavky. Můžete také vybrat oblast pro splnění zákonných požadavků na distribuce aplikace v určitých zemích.

### <a name="azure-portal"></a>portál Azure

Na webu Azure portal je webová aplikace, který slouží k vytvoření, správě a odebrání prostředků Azure a služeb. Na webu Azure portal se nachází na [portal.azure.com](https://portal.azure.com). Zahrnuje přizpůsobitelný řídicí panel a nástroje pro správu prostředků Azure. Poskytuje také informace o fakturaci a předplatné. Další informace najdete v tématu [přehled portálu Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) a [Správa prostředků Azure prostřednictvím portálu](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Zdroje a prostředky

Prostředky Azure se jednotlivých výpočetních, sítí, dat nebo aplikace, který je hostitelem služby, které jsou nasazené do předplatného Azure. Mezi běžné prostředky patří virtuální počítače, účty úložiště nebo databáze SQL. Služby Azure se často skládají z několika souvisejících prostředků Azure. Virtuální počítač Azure může obsahovat například virtuální počítač, účet úložiště, síťový adaptér a veřejnou IP adresu. Tyto prostředky může vytvořit, spravované a odstranit samostatně nebo jako skupinu. Prostředky Azure se budeme věnovat jednotlivě podrobněji dále v této příručce.

### <a name="resource-groups"></a>Skupiny prostředků

Skupina prostředků Azure je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení, nebo jenom prostředky, které chcete spravovat jako skupinu. Skupiny prostředků Azure se budeme věnovat jednotlivě podrobněji dále v této příručce.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

Šablony Azure Resource Manageru je soubor JavaScript Object Notation (JSON), který definuje jeden nebo více prostředků k nasazení do skupiny prostředků. Definuje také závislosti mezi nasazených prostředků. Šablony Resource Manageru se budeme věnovat jednotlivě podrobněji dále v této příručce.

### <a name="automation"></a>Automation

Kromě vytváření, správu a odstraňování prostředků pomocí webu Azure portal můžete automatizovat tyto aktivity pomocí Powershellu nebo rozhraní příkazového řádku Azure (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Prostředí Azure PowerShell je sada modulů, které poskytují rutiny pro správu Azure. Rutiny můžete použít k vytvoření, správě a odebrání služby Azure. Rutiny vám umožňují dosáhnete konzistentní vzhledem k aplikacím, opakovatelným a bezobslužnou nasazení. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

#### <a name="azure-command-line-interface"></a>rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure je nástroj, který můžete použít k vytvoření, správě a odebrat prostředky Azure z příkazového řádku. Azure CLI je k dispozici pro Linux, Mac OS X a Windows. Další informace a technické podrobnosti najdete v tématu [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>Rozhraní REST API

Azure je postavený na sadu rozhraní REST API, která podporuje uživatelské rozhraní portálu Azure. Většina těchto rozhraní REST API jsou podporovány také umožňuje programově zřizovat a spravovat vaše prostředky Azure a aplikace z jakéhokoli zařízení využívajících Internet. Další informace najdete v tématu [Reference k rozhraní Azure REST SDK](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Správci mohou přistupovat k prostředí Azure PowerShell a rozhraní příkazového řádku Azure prostřednictvím prohlížeče přístupné prostředí volá Azure Cloud Shell. Toto interaktivní rozhraní poskytuje flexibilní nástroj pro systémy Linux a Windows správce používat jejich rozhraní příkazového řádku podle vlastní volby, Bashe nebo Powershellu. Azure Cloud Shell lze přistupovat pomocí portálu, jako samostatné webové rozhraní na [shell.azure.com](https://shell.azure.com), nebo z mnoha jiných přístupových bodů. Další informace najdete v tématu [Přehled služby Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Předplatná Azure

Předplatné je logické seskupení služeb Azure, který je propojený s účtem Azure. Jeden účet Azure může obsahovat několik předplatných. Fakturace za služby Azure se provádí na základě za předplatné. Předplatná Azure mít účet správce, který má plnou kontrolu nad předplatné, a Správce služby, který má kontrolu nad všechny služby v rámci předplatného. Informace o správcích klasickém modelu předplatného najdete v tématu [přidat nebo změnit správce předplatného Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Kromě správci, lze udělit jednotlivým účtům podrobné řízení prostředků Azure pomocí [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Vybrat a povolit předplatné Azure

Než začnete pracovat se službami Azure, musíte mít předplatné. Několik typů předplatného jsou k dispozici.

**Bezplatné účty**: odkaz pro registraci bezplatného účtu je na [web Azure](https://azure.microsoft.com/). Získáte kredit v průběhu 30 dnů vyzkoušet libovolnou kombinaci prostředků v Azure. Při překročení kreditu, který vaše, váš učet je zablokovaný. Na konci zkušebního období vaše služby jsou vyřazeny z provozu a přestane fungovat. Můžete upgradovat na předplatné s průběžnými platbami kdykoli.

**Předplatná MSDN**: Pokud máte předplatné MSDN, můžete získat určitou velikostí v kreditech Azure každý měsíc. Například pokud máte Microsoft Enterprise sady Visual Studio s předplatným MSDN získáte \$150 USD v kreditech Azure.

Při překročení kreditu, který, služby jsou zakázané, dokud nezačne další měsíc. Můžete vypnout limit útraty a přidat platební kartu pro další náklady. Některé tyto náklady Dáváme pro účty MSDN. Například platíte cenu Linux pro virtuální počítače s Windows serverem a neexistuje žádné další poplatky za servery společnosti Microsoft, jako je Microsoft SQL Server. Díky tomu MSDN účty ideální pro scénáře vývoje a testování.

**Účty BizSpark**: programu The Microsoft BizSpark má spoustu výhod pro začínající firmy. Jednou z těchto výhod je přístup k veškerému softwaru Microsoftu pro vývojová a testovací prostředí pro účty až o pěti MSDN. Získejte 150 USD v kreditech Azure pro každou z těchto pět MSDN účtů a platit nižší sazby pro několik služeb Azure, jako jsou virtuální počítače.

**Průběžné platby**: U tohoto předplatného, platíte za použití připojením kreditní nebo debetní kartu k účtu. Pokud organizace, můžete je také schválené pro fakturaci.

**Smlouvy Enterprise**: se smlouvou enterprise agreement, můžete zavázat k využívání počet služeb v Azure přes příští rok, a vy platíte šířka předem. Závazek, který se spotřebovává během roku. Pokud překročíte závazku odečítá, můžete platit Nadlimitní využití za uplynulé čtvrtletí. V závislosti na množství závazku Získejte slevu na služby v Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Udělit přístup správce k předplatnému Azure

RBAC má několik předdefinovaných rolí, které vám umožní přiřadit oprávnění. Chcete-li uživatel správcem předplatného Azure, přiřaďte jim [vlastníka](../../role-based-access-control/built-in-roles.md#owner) role v oboru předplatného. Role vlastníka poskytuje úplný přístup uživatelů ke všem prostředkům v rámci předplatného, včetně práva na delegovat přístup ostatním uživatelům.

Další informace najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Zobrazovat fakturační informace na webu Azure Portal

Důležitou součástí pomocí Azure je možnost zobrazit informace o fakturaci. Na webu Azure portal nabízí podrobný pohled na Azure fakturační údaje.

Další informace najdete v tématu [ke stažení Azure billing invoice a denní data o využití](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Získat fakturační údaje z rozhraní API pro fakturaci

Kromě zobrazování fakturace na portálu, přístupné pomocí skriptu nebo programu prostřednictvím rozhraní REST API fakturace Azure fakturační údaje:

- Rozhraní API využití Azure můžete použít k načtení data o využití. Označování související prostředky Azure, lze optimalizovat fakturační informace o využití. Můžete například označit všechny prostředky ve skupině prostředků s názvem oddělení nebo název projektu a pak je sledovat náklady speciálně pro tuto jednu značku.

- Rozhraní API Azure míra karty můžete vypíše všechny dostupné prostředky, spolu s metadata a ceny informace o každém z těchto prostředků.

Další informace najdete v tématu [získání přehledů o spotřebě prostředků Microsoft Azure](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognózy náklady pomocí cenové kalkulačky

Ceny pro každou službu v Azure se liší. Mnoho služeb Azure nabízí úrovní Basic, Standard a Premium. Každá úroveň má obvykle několik úrovní cenou a výkonem. S použitím [online cenová Kalkulačka](http://azure.microsoft.com/pricing/calculator), odhady cen je možné vytvořit. Kalkulačce zahrnuje flexibilitu pro odhad nákladů na jeden prostředek nebo skupinu prostředků.

### <a name="set-up-billing-alerts"></a>Nastavení upozornění fakturace

Po nasazení aplikace nebo řešení v Azure můžete vytvořit výstrahy, které odesílání že e-mailu při přístupu limitech útraty stanovenými výstrahu. Další informace najdete v tématu [nastavení upozornění pro předplatná Microsoft Azure fakturace](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager je nasazení, správu a organizace mechanismus pro prostředky Azure. Pomocí Resource Manageru můžete shromáždit mnoho jednotlivé prostředky ve skupině prostředků.

Resource Manageru také zahrnuje možnosti nasazení, které umožňují přizpůsobit nasazení a konfigurace související prostředky. Například pomocí Resource Manageru můžete nasadit aplikaci, která se skládá z několika virtuálních počítačů, nástroj pro vyrovnávání zatížení a SQL database jako jeden celek. Vývoj těchto nasazení pomocí šablony Resource Manageru.

Resource Manager poskytuje několik výhod:

- Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

- Můžete opakovaně nasadit v průběhu životního cyklu vývoje řešení a mít přitom jistotu, že se prostředky nasadí konzistentně.

- Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

- Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

- Řízení přístupu můžete použít ke všem službám ve vaší skupině prostředků a protože RBAC je nativně integrováno do platformy pro správu.

- Použít značky u prostředků logicky tak uspořádat všechny prostředky ve vašem předplatném.

- Můžete zpřehlednit fakturaci svojí organizace zobrazením nákladů na skupinu prostředků, které sdílejí stejnou značku.

### <a name="tips-for-creating-resource-groups"></a>Tipy pro vytváření skupiny prostředků

Když děláte rozhodnutí o vaší skupiny prostředků, vezměte v úvahu tyto tipy:

- Všechny prostředky ve skupině prostředků by měl mít stejný životní cyklus.

- Prostředek můžete přiřadit jenom jedné skupiny najednou.

- Můžete přidat nebo odebrat zdroje ze skupiny prostředků kdykoli. Každý prostředek musí patřit do skupiny prostředků. Takže pokud odeberete prostředek z jedné skupiny, musíte jej přidat do druhé.

- Většina typů prostředků můžete přesunout do jiné skupiny prostředků kdykoli.

- Prostředky ve skupině prostředků mohou být v různých oblastech.

- Skupinu prostředků můžete použít k řízení přístupu pro prostředky v ní.

### <a name="building-resource-manager-templates"></a>Vytváření šablon Resource Manageru

Šablony Resource Manageru pomocí deklarace definice prostředků a konfigurace prostředků, které se nasadí do jedné skupiny prostředků. Šablony Resource Manageru můžete orchestrovat složité nasazení bez nutnosti skriptování nadbytečná nebo ruční konfigurace. Poté, co při vývoji šablony, můžete ji nasadit více než jednou – pokaždé, když s nejistým identické.

Šablony Resource Manageru se skládá ze čtyř částí:

- **Parametry**: Toto jsou vstupy nasazení. Hodnoty parametru lze zadat člověk nebo automatizovaného procesu. Jako příklad parametr může být správce uživatelské jméno a heslo pro virtuální počítač s Windows. Hodnoty parametrů se používají v rámci nasazení, když jste zadali.

- **Proměnné**: ty se používají pro uchování hodnoty, které se používají v rámci nasazení. Na rozdíl od parametry hodnotu proměnné není k dispozici v době nasazení. Místo toho je obtížné kódované nebo generuje dynamicky.

- **Prostředky**: v této části šablony definuje prostředky k nasazení, jako jsou virtuální počítače, účty úložiště a virtuální sítě.

- **Výstup**: Po dokončení nasazení Resource Manageru můžete vrátit data, jako jsou dynamicky generované připojovací řetězce.

Jsou k dispozici pro automatizaci nasazení následujících mechanismů:

- **Funkce**: můžete použít několik funkcí v šablonách Resource Manageru. Patří mezi ně operací, jako je například převod řetězce na malá písmena, nasazení více instancí definovaných prostředků a dynamicky vrácení cílová skupina prostředků. Funkce Resource Manageru vám pomůžou rozvíjet dynamických nasazení.

- **Závislosti prostředků**: když nasazujete více zdrojů, některé prostředky budou mít závislost na ostatní. Pro usnadnění nasazení, můžete použít deklarace závislostí tak, aby ostatní jsou nasazena závislé prostředky.

- **Propojení šablony**: Z v rámci jedné šablony Resource Manageru můžete propojit s jinou šablonu. To umožňuje rozložené nasazení na sadu cílových, zaměřené na konkrétní účel šablony.

Můžete vytvářet šablony Resource Manageru v libovolném textovém editoru. Ale sady Azure SDK for Visual Studio obsahuje nástroje, které vám pomohou. Pomocí sady Visual Studio můžete přidat prostředky do šablony v průvodci, potom nasadit a ladit šablonu přímo z Visual Studia. Další informace najdete v tématu [šablon pro vytváření Azure Resource Manageru](../../resource-group-authoring-templates.md).

Nakonec můžete převést existující skupiny prostředků opakovaně použitelné šablony z portálu Azure portal. To může být užitečné, pokud chcete vytvořit šablonu nasadit existující skupinu prostředků, nebo chcete jenom zkontrolovat základní JSON. Pokud chcete exportovat skupiny prostředků, vyberte **automatizační skript** tlačítko Nastavení skupiny prostředků.

## <a name="security-of-azure-resources-rbac"></a>Zabezpečení prostředků Azure (RBAC)

Můžete udělit provozní přístup k uživatelským účtům v zadaném oboru: předplatné, skupinu prostředků nebo samostatný prostředek. To znamená, že můžete nasadit sadu prostředků do skupiny prostředků, jako je například virtuální počítač a všechny související prostředky a udělení oprávnění pro konkrétního uživatele nebo skupiny. Tento přístup omezuje přístup k prostředkům, které patří k cílové skupině prostředků. Můžete také udělit přístup na jeden prostředek, jako je například virtuální počítač nebo virtuální sítě.

Pokud chcete udělit přístup, přiřadíte roli pro uživatele nebo skupiny uživatelů. Existuje mnoho předdefinovaných rolí. Můžete také definovat vlastní role.

Tady je příklad několika [předdefinované role v Azure](../../role-based-access-control/built-in-roles.md):

- **Vlastník**: uživatel s touto rolí můžou spravovat všechno včetně přístupu.

- **Čtečka**: uživatel s touto rolí může číst prostředky všech typů (s výjimkou tajné kódy), ale nemůže provádět změny.

- **Přispěvatel virtuálních počítačů**: uživatel s touto rolí může spravovat virtuální počítače, ale nemůže spravovat virtuální sítě pro které jsou připojené nebo účet úložiště, kde se nachází soubor virtuálního pevného disku.

- **Přispěvatel databází SQL**: uživatel s touto rolí můžou spravovat databáze SQL, ale ne jejich zásady související se zabezpečením.

- **Správce zabezpečení SQL**: uživatel s touto rolí můžou spravovat zásady související se zabezpečením SQL serverů a databází.

- **Přispěvatel účtů úložiště**: uživatel s touto rolí můžou spravovat účty úložiště, ale nemůžete spravovat přístup k účtům úložiště.

Další informace najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines je jedním z centrální služby IaaS v Azure. Azure Virtual Machines podporuje nasazení virtuálních počítačů s Windows nebo Linuxem v datacentru Microsoft Azure. S Azure Virtual Machines, mají úplnou kontrolu nad konfigurací virtuálních počítačů a odpovídáte za všechny instalace softwaru, konfigurace a údržby.

Při nasazení virtuálního počítače Azure, můžete vybrat image z Azure Marketplace, nebo je můžete zadat vlastní generalizované image. Tato image se používá k aplikování operačního systému a počáteční konfiguraci. Během nasazení bude Resource Manageru zpracovávat některá nastavení konfigurace, jako je například přiřazení názvu počítače, pověření pro správu a konfiguraci sítě. Rozšíření virtuálního počítače Azure můžete použít k automatizaci další konfigurace, jako je instalace softwaru, antivirové konfiguraci a monitorování řešení.

Virtuální počítače můžete vytvořit mnoho různých velikostí. Velikost virtuálního počítače určuje přidělení prostředků, jako je zpracování, paměť a kapacitu úložiště. V některých případech jsou dostupné pouze určité velikosti virtuálních počítačů s konkrétní funkce, jako je RDMA povolené síťové adaptéry nebo disky SSD. Úplný seznam velikostí virtuálních počítačů a možnosti najdete v části "Velikosti virtuálních počítačů v Azure" pro [Windows](../../virtual-machines/windows/sizes.md) a [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Případy použití

Protože virtuální počítače Azure nabízejí plnou kontrolu nad konfigurací, jsou ideální pro širokou škálu úloh serveru, které se nehodí do modelu PaaS. Jiné úlohy serveru jako jsou třeba servery databáze (SQL Server, Oracle nebo MongoDB), Windows Server Active Directory, Microsoft SharePoint a mnoha dalších stát možné spustit na platformě Microsoft Azure. V případě potřeby můžete tyto úlohy přesunout z místního datacentra do jedné nebo několika oblastech Azure, bez velké množství Rekonfigurace.

### <a name="deployment-of-virtual-machines"></a>Nasazení virtuálních počítačů

Virtuální počítače Azure můžete nasadit pomocí webu Azure portal, pomocí modulu Powershellu pro Azure automation nebo pomocí automatizace pomocí rozhraní příkazového řádku pro různé platformy.

#### <a name="portal"></a>Portál

Nasazení virtuálního počítače pomocí webu Azure portal vyžaduje jenom aktivní předplatné Azure a přístup k webovému prohlížeči. Můžete vybrat celou řadu imagí různých operačních systémů s různými konfiguracemi. Všechny požadavky na síť a úložiště jsou nakonfigurované během nasazení. Další informace viz "Vytvoření virtuálního počítače na webu Azure Portal" pro [Windows](../../virtual-machines/windows/quick-create-portal.md) a [Linux](../../virtual-machines/linux/quick-create-portal.md).

Kromě nasazení virtuálního počítače z portálu Azure portal, můžete nasadit šablonu Azure Resource Manageru z portálu. To nasadí a nakonfiguruje všechny prostředky, jak jsou definovány v šabloně. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a webu Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Nasazení virtuálního počítače Azure s použitím prostředí PowerShell umožňuje automatizaci kompletního nasazení ve všech prostředcích související virtuálního počítače, včetně úložiště a sítě. Další informace najdete v tématu [vytvoření virtuálního počítače s Windows pomocí Resource Manageru a Powershellu](../../virtual-machines/windows/quick-create-powershell.md).

Kromě individuálně nasazení výpočetní prostředky Azure, můžete použít modul Azure PowerShell k nasazení šablony Azure Resource Manageru. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Rozhraní příkazového řádku (CLI)

Stejně jako u modulu prostředí PowerShell, rozhraní příkazového řádku Azure poskytuje automatizaci nasazení a můžou používat v systémech Windows, OS X nebo Linux. Pokud používáte rozhraní příkazového řádku Azure **rychlé vytvoření virtuálního počítače** příkaz a všechny související prostředky virtuálního počítače (včetně úložiště a sítě) a celý virtuální počítač se nasadí. Další informace najdete v tématu [vytvoření virtuálního počítače s Linuxem v Azure pomocí rozhraní příkazového řádku](../../virtual-machines/linux/quick-create-cli.md).

Podobně můžete použít rozhraní příkazového řádku Azure k nasazení šablony Azure Resource Manageru. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Přístup a zabezpečení pro virtuální počítače

Přístup k virtuálnímu počítači z Internetu vyžaduje přidružená síťová rozhraní, nebo pokud je k dispozici, nakonfigurovat veřejnou IP adresu nástroje pro vyrovnávání zatížení. Veřejná IP adresa obsahuje název DNS, který se přeložit na virtuální počítač nebo nástroj pro vyrovnávání zatížení. Další informace najdete v tématu [IP adresách v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Můžete spravovat přístup k virtuálnímu počítači přes veřejnou IP adresu pomocí síťového zabezpečení skupiny (NSG) prostředku. Skupina zabezpečení sítě funguje jako brána firewall a povoluje nebo odepírá provoz přes rozhraní sítě nebo podsítě na sadu definované porty. Například vytvořit relaci vzdálené plochy s Virtuálním počítači Azure, budete muset nakonfigurovat skupinu zabezpečení sítě, které povolí příchozí provoz na portu 3389. Další informace najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí webu Azure portal](../../virtual-machines/windows/nsg-quickstart-portal.md).

A konečně stejně jako u správy všechny počítače, byste měli poskytnout zabezpečení pro virtuální počítač Azure na operační systém pomocí pověření zabezpečení a software brány firewall.

## <a name="azure-storage"></a>Azure Storage

Azure Storage je služba spravovaná Microsoftem, která poskytuje odolné, škálovatelné a redundantní úložiště. Účet úložiště Azure můžete přidat jako prostředek do libovolné skupiny prostředků pomocí libovolné metody nasazení prostředků. Azure obsahuje čtyři typy úložišť: Blob storage, File Storage, Table storage a Queue storage. Při nasazení účtu úložiště, dva typy účtů jsou k dispozici, pro obecné účely a úložiště objektů blob. Účet úložiště pro obecné účely poskytuje přístup pro všechny čtyři typy úložiště. Účty úložiště BLOB jsou podobné účtům pro obecné účely, ale obsahovat specializované objekty BLOB, které zahrnují přístup horké a studené úrovně. Další informace o úložišti objektů blob najdete v tématu [úložiště objektů Blob v Azure](../../storage/blobs/storage-blob-storage-tiers.md).

Účty úložiště Azure můžete nakonfigurovat různé úrovně redundance:

- **Místně redundantní úložiště** poskytuje vysokou dostupnost díky zajištění, že tři kopie všech dat probíhají synchronně předtím, než se zápis se bude považovat za úspěšné. Tyto kopie jsou uloženy v jednoho zařízení v jedné oblasti. Repliky jsou umístěny v samostatných doménách selhání a upgradovacími doménami. To znamená, že data jsou k dispozici i v případě, že uzel úložiště, který drží vašich dat selže nebo je převedeno do režimu offline aktualizaci.

- **Geograficky redundantní úložiště** vytvoří tři synchronních kopií dat v primární oblasti pro zajištění vysoké dostupnosti a potom asynchronně provádí tři repliky v spárované oblasti pro zotavení po havárii.

- **Geograficky redundantní úložiště jen pro čtení** je geograficky redundantní úložiště, a navíc schopnost číst data v sekundární oblasti. Tato možnost je vhodná k částečné zotavení po havárii. Pokud dojde k problému s primární oblastí, můžete změnit vaše aplikace měla přístup jen pro čtení pro spárované oblasti.

### <a name="use-cases"></a>Případy použití

Každý typ úložiště má případ použití v odlišných.

#### <a name="blob-storage"></a>Blob Storage

Slovo *blob* je zkratka pro *binární rozsáhlý objekt*. Objekty BLOB jsou nestrukturovaných soubory podobné těm, které ukládáte ve vašem počítači. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů. Azure Blob storage taky obsahuje datové disky Azure Virtual Machines.

Azure Storage podporuje tři typy objektů blob:

- **Objekty BLOB bloku** se používají k uložení obyčejných souborů až 195 GB velikosti (4 MB × 50 000 bloků). Případem primárního použití pro objekty BLOB bloku je úložiště souborů, které jsou čtení od začátku do konce, jako je například mediální soubory nebo soubory obrázků pro weby. Protože soubory větší než 64 MB musí být odeslán jako malé bloky jsou pojmenovány objekty BLOB bloku. Tyto bloky jsou pak konsolidovat (nebo potvrzené) do konečného objektu blob.

- **Objekty BLOB stránky** se používají k uložení náhodný přístup soubory ve velikosti až 1 TB. Objekty BLOB stránky se používají především jako záložní úložiště pro virtuální pevné disky, které poskytují trvalé disky pro virtuální počítače Azure, IaaS výpočetní služby v Azure. Objekty BLOB stránky jsou pojmenovány protože poskytují přístup k náhodné čtení a zápis na stránky o velikosti 512 bajtů.

- **Doplňovací objekty BLOB** skládají z bloků, jako jsou objekty BLOB bloku, ale jsou optimalizované pro doplňovací operace. Často používají k protokolování informací z jednoho nebo více zdrojů do stejného objektu blob. Například můžete zapsat všechny vaše protokolování trasování do stejné doplňovací objekt blob pro aplikaci, která běží na několika virtuálních počítačů. Jeden doplňovací objekt blob může být až 195 GB.

Další informace najdete v tématu [Začínáme s Azure Blob storage pomocí .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Azure File storage je služba, která nabízí sdílené složky v cloudu s využitím standardního protokolu zprávy bloku SMB (Server). Služba podporuje SMB 2.1 i SMB 3.0. Se službou Azure File storage můžete migrovat aplikace, které spoléhají na sdílené složky Azure, rychle a bez nákladných přepisů. Aplikace běžící na virtuálních počítačích Azure, v cloudových službách nebo z místních klientů připojit sdílenou složku v cloudu. To se podobá jak desktopová aplikace připojí typickou sdílenou složku SMB. Potom může sdílenou složku File Storage připojit a používat libovolný počet aplikací.

Protože sdílená složka File storage je standardní sdílené složky SMB, aplikace běžící v Azure můžou k datům ve sdílené složce přistupovat přes rozhraní API vstupně-výstupních operací systému souborů. Vývojáři tedy můžete použít své dovednosti a znalosti kódu při migraci stávajících aplikací. IT specialisté můžou použít rutiny prostředí PowerShell k vytváření, připojování a správě sdílených složek File storage v rámci správy aplikací Azure.

Další informace najdete v tématu [Začínáme s Azure File storage ve Windows](../../storage/files/storage-how-to-use-files-windows.md) nebo [postupy používání Azure File storage s Linuxem](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Úložiště Table

Azure Table Storage je služba, která ukládá strukturovaná data typu NoSQL v cloudu. Úložiště Table je úložiště klíčů/atributů s návrhem bez schématu. Table storage je bez schématu, je snadné data přizpůsobovat měnícím potřebám vaší aplikace. Přístup k datům je rychlý a cenově výhodný pro všechny typy aplikací. Využívání úložiště Table Storage obvykle znamená výrazně nižší náklady než tradiční SQL pro podobné objemy dat.

Úložiště Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení a dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit. Účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

Další informace najdete v tématu [Začínáme s Azure Table storage](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Úložiště Azure Queue zajišťuje cloudový přenos zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování, součásti aplikací často oddělené, aby se mohly škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Další informace najdete v tématu [Začínáme s Azure Queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Nasazení účtu úložiště

Existuje několik možností pro nasazení účtu úložiště.

#### <a name="portal"></a>Portál

Nasazení účtu úložiště pomocí webu Azure portal vyžaduje jenom aktivní předplatné Azure a přístup k webovému prohlížeči. Nový účet úložiště můžete nasadit do skupiny nové nebo existující prostředek. Po vytvoření účtu úložiště, můžete vytvořit objektů blob v kontejneru nebo ve sdílené složce pomocí portálu. Můžete vytvářet tabulky a fronty úložiště entity prostřednictvím kódu programu. Další informace najdete v tématu [vytvořit účet úložiště](../../storage/common/storage-quickstart-create-account.md).

Kromě nasazení účtu úložiště na webu Azure Portal, můžete nasadit šablonu Azure Resource Manageru z portálu. To nasadí a nakonfiguruje všechny prostředky, jak jsou definovány v šabloně, včetně účtů úložiště. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a webu Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Nasazení účtu služby Azure storage s použitím prostředí PowerShell umožňuje automatizaci kompletního nasazení účtu úložiště. Další informace najdete v tématu [pomocí Azure Powershellu s Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Kromě individuálně nasazení prostředků Azure, můžete použít modul Azure PowerShell k nasazení šablony Azure Resource Manageru. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Rozhraní příkazového řádku (CLI)

Stejně jako u modulu prostředí PowerShell, rozhraní příkazového řádku Azure poskytuje automatizaci nasazení a můžou používat v systémech Windows, OS X nebo Linux. Můžete použít rozhraní příkazového řádku Azure **vytvořit účet úložiště** příkaz k vytvoření účtu úložiště. Další informace najdete v tématu [pomocí Azure CLI s Azure Storage.](../../storage/common/storage-azure-cli.md)

Podobně můžete použít rozhraní příkazového řádku Azure k nasazení šablony Azure Resource Manageru. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Přístup a zabezpečení pro službu Azure Storage

Úložiště Azure přistupuje různými způsoby, včetně i když na webu Azure portal, při vytváření virtuálního počítače a operaci a z klientských knihoven pro úložiště.

#### <a name="virtual-machine-disks"></a>Disky virtuálních počítačů

Když nasazujete virtuální počítač, musíte také vytvořit účet úložiště pro uložení disku s operačním systémem virtuálního počítače a všechny další datové disky. Můžete vybrat existující účet úložiště nebo vytvořte novou. Vzhledem k tomu, že 1 024 GB je maximální velikost objektu blob, jeden virtuální počítač disk má maximální velikost 1,023 GB. Ke konfiguraci větší datový disk, můžete k dispozici více datových disků k virtuálnímu počítači a fondu je společně jako jednu logického disku. Další informace najdete v části "Správa disků v Azure" pro [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) a [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Nástroje pro úložiště

Účty úložiště Azure přistupuje prostřednictvím mnoha průzkumníci jiného úložiště, jako je například Visual Studio Cloud Explorer. Tyto nástroje umožňují procházet účty úložiště a data. Další informace a seznam průzkumníci úložiště k dispozici, najdete v části [klientské nástroje pro Azure Storage](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>API služby Storage

Úložiště prostředků je možný v jakémkoliv jazyce, který umí vytvářet požadavky HTTP/HTTPS. Azure Storage dále nabízí programovací knihovny pro několik oblíbených jazyků. Tyto knihovny zjednodušují práci s Azure Storage pomocí drobnosti jako synchronní a asynchronní vyvolání, dávkování operací, řízení výjimek a automatické opakování pokusů. Další informace najdete v tématu [odkaz na rozhraní REST API pro službu Azure Storage](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Přístupové klíče k úložišti

Každý účet úložiště má dva ověřovací klíče, primární a sekundární lokalitou. Buď je možné pro operací přístupu k úložišti. Tyto klíče úložiště se používají k zabezpečení účtu úložiště a jsou požadovány pro programový přístup k datům. Existují dva klíče, abyste povolili občasné výměny klíčů pro zvýšení zabezpečení. Je důležité k lepšímu zabezpečení klíče, protože jejich vlastnictví spolu s názvem účtu umožňuje neomezený přístup k žádným datům v účtu úložiště.

#### <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Pokud je potřeba povolit uživatelům mít řízený přístup ke svým prostředkům úložiště, můžete vytvořit sdílený přístupový podpis. Sdílený přístupový podpis je token, který lze připojit k URL a který umožní Delegovaný přístup k prostředku úložiště. Každý, kdo má token, který může přistupovat k prostředku, který odkazuje na s oprávněními, určuje, pro časový interval, že je platný. Další informace najdete v tématu [použití sdílených přístupových podpisů](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuální sítě jsou nezbytné pro podporu komunikace mezi virtuálními počítači. Můžete definovat podsítě, vlastní IP adresu, nastavení DNS, zabezpečení, filtrování a vyrovnávání zatížení. Azure podporuje různá použití případů: čistě cloudové sítě nebo hybridní virtuální sítě.

### <a name="cloud-only-virtual-networks"></a>Výhradně cloudový virtuální sítě

Virtuální síť Azure, ve výchozím nastavení, budou přístupné jenom pro prostředky uložené v Azure. Prostředky, které jsou připojeny ke stejné virtuální síti komunikovat mezi sebou. Můžete přidružit síťových rozhraní virtuálního počítače a služby s veřejnou IP adresu virtuálního počítače zpřístupnit přes Internet Vyrovnávání zatížení. Pomocí skupiny zabezpečení sítě můžete líp zabezpečit přístup do veřejně vystavené prostředky.

![Azure Virtual Network úrovně 2 webové aplikace](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybridní virtuální sítě

Místní sítě můžete připojit ke službě Azure virtual network s využitím ExpressRoute nebo připojení site-to-site VPN. V této konfiguraci virtuální sítě Azure je v podstatě založené na cloudu rozšíření místní sítě.
![Hybridní virtuální síti pomocí sítě VPN](https://docs.microsoft.com/azure/architecture/reference-architectures/_images/blueprints/hybrid-network-vpn.png)

Vzhledem k tomu, že virtuální síť Azure je připojený k vaší místní síti, mezi různými místy, že virtuální sítě musíte použít jedinečný části adresního prostoru, který vaše organizace používá. Stejným způsobem, který různých sídlech jsou přiřazeny konkrétní podsíť protokolu IP Azure stane jinam, jak můžete svoji síť rozšířit.
Existuje několik možností pro nasazení virtuální sítě.

- [Azure Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Rozhraní příkazového řádku (CLI)](../../virtual-network/quick-create-cli.md)

- Šablony Azure Resource Manageru

>**Kdy použít**: když pracujete s virtuálními počítači v Azure, budete pracovat s virtuálními sítěmi. To umožňuje segmentace vašich virtuálních počítačů do podsítí veřejných a privátních podobné místních datových centrech.

>**Začínáme**: nasazení služby Azure virtual network pomocí webu Azure portal vyžaduje jenom aktivní předplatné Azure a přístup k webovému prohlížeči. Nové virtuální sítě můžete nasadit do skupiny nové nebo existující prostředek. Při vytváření nového virtuálního počítače z portálu, můžete vybrat existující virtuální síť nebo vytvořte novou. Začínáme a [vytvořit virtuální síť pomocí webu Azure portal](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Přístup a zabezpečení pro virtuální sítě

Zabezpečení virtuální sítě Azure může pomoct s použitím skupiny zabezpečení sítě. Skupiny Nsg obsahují seznam pravidel seznamu ACL řízení přístupu, která povolují nebo odpírají síťový provoz do vašich instancí virtuálních počítačů ve virtuální síti. Skupiny Nsg můžete přidružit podsítě nebo jednotlivých instancí virtuálních počítačů v této podsíti. Pokud přidružíte skupinu NSG k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti. Kromě toho můžete dále omezit provoz do konkrétního virtuálního počítače tím, že přidružíte skupinu NSG přímo se tento virtuální počítač. Další informace najdete v tématu [filtrování provozu sítě s použitím skupin zabezpečení sítě](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Další postup

- [Vytvoření virtuálního počítače Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Vytvoření virtuálního počítače s Linuxem](../../virtual-machines/linux/quick-create-portal.md)
