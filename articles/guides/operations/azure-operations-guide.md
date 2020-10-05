---
title: Úvodní příručka pro operátory IT Azure | Microsoft Docs
description: Úvodní příručka pro operátory IT Azure
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 0f67939358a4395416ea28c1c459d4890ab0351d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91397806"
---
# <a name="get-started-for-azure-it-operators"></a>Začínáme s operátory IT Azure

Tato příručka přináší základní koncepty související s nasazením a správou infrastruktury Microsoft Azure. Pokud začínáte s cloud computingem nebo samotným Azure, tato příručka vám pomůže rychle začít s koncepty, nasazením a podrobnostmi o správě. Mnoho částí této příručky popisuje operace, jako je například nasazení virtuálního počítače, a pak poskytuje odkaz na podrobné technické podrobnosti.

## <a name="cloud-computing-overview"></a>Přehled cloud computingu

Cloud Computing nabízí moderní alternativu k tradičnímu místnímu datovému centru. Dodavatelé veřejného cloudu poskytují a spravují veškerou výpočetní infrastrukturu a základní software pro správu. Tito dodavatelé poskytují širokou škálu cloudových služeb. Cloudová služba v tomto případě může být virtuálním počítačem, webovým serverem nebo databázovým strojem hostovaným v cloudu. Jako zákazník poskytovatele cloudu tyto cloudové služby zapůjčujete podle potřeby. V takovém případě převedete kapitálovou cenu údržby hardwaru na provozní náklady. Cloudová služba také nabízí tyto výhody:

- Rychlé nasazení velkých výpočetních prostředí

- Rychlé navracení systémů, které už nejsou potřeba

- Snadné nasazení tradičně složitých systémů, jako jsou nástroje pro vyrovnávání zatížení

- Možnost poskytovat flexibilní výpočetní kapacitu nebo škálování v případě potřeby

- Cenově výhodné výpočetní prostředí

- Přístup odkudkoli pomocí webového portálu nebo programového automatizace

- Cloudové služby pro splnění většiny požadavků na výpočetní výkon a aplikace

S místní infrastrukturou máte plnou kontrolu nad hardwarem a softwarem, který je nasazený. Historicky to vedlo k rozhodování o nákupu hardwaru, které se soustředí na horizontální navýšení kapacity. Příkladem je nákup serveru s více jádry pro splnění požadavků na výkon ve špičce. Tato infrastruktura bohužel může být nevyužitá mimo okno poptávky. S Azure můžete nasadit jenom infrastrukturu, kterou potřebujete, a kdykoli ji upravit. To vede k zaměření na horizontální navýšení kapacity v rámci nasazení dalších výpočetních uzlů, které splní požadavky na výkon. Horizontální navýšení kapacity Cloud Services je efektivnější než při vertikálním navýšení kapacity prostřednictvím nákladného hardwaru.

Společnost Microsoft nasadila spoustu datových center Azure po celém světě a plánuje se efektivněji. Společnost Microsoft navíc rozšiřuje cloudy v zemích, jako je Čína a Německo. Tímto způsobem můžou nasazovat datacentra jenom největší globální podniky. díky tomu Azure usnadňuje podnikům libovolné velikosti nasazení svých služeb blízko zákazníkům.

Pro malé firmy Azure umožňuje využít vstupní bod s nízkými náklady a možnost rychle škálovat na zvýšení poptávky. Tím se zabrání velkému objemu investic do infrastruktury v infrastruktuře a v případě potřeby poskytuje flexibilitu architektům a novým systémům. Používání cloud computingu zapadá do kvalitního a rychlého modelu nárůstu po spuštění.

Další informace o dostupných oblastech Azure najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Model cloud computingu

Azure používá model cloud computingu založený na kategoriích služby poskytovaných zákazníkům. Mezi tyto tři kategorie služeb patří infrastruktura jako služba (IaaS), platforma jako služba (PaaS) a software jako služba (SaaS). Dodavatelé sdílejí určitou nebo veškerou odpovědnost za komponenty v zásobníku computingu v každé z těchto kategorií. Pojďme se podívat na všechny kategorie pro cloud computing.
![Porovnání zásobníku cloud computingu](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: infrastruktura jako služba

Dodavatel IaaS cloudu spouští a spravuje všechny fyzické výpočetní prostředky a požadovaný software, který umožňuje virtualizaci počítačů. Zákazník této služby nasadí virtuální počítače v těchto hostovaných datacentrech. I když se virtuální počítače nacházejí v datacentru mimo pracoviště, příjemce IaaS má kontrolu nad konfigurací a správou operačního systému, který ponechává základní infrastrukturu pro dodavatele cloudu.

Azure zahrnuje několik řešení IaaS, včetně virtuálních počítačů, virtuálních počítačů a služby Virtual Machine Scale Sets a související síťové infrastruktury. Virtuální počítače jsou oblíbená volba pro prvotní migraci služeb do Azure, protože umožňuje model migrace "výtah a Shift". V datovém centru můžete nakonfigurovat virtuální počítač, jako je aktuálně běžící vaše služby, a pak svůj software migrovat do nového virtuálního počítače. Je možné, že budete muset provést aktualizace konfigurace, jako jsou adresy URL pro jiné služby nebo úložiště, ale tímto způsobem můžete migrovat mnoho aplikací.

Sada škálování virtuálních počítačů je postavená na Azure Virtual Machines a poskytuje snadný způsob, jak nasadit clustery identických virtuálních počítačů. Sada škálování virtuálních počítačů také podporuje automatické škálování, aby bylo možné v případě potřeby nasadit nové virtuální počítače automaticky. Díky tomu bude škálování virtuálního počítače ideální platformou pro hostování výpočetních clusterů mikroslužeb vyšší úrovně, jako je například Azure Service Fabric a Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: platforma jako služba

Pomocí PaaS nasadíte aplikaci do prostředí, které poskytuje dodavatel cloudové služby. Dodavatel provádí veškerou správu infrastruktury, takže se můžete soustředit na vývoj aplikací a správu dat.

Azure poskytuje několik PaaS výpočetních nabídek, včetně funkce Web Apps Azure App Service a Azure Cloud Services (webová role a role pracovních procesů). V obou případech mají vývojáři více způsobů, jak nasadit své aplikace bez znalosti o ořechech a šroubech, které to podporují. Vývojáři nepotřebují vytvářet virtuální počítače, pomocí protokol RDP (Remote Desktop Protocol) (RDP) se přihlaste ke každému z nich, nebo aplikaci nainstalujte. Stačí tlačítko (nebo se na něj zavřít) a nástroje poskytované společností Microsoft zřídit virtuální počítače a potom na ně nasadit a nainstalovat aplikaci.

#### <a name="saas-software-as-a-service"></a>SaaS: software jako služba

SaaS je software, který je centrálně hostovaný a spravovaný. Obvykle je založena na víceklientské architektuře – jedna verze aplikace se používá pro všechny zákazníky. Dá se škálovat na více instancí, abyste zajistili nejlepší výkon ve všech umístěních. SaaS software je obvykle licencován prostřednictvím měsíčního nebo ročního předplatného. Výrobci softwaru SaaS jsou odpovědní za všechny komponenty softwarového zásobníku, takže spravovat budou jenom poskytované služby.

Microsoft 365 je dobrým příkladem nabídky SaaS. Předplatitelé platí měsíční nebo roční poplatek za předplatné a jako službu získají Microsoft Exchange, Microsoft OneDrive a zbytek systém Microsoft Office Suite. Předplatitelé vždycky získají nejnovější verzi a pro vás spravuje Exchange Server. V porovnání s instalací a upgradem Office každý rok je to levnější a vyžaduje menší úsilí.

## <a name="azure-services"></a>Služby Azure

Azure nabízí ve své platformě cloud computingu spoustu služeb. Mezi tyto služby patří:

### <a name="compute-services"></a>Výpočetní služby

Služby pro hostování a spouštění aplikačních úloh:

- Azure Virtual Machines – Linux i Windows

- App Services (Web Apps, Mobile Apps, Logic Apps, API Apps a aplikace Functions)

- Azure Batch (pro rozsáhlé paralelní a dávkové výpočetní úlohy)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Datové služby

Služby pro ukládání a správu dat:

- Azure Storage (zahrnuje služby Azure Blob, Queue, Table a File)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Aplikační služby

Služby pro sestavování a provoz aplikací:

- Azure Active Directory (Azure AD)

- Azure Service Bus pro propojení distribuovaných systémů

- Azure HDInsight pro zpracování velkých objemů dat

- Azure Logic Apps pro pracovní postupy pro integraci a orchestraci

- Azure Media Services

### <a name="network-services"></a>Síťové služby

Služby pro sítě v rámci Azure i mezi Azure a místními datacentry:

- Azure Virtual Network

- Azure ExpressRoute

- DNS poskytovaný službou Azure

- Azure Traffic Manager

- Azure Content Delivery Network

Podrobnou dokumentaci ke službám Azure najdete v [dokumentaci ke službě Azure](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Klíčové pojmy Azure

### <a name="datacenters-and-regions"></a>Datová centra a oblasti

Azure je globální cloudová platforma, která je obecně dostupná v mnoha oblastech po celém světě. Při zřizování služby, aplikace nebo virtuálního počítače v Azure budete požádáni o výběr oblasti. Vybraná oblast představuje konkrétní datové centrum, ve kterém je vaše aplikace spuštěná. Další informace najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).

Jednou z výhod používání Azure je to, že můžete své aplikace nasadit do různých Datacenter po celém světě. Oblast, kterou zvolíte, může ovlivnit výkon aplikace. Je optimální zvolit oblast, která je blíže většině vašich zákazníků, aby se snížila latence v případě síťových požadavků. Můžete také vybrat oblast, která bude vyhovovat zákonným požadavkům pro distribuci aplikace v určitých zemích nebo oblastech.

### <a name="azure-portal"></a>portál Azure

Azure Portal je webová aplikace, která se dá použít k vytváření, správě a odebírání prostředků a služeb Azure. Azure Portal najdete na adrese [Portal.Azure.com](https://portal.azure.com). Zahrnuje přizpůsobitelný řídicí panel a nástroje pro správu prostředků Azure. Poskytuje také informace o fakturaci a předplatném. Další informace najdete v tématu [přehled portál Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) a [Správa prostředků Azure prostřednictvím portálu](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Zdroje a prostředky

Prostředky Azure jsou individuální výpočetní prostředí, sítě, data nebo služby hostování aplikací, které se nasadily do předplatného Azure. Mezi běžné prostředky patří virtuální počítače, účty úložiště nebo databáze SQL. Služby Azure se často skládají z několika souvisejících prostředků Azure. Virtuální počítač Azure může například zahrnovat virtuální počítač, účet úložiště, síťový adaptér a veřejnou IP adresu. Tyto prostředky je možné vytvořit, spravovat a odstranit jednotlivě nebo jako skupinu. Prostředky Azure jsou podrobněji popsány dále v této příručce.

### <a name="resource-groups"></a>Skupiny prostředků

Skupina prostředků Azure je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze prostředky, které chcete spravovat jako skupinu. Skupiny prostředků Azure jsou podrobněji popsány dále v této příručce.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

Šablona Azure Resource Manager je soubor JavaScript Object Notation (JSON), který definuje jeden nebo více prostředků pro nasazení do skupiny prostředků. Definuje také závislosti mezi nasazenými prostředky. Šablony Správce prostředků jsou podrobněji popsány dále v této příručce.

### <a name="automation"></a>Automation

Kromě vytváření, správy a odstraňování prostředků pomocí Azure Portal můžete automatizovat tyto aktivity pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell je sada modulů, které poskytují rutiny pro správu Azure. Pomocí rutin můžete vytvářet, spravovat a odebírat služby Azure. Rutiny vám pomůžou dosáhnout konzistentních, opakovaných a praktických nasazení. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure je nástroj, který můžete použít k vytvoření, správě a odebrání prostředků Azure z příkazového řádku. Rozhraní příkazového řádku Azure je dostupné pro Linux, Mac OS X a Windows. Další informace a technické podrobnosti najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>Rozhraní REST API

Azure je založený na sadě rozhraní REST API, které podporují uživatelské rozhraní Azure Portal. Většina těchto rozhraní REST API se taky podporuje, aby vám umožnila programové zřizování a správu prostředků a aplikací Azure z libovolného zařízení s podporou Internetu. Další informace najdete v referenčních informacích k [sadě Azure REST SDK](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Správci mají přístup k Azure PowerShell a Azure CLI prostřednictvím prostředí přístupného pro prohlížeč, které se nazývá Azure Cloud Shell. Toto interaktivní rozhraní poskytuje flexibilní nástroj pro správce systémů Linux a Windows, aby používal své rozhraní příkazového řádku, které je zvoleno, buď bash nebo PowerShell. Azure Cloud Shell může mít přístup prostřednictvím portálu, jako samostatné webové rozhraní na [Shell.Azure.com](https://shell.azure.com), nebo z řady dalších přístupových bodů. Další informace najdete v tématu [přehled Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Předplatná Azure

Předplatné je logické seskupení služeb Azure, které jsou propojené s účtem Azure. Jeden účet Azure může obsahovat několik předplatných. Fakturace služeb Azure se provádí na základě jednotlivých předplatných. Předplatná Azure mají správce účtu, který má plnou kontrolu nad předplatným, a správce služeb, který má kontrolu nad všemi službami v rámci předplatného. Informace o klasických správcích předplatných najdete v tématu [Přidání nebo změna správců předplatného Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). Kromě správců může být jednotlivým účtům přiděleno podrobné řízení prostředků Azure pomocí [řízení přístupu na základě role Azure (RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Výběr a povolení předplatného Azure

Než budete moct pracovat se službami Azure, potřebujete předplatné. K dispozici je několik typů předplatného.

**Bezplatné účty**: odkaz pro registraci bezplatného účtu se nachází na [webu Azure](https://azure.microsoft.com/). Díky tomu budete mít kredit v průběhu 30 dnů a vyzkoušíte jakoukoli kombinaci prostředků v Azure. Pokud částku svého kreditu překročíte, váš účet se pozastaví. Na konci zkušebního období se vaše služby vyřadí z provozu a nebudou už fungovat. Můžete kdykoli upgradovat na předplatné s průběžnými platbami.

**Předplatná MSDN**: Pokud máte předplatné MSDN, získáte každý měsíc určitou částku na kredit Azure. Pokud máte například Microsoft Visual Studio Enterprise s předplatným MSDN, získáte \$ kredit Azure 150 měsíčně.

Pokud překročíte částku kreditu, bude služba zakázána, dokud se nespustí další měsíc. Limit útraty můžete vypnout a přidat platební kartu, která se má použít pro další náklady. Některé z těchto nákladů se uvažují pro účty MSDN. Platíte například cenu za Linux pro virtuální počítače s Windows serverem a za servery Microsoftu se neúčtují žádné další poplatky, jako je Microsoft SQL Server. To umožňuje vytvářet účty MSDN ideální pro scénáře vývoje a testování.

**Účty BizSpark**: program Microsoft BizSpark poskytuje spoustu výhod spouštění. Jednou z těchto výhod je přístup ke všem softwarovým prostředím Microsoftu pro vývoj a testování až na pět účtů MSDN. Pro každý z těchto pěti účtů MSDN získáte poplatky za $150 a platíte nižší sazby za několik služeb Azure, například Virtual Machines.

Průběžné **platby**: s tímto předplatným platíte za to, co využijete, připojením platební karty nebo debetní karty k účtu. Pokud jste organizace, můžete také schválit fakturaci.

**Smlouvy Enterprise**: v rámci smlouvy Enterprise se v Azure po příštím roce zavazujeme, že v Azure použijete určitý počet služeb a platíte tuto částku před uplynutím času. Závazek, který provedete, se spotřebovává po celý rok. Pokud překročíte částku závazku, můžete nadlimitní využití zaplatit v nedoplatkech. V závislosti na objemu závazku získáte slevu služby v Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Udělení přístupu pro správu k předplatnému Azure

RBAC má několik předdefinovaných rolí, které můžete použít k přiřazení oprávnění. Pokud chcete, aby uživatel měl správce předplatného Azure, přiřaďte mu roli [vlastníka](../../role-based-access-control/built-in-roles.md#owner) v oboru předplatného. Role Vlastník poskytuje uživateli úplný přístup ke všem prostředkům v předplatném, včetně práva delegovat přístup na ostatní.

Další informace najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Zobrazit fakturační údaje v Azure Portal

Důležitou součástí používání Azure je možnost zobrazit informace o fakturaci. Azure Portal poskytuje podrobný přehled o fakturačních informacích Azure.

Další informace najdete v tématu [stažení fakturačních faktur Azure a dat o denním využití](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Získat fakturační informace z rozhraní API pro fakturaci

Kromě zobrazení fakturace na portálu můžete získat přístup k fakturačním údajům pomocí skriptu nebo programu přes rozhraní REST API pro účtování Azure:

- K načtení dat o využití můžete použít rozhraní API využití Azure. Informace o využití fakturace můžete vyladit pomocí označení souvisejících prostředků Azure. Můžete například označit každý z prostředků ve skupině prostředků s názvem oddělení nebo názvem projektu a pak sledovat náklady konkrétně pro tuto jednu značku.

- K vypsání všech dostupných prostředků spolu s informacemi o metadatech a cenách jednotlivých prostředků můžete použít rozhraní API služby Azure Rate Card.

Další informace najdete v článku [Získání přehledu o spotřebě prostředků Microsoft Azure](../../cost-management-billing/manage/usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Náklady na prognózu pomocí cenové kalkulačky

Ceny za každou službu v Azure se liší. Řada služeb Azure poskytuje úrovně Basic, Standard a Premium. Každá úroveň má obvykle několik cen a úrovní výkonu. Pomocí [cenové kalkulačky online](https://azure.microsoft.com/pricing/calculator)můžete vytvořit odhad cen. Kalkulačka zahrnuje flexibilitu při odhadování nákladů na jeden prostředek nebo skupinu prostředků.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager je mechanismus nasazení, správy a organizace pro prostředky Azure. Pomocí Správce prostředků můžete do skupiny prostředků umístit mnoho jednotlivých prostředků dohromady.

Správce prostředků taky zahrnuje možnosti nasazení, které umožňují přizpůsobitelné nasazení a konfiguraci souvisejících prostředků. Například pomocí Správce prostředků můžete nasadit aplikaci, která se skládá z několika virtuálních počítačů, nástroje pro vyrovnávání zatížení a databáze v Azure SQL Database jako jeden celek. Tato nasazení vyvíjíte pomocí šablony Správce prostředků.

Resource Manager poskytuje několik výhod:

- Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

- Řešení můžete opakovaně nasadit v průběhu životního cyklu vývoje a mít jistotu, že vaše prostředky budou nasazeny v konzistentním stavu.

- Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

- Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

- Řízení přístupu můžete použít pro všechny služby ve vaší skupině prostředků, protože RBAC je nativně integrovaná do platformy pro správu.

- Můžete použít značky pro prostředky a logicky uspořádat všechny prostředky v rámci vašeho předplatného.

- Můžete vyjasnit fakturaci vaší organizace zobrazením nákladů na skupinu prostředků, které sdílejí stejnou značku.

### <a name="tips-for-creating-resource-groups"></a>Tipy pro vytváření skupin prostředků

Při rozhodování o skupinách prostředků Vezměte v úvahu tyto tipy:

- Všechny prostředky ve skupině prostředků by měly mít stejný životní cyklus.

- V jednom okamžiku můžete přiřadit prostředek jenom k jedné skupině.

- Prostředek můžete kdykoli přidat nebo odebrat ze skupiny prostředků. Každý prostředek musí patřit do skupiny prostředků. Takže pokud odeberete prostředek z jedné skupiny, musíte ho přidat do jiného.

- Většinu typů prostředků můžete kdykoli přesunout do jiné skupiny prostředků.

- Prostředky ve skupině prostředků mohou být v různých oblastech.

- Skupinu prostředků můžete použít k řízení přístupu k prostředkům v ní.

### <a name="building-resource-manager-templates"></a>Vytváření šablon Správce prostředků

Správce prostředků šablony deklarativně definují prostředky a konfigurace prostředků, které budou nasazeny do jedné skupiny prostředků. Šablony Správce prostředků můžete použít k orchestraci složitých nasazení bez nutnosti nadbytečného skriptování nebo ruční konfigurace. Po vytvoření šablony ji můžete nasadit několikrát – pokaždé se stejným výsledkem.

Šablona Správce prostředků se skládá ze čtyř částí:

- **Parametry**: Jedná se o vstupy pro nasazení. Hodnoty parametrů lze zadat pomocí lidského nebo automatizovaného procesu. Příkladem parametru může být uživatelské jméno a heslo správce pro virtuální počítač s Windows. Hodnoty parametrů jsou v rámci nasazení použity, když jsou zadány.

- **Proměnné**: slouží k uložení hodnot, které se používají v rámci nasazení. Na rozdíl od parametrů není hodnota proměnné k dispozici v době nasazení. Místo toho je pevně zakódována nebo dynamicky generována.

- **Prostředky**: Tato část šablony definuje prostředky, které mají být nasazeny, například virtuální počítače, účty úložiště a virtuální sítě.

- **Výstup**: po dokončení nasazení může správce prostředků vracet data, jako jsou například dynamicky generované připojovací řetězce.

Pro automatizaci nasazení jsou k dispozici následující mechanismy:

- **Funkce**: můžete použít několik funkcí v šablonách správce prostředků. Patří mezi ně operace, jako je převod řetězce na malá písmena, nasazení více instancí definovaného prostředku a dynamické vrácení cílové skupiny prostředků. Správce prostředků Functions vám pomůžou vytvářet dynamická nasazení.

- **Závislosti prostředků**: Když nasazujete víc prostředků, budou mít některé prostředky závislost na dalších. Pro usnadnění nasazení můžete použít deklaraci závislosti, aby byly závislé prostředky nasazeny před ostatními.

- **Propojování šablon**: v rámci jedné šablony Správce prostředků můžete propojit s jinou šablonou. To umožňuje, aby bylo nasazení rozloženo do sady cílových šablon specifických pro účel.

Šablony Správce prostředků lze vytvořit v libovolném textovém editoru. Sada Azure SDK pro Visual Studio ale obsahuje nástroje, které vám pomůžou. Pomocí sady Visual Studio můžete přidat prostředky do šablony prostřednictvím průvodce a potom nasadit a ladit šablonu přímo z aplikace Visual Studio. Další informace najdete v tématu [vytváření Azure Resource Manager šablon](../../resource-group-authoring-templates.md).

Nakonec můžete převést existující skupiny prostředků na opakovaně použitelnou šablonu z Azure Portal. To může být užitečné, pokud chcete vytvořit nasazenou šablonu existující skupiny prostředků nebo chcete jenom prostudovat základní JSON. Pokud chcete exportovat skupinu prostředků, vyberte tlačítko **skript Automation** z nastavení skupiny prostředků.

## <a name="security-of-azure-resources-rbac"></a>Zabezpečení prostředků Azure (RBAC)

Operačnímu účtu můžete udělit přístup k uživatelským účtům v zadaném oboru: předplatné, skupina prostředků nebo individuální prostředek. To znamená, že můžete nasadit sadu prostředků do skupiny prostředků, jako je například virtuální počítač a všechny související prostředky, a udělit oprávnění konkrétnímu uživateli nebo skupině. Tento přístup omezuje přístup jenom na prostředky, které patří do cílové skupiny prostředků. Můžete taky udělit přístup k jednomu prostředku, jako je třeba virtuální počítač nebo virtuální síť.

Chcete-li udělit přístup, přiřaďte roli uživateli nebo skupině uživatelů. Existuje mnoho předdefinovaných rolí. Můžete také definovat vlastní role.

Tady je několik příkladů [předdefinovaných rolí v Azure](../../role-based-access-control/built-in-roles.md):

- **Vlastník**: uživatel s touto rolí může spravovat všechno, včetně přístupu.

- **Čtecí modul**: uživatel s touto rolí může číst prostředky všech typů (s výjimkou tajných klíčů), ale nemůže provádět změny.

- **Přispěvatel virtuálních počítačů**: uživatel s touto rolí může spravovat virtuální počítače, ale nemůže spravovat virtuální síť, ke které jsou připojené, nebo účet úložiště, ve kterém se nachází soubor VHD.

- **Přispěvatel databáze SQL**: uživatel s touto rolí může spravovat databáze SQL, ale ne jejich zásady související se zabezpečením.

- **Správce zabezpečení SQL**: uživatel s touto rolí může spravovat zásady týkající se zabezpečení serverů a databází SQL.

- **Přispěvatel účtu úložiště**: uživatel s touto rolí může spravovat účty úložiště, ale nemůže spravovat přístup k účtům úložiště.

Další informace najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines je jednou z centrálních služeb IaaS v Azure. Azure Virtual Machines podporuje nasazení virtuálních počítačů se systémem Windows nebo Linux v datovém centru Microsoft Azure. S Azure Virtual Machines máte celkovou kontrolu nad konfigurací virtuálních počítačů a zodpovídáte za veškerou instalaci, konfiguraci a údržbu softwaru.

Když nasazujete virtuální počítač Azure, můžete vybrat obrázek z Azure Marketplace, nebo můžete zadat vlastní zobecněnou bitovou kopii. Tato image se používá k aplikování operačního systému a počáteční konfigurace. Při nasazení Správce prostředků zpracuje některá nastavení konfigurace, jako je například přiřazení názvu počítače, pověření pro správu a konfigurace sítě. Rozšíření virtuálních počítačů Azure můžete použít k dalšímu automatizaci konfigurací, jako je instalace softwaru, konfigurace antivirového programu a řešení monitorování.

Virtuální počítače můžete vytvářet v mnoha různých velikostech. Velikost virtuálního počítače určuje přidělování prostředků, jako je například zpracování, paměť a kapacita úložiště. V některých případech jsou specifické funkce, jako jsou síťové adaptéry s podporou RDMA a disky SSD, dostupné jenom pro určité velikosti virtuálních počítačů. Úplný seznam velikostí a možností virtuálních počítačů najdete v tématu "velikosti pro virtuální počítače v Azure" pro [systémy Windows](../../virtual-machines/windows/sizes.md) a [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Případy použití

Vzhledem k tomu, že virtuální počítače Azure nabízejí úplnou kontrolu nad konfigurací, jsou ideální pro celou řadu úloh serveru, které se nevejdou do modelu PaaS. Zatížení serveru, jako jsou databázové servery (SQL Server, Oracle nebo MongoDB), Windows Server Active Directory, Microsoft SharePoint a spousta dalších možností spuštění na Microsoft Azure platformě. V případě potřeby můžete přesunout takové úlohy z místního datacentra do jedné nebo víc oblastí Azure, aniž byste museli velkou velikost znovu nakonfigurovat.

### <a name="deployment-of-virtual-machines"></a>Nasazení virtuálních počítačů

Virtuální počítače Azure můžete nasadit pomocí Azure Portal pomocí automatizace s modulem Azure PowerShell nebo pomocí automatizace s rozhraním příkazového řádku pro různé platformy.

#### <a name="portal"></a>Portál

Nasazení virtuálního počítače pomocí Azure Portal vyžaduje jenom aktivní předplatné Azure a přístup k webovému prohlížeči. Můžete vybrat spoustu různých imagí operačního systému s různými konfiguracemi. Všechny požadavky na úložiště a síť jsou nakonfigurované během nasazování. Další informace najdete v části "Vytvoření virtuálního počítače v Azure Portal pro [systémy Windows](../../virtual-machines/windows/quick-create-portal.md) a [Linux](../../virtual-machines/linux/quick-create-portal.md).

Kromě nasazení virtuálního počítače z Azure Portal můžete nasadit Azure Resource Manager šablonu z portálu. Tím se nasadí a nakonfigurují všechny prostředky, jak jsou definované v šabloně. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Nasazení virtuálního počítače Azure pomocí prostředí PowerShell umožňuje kompletní automatizaci nasazení všech souvisejících prostředků virtuálních počítačů, včetně úložiště a sítě. Další informace najdete v tématu [Vytvoření virtuálního počítače s Windows pomocí Správce prostředků a PowerShellu](../../virtual-machines/windows/quick-create-powershell.md).

Kromě nasazení výpočetních prostředků Azure můžete použít modul Azure PowerShell k nasazení Azure Resource Manager šablony. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Rozhraní příkazového řádku (CLI)

Stejně jako modul PowerShellu nabízí rozhraní příkazového řádku Azure automatizaci nasazení a dá se použít v systémech Windows, OS X a Linux. Když použijete příkaz pro **rychlé vytvoření virtuálního počítače** Azure CLI, nasadí se všechny související prostředky virtuálního počítače (včetně úložiště a sítě) a samotného virtuálního počítače. Další informace najdete v tématu [Vytvoření virtuálního počítače se systémem Linux v Azure pomocí rozhraní](../../virtual-machines/linux/quick-create-cli.md)příkazového řádku.

Podobně můžete pomocí rozhraní příkazového řádku Azure nasadit šablonu Azure Resource Manager. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Přístup a zabezpečení pro virtuální počítače

Přístup k virtuálnímu počítači z Internetu vyžaduje přidružené síťové rozhraní nebo nástroj pro vyrovnávání zatížení, aby se nakonfigurovala veřejná IP adresa. Veřejná IP adresa zahrnuje název DNS, který se bude překládat na virtuální počítač nebo nástroj pro vyrovnávání zatížení. Další informace najdete v tématu [IP adresy v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Přístup k virtuálnímu počítači můžete spravovat přes veřejnou IP adresu pomocí prostředku skupiny zabezpečení sítě (NSG). NSG funguje jako brána firewall a povoluje nebo zakazuje provoz napříč síťovým rozhraním nebo podsítí na sadě definovaných portů. Pokud třeba chcete vytvořit relaci vzdálené plochy s virtuálním počítačem Azure, musíte nakonfigurovat NSG, aby povolovala příchozí provoz na portu 3389. Další informace najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí Azure Portal](../../virtual-machines/windows/nsg-quickstart-portal.md).

Stejně jako u jakéhokoli počítačového systému byste měli zajistit zabezpečení pro virtuální počítač Azure v operačním systému pomocí zabezpečovacích přihlašovacích údajů a bran firewall softwaru.

## <a name="azure-storage"></a>Azure Storage

Azure Storage je služba spravovaná Microsoftem, která poskytuje trvalé, škálovatelné a redundantní úložiště. Účet služby Azure Storage můžete přidat jako prostředek do jakékoli skupiny prostředků pomocí libovolné metody nasazení prostředků. Azure zahrnuje čtyři typy úložišť: BLOB Storage, File Storage, Table Storage a Queue Storage. Při nasazování účtu úložiště jsou k dispozici dva typy účtů, obecné účely a BLOB Storage. Účet úložiště pro obecné účely vám umožní přístup ke všem čtyřem typům úložišť. Účty BLOB Storage jsou podobné účtům pro obecné účely, ale obsahují specializované objekty blob, které zahrnují horké a studené úrovně přístupu. Další informace o službě BLOB Storage najdete v tématu [úložiště objektů BLOB v Azure](../../storage/blobs/storage-blob-storage-tiers.md).

Účty Azure Storage je možné nakonfigurovat s různými úrovněmi redundance:

- **Místně redundantní úložiště** poskytuje vysokou dostupnost tím, že zajišťuje synchronní dokončení tří kopií všech dat předtím, než se zápis považuje za úspěšný. Tyto kopie jsou uloženy v jednom zařízení v jedné oblasti. Repliky se nacházejí v samostatných doménách selhání a upgradovacích doménách. To znamená, že data jsou k dispozici i v případě, že uzel úložiště, který uchovává vaše data, není úspěšný nebo je offline aktualizován.

- **Geograficky redundantní úložiště** zajišťuje pro vysokou dostupnost tři synchronní kopie dat v primární oblasti a pak asynchronně vytváří tři repliky v spárované oblasti pro zotavení po havárii.

- **Geograficky redundantní úložiště s přístupem pro čtení** je geograficky redundantní úložiště a možnost číst data v sekundární oblasti. Tato možnost je vhodná pro částečné zotavení po havárii. Pokud dojde k potížím s primární oblastí, můžete změnit aplikaci tak, aby měla přístup jen pro čtení k spárované oblasti.

### <a name="use-cases"></a>Případy použití

Každý typ úložiště má jiný případ použití.

#### <a name="blob-storage"></a>Blob Storage

*Objekt BLOB* Word je zkratka pro *binární rozsáhlý objekt*. Objekty blob jsou nestrukturované soubory, jako jsou ty, které ukládáte ve vašem počítači. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů. Úložiště objektů BLOB v Azure taky obsahuje datové disky Azure Virtual Machines.

Azure Storage podporuje tři druhy objektů blob:

- **Objekty blob bloku** se používají pro uchovávání běžných souborů o velikosti až 195 GB (4 MB × 50 000 bloků). Objekty blob bloku se primárně používají pro úložiště souborů, které se čtou od začátku do konce, jako jsou například mediální soubory nebo soubory obrázků pro weby. Jsou pojmenované objekty blob bloku, protože soubory větší než 64 MB se musí nahrát jako malé bloky. Tyto bloky se pak konsolidují (nebo potvrdí) do konečného objektu BLOB.

- **Objekty blob stránky** se používají k ukládání souborů s náhodným přístupem o velikosti až 1 TB. Objekty blob stránky se používají hlavně jako záložní úložiště pro virtuální pevné disky, které poskytují trvalé disky pro Azure Virtual Machines, výpočetní služby IaaS v Azure. Označují se jako objekty blob stránky, protože poskytují náhodný přístup pro čtení/zápis k 512bajtovým stránkám.

- **Doplňovací objekty blob** se skládají z bloků, jako jsou objekty blob bloku, ale jsou optimalizované pro operace připojení. Tyto informace se často používají k protokolování informací z jednoho nebo více zdrojů do stejného objektu BLOB. Můžete například zapsat všechna trasování protokolování do stejného doplňovací objektu BLOB pro aplikaci, která běží na více virtuálních počítačích. Jeden doplňovací objekt blob může mít až 195 GB.

Další informace najdete v tématu Začínáme [s úložištěm objektů BLOB v Azure pomocí .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Azure File Storage je služba, která nabízí sdílené složky v cloudu pomocí standardního protokolu SMB (Server Message Block). Služba podporuje SMB 2,1 a SMB 3,0. Pomocí služby Azure File Storage můžete migrovat aplikace, které spoléhají na sdílené složky do Azure, rychle a bez nákladných přepisů. Aplikace běžící na virtuálních počítačích Azure, v cloudových službách nebo z místních klientů mohou připojit sdílení souborů v cloudu. To se podobá tomu, jak desktopová aplikace připojí typickou sdílenou složku SMB. Potom může sdílenou složku File Storage připojit a používat libovolný počet aplikací.

Vzhledem k tomu, že sdílená složka úložiště je standardní sdílená složka SMB, aplikace běžící v Azure mají přístup k datům ve sdílené složce přes rozhraní API v/v systému souborů. Vývojáři proto můžou použít svůj existující kód a dovednosti k migraci stávajících aplikací. IT specialisté můžou použít rutiny prostředí PowerShell k vytváření, připojování a správě sdílených složek úložiště souborů v rámci správy aplikací Azure.

Další informace najdete v tématu Začínáme [se službou Azure File Storage ve Windows](../../storage/files/storage-how-to-use-files-windows.md) nebo [používání služby Azure File Storage se systémem Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Úložiště Table

Azure Table Storage je služba, která ukládá strukturovaná data typu NoSQL v cloudu. Table Storage je úložiště klíčů/atributů s návrhem bez schématu. Vzhledem k tomu, že je tabulka úložiště bez schématu, je snadné přizpůsobit data, jak se vyvíjí vaše aplikace. Přístup k datům je rychlý a nákladově efektivní pro všechny typy aplikací. Využívání úložiště Table Storage obvykle znamená výrazně nižší náklady než tradiční SQL pro podobné objemy dat.

Úložiště Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení a dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit. Účet úložiště může obsahovat libovolný počet tabulek až do limitu kapacity účtu úložiště.

Další informace najdete v tématu [Začínáme s úložištěm Azure Table](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Úložiště Azure Queue zajišťuje cloudový přenos zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování jsou součásti aplikace často odděleny, takže je lze škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Další informace najdete v tématu [Začínáme s úložištěm Azure Queue](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Nasazení účtu úložiště

K dispozici je několik možností nasazení účtu úložiště.

#### <a name="portal"></a>Portál

Nasazení účtu úložiště pomocí Azure Portal vyžaduje jenom aktivní předplatné Azure a přístup k webovému prohlížeči. Nový účet úložiště můžete nasadit do nové nebo existující skupiny prostředků. Po vytvoření účtu úložiště můžete vytvořit kontejner objektů BLOB nebo sdílení souborů pomocí portálu. Entity úložiště tabulky a fronty můžete vytvářet programově. Další informace najdete v článku o [vytvoření účtu úložiště](../../storage/common/storage-account-create.md).

Kromě nasazení účtu úložiště z Azure Portal můžete nasadit Azure Resource Manager šablonu z portálu. Tím se nasadí a nakonfigurují všechny prostředky, jak jsou definované v šabloně, včetně všech účtů úložiště. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Nasazení účtu Azure Storage pomocí prostředí PowerShell umožňuje kompletní automatizaci nasazení účtu úložiště. Další informace najdete v tématu [použití Azure PowerShell s Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Kromě nasazení prostředků Azure můžete použít modul Azure PowerShell k nasazení Azure Resource Manager šablony. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Rozhraní příkazového řádku (CLI)

Stejně jako modul PowerShellu nabízí rozhraní příkazového řádku Azure automatizaci nasazení a dá se použít v systémech Windows, OS X a Linux. K vytvoření účtu úložiště můžete použít příkaz **vytvořit účet úložiště** Azure CLI. Další informace najdete v tématu [použití rozhraní příkazového řádku Azure s Azure Storage.](../../storage/common/storage-azure-cli.md)

Podobně můžete pomocí rozhraní příkazového řádku Azure nasadit šablonu Azure Resource Manager. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Přístup a zabezpečení pro Azure Storage

K Azure Storage dochází různými způsoby, včetně Azure Portal i když během vytváření a operace virtuálních počítačů a z klientských knihoven úložiště.

#### <a name="virtual-machine-disks"></a>Disky virtuálních počítačů

Pokud nasazujete virtuální počítač, budete muset také vytvořit účet úložiště, který bude uchovávat disk s operačním systémem virtuálního počítače a všechny další datové disky. Můžete vybrat existující účet úložiště nebo vytvořit nový. Vzhledem k tomu, že maximální velikost objektu BLOB je 1 024 GB, má jeden disk virtuálního počítače maximální velikost 1 023 GB. Pokud chcete nakonfigurovat větší datový disk, můžete k virtuálnímu počítači prezentovat více datových disků a seskupovat je jako jeden logický disk. Další informace najdete v tématu Správa disků Azure pro [systémy Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) a [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Nástroje úložiště

K účtům Azure Storage se dá dostat prostřednictvím mnoha různých Průzkumníka úložišť, jako je Visual Studio Cloud Explorer. Tyto nástroje umožňují procházení účtů a dat úložiště. Další informace a seznam dostupných Průzkumníka úložišť najdete v tématu [Azure Storage nástrojů klienta](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Rozhraní API pro úložiště

Prostředky úložiště jsou dostupné v jakémkoli jazyce, který může dělat požadavky HTTP/HTTPS. Azure Storage dále nabízí programovací knihovny pro několik oblíbených jazyků. Tyto knihovny usnadňují práci s Azure Storage tím, že zpracovávají podrobnosti jako synchronní a asynchronní vyvolání, dávkování operací, správu výjimek a automatické opakování. Další informace najdete v tématu informace o [REST API Azure Storage služby](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Přístupové klíče k úložišti

Každý účet úložiště má dva ověřovací klíče, primární a sekundární. Jednu z nich je možné použít pro operace přístupu k úložišti. Tyto klíče úložiště slouží k lepšímu zabezpečení účtu úložiště a jsou vyžadovány pro programové přistupování k datům. Existují dva klíče, které umožňují příležitostné výměny klíčů pro zvýšení zabezpečení. Je důležité zachovat zabezpečení klíčů, protože jejich vlastnictví, společně s názvem účtu, umožňuje neomezený přístup k žádným datům v účtu úložiště.

#### <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Pokud potřebujete, aby mohli uživatelé řídit přístup k prostředkům úložiště, můžete vytvořit sdílený přístupový podpis. Sdílený přístupový podpis je token, který se dá připojit k adrese URL, která umožňuje delegovaný přístup k prostředku úložiště. Každý, kdo má token, má přístup k prostředku, na který odkazuje, s oprávněními, která určuje, po dobu, po kterou je platný. Další informace najdete v tématu [použití sdílených přístupových podpisů](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuální sítě jsou nezbytné k podpoře komunikace mezi virtuálními počítači. Můžete definovat podsítě, vlastní IP adresu, nastavení DNS, filtrování zabezpečení a vyrovnávání zatížení. Azure podporuje různé případy použití: jenom pro cloudové sítě nebo hybridních virtuálních sítí.

### <a name="cloud-only-virtual-networks"></a>Jenom cloudové virtuální sítě

Služba Azure Virtual Network je ve výchozím nastavení dostupná jenom pro prostředky uložené v Azure. Prostředky připojené ke stejné virtuální síti můžou vzájemně komunikovat. K zpřístupnění virtuálního počítače přes Internet můžete přidružit síťová rozhraní virtuálního počítače a nástroje pro vyrovnávání zatížení s veřejnou IP adresou. Zabezpečený přístup k veřejně vystaveným prostředkům můžete zvýšit pomocí skupiny zabezpečení sítě.

![Azure Virtual Network pro webovou aplikaci ve dvou vrstvách](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybridní virtuální sítě

Místní síť můžete připojit k virtuální síti Azure pomocí ExpressRoute nebo připojení VPN typu Site-to-site. V této konfiguraci je virtuální síť Azure v podstatě cloudové rozšíření vaší místní sítě.

Vzhledem k tomu, že je virtuální síť Azure připojená k vaší místní síti, musí virtuální sítě mezi různými místy používat jedinečnou část adresního prostoru, který vaše organizace používá. Podobně jako u různých podnikových umístění je přiřazena konkrétní podsíť protokolu IP, Azure se v rámci rozšiřování sítě bude lišit od jiného umístění.
Existuje několik možností, jak nasadit virtuální síť.

- [Azure Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Rozhraní příkazového řádku (CLI)](../../virtual-network/quick-create-cli.md)

- Šablony Azure Resource Manageru

> **Kdy použít**: kdykoli pracujete s virtuálními počítači v Azure, budete pracovat s virtuálními sítěmi. To umožňuje segmentovat vaše virtuální počítače do veřejných nebo privátních podsítí podobných v místních datových centrech.
>
> **Začínáme**: nasazení virtuální sítě azure pomocí Azure Portal vyžaduje jenom aktivní předplatné Azure a přístup k webovému prohlížeči. Novou virtuální síť můžete nasadit do nové nebo existující skupiny prostředků. Když vytváříte nový virtuální počítač z portálu, můžete vybrat existující virtuální síť nebo vytvořit novou. Začněte a [vytvořte virtuální síť pomocí Azure Portal](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Přístup a zabezpečení pro virtuální sítě

Pomocí skupiny zabezpečení sítě můžete přispět k zabezpečení virtuálních sítí Azure. Skupin zabezpečení sítě obsahují seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťové přenosy do instancí virtuálních počítačů ve virtuální síti. V rámci této podsítě můžete přidružit skupin zabezpečení sítě buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů. Když přidružíte NSG k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v dané podsíti. Kromě toho můžete dál omezit provoz na jednotlivé virtuální počítače tím, že přidružíte NSG přímo k tomuto virtuálnímu počítači. Další informace viz [Filtrování provozu sítě s použitím skupin zabezpečení sít](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Další kroky

- [Vytvoření virtuálního počítače s Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Vytvoření virtuálního počítače s Linuxem](../../virtual-machines/linux/quick-create-portal.md)
