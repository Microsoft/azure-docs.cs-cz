---
title: Příručka Začínáme pro vývojáře v Azure | Dokumentace Microsoftu
description: Toto téma obsahuje důležité informace pro vývojáře, které pokud chcete začít pracovat na platformě Microsoft Azure pro potřeb rozvoje svého podniku.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: 7c4eefa09f0a70d42601a5b1fe8694500b5c6be2
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118247"
---
# <a name="get-started-guide-for-azure-developers"></a>Úvodní příručka pro vývojáře v Azure

## <a name="what-is-azure"></a>Co je Azure?

Azure je kompletní cloudovou platformou, můžete hostovat svoje existující aplikace, Zjednodušte vývoj nové aplikace a ještě vylepšit u místních aplikací. Azure se integruje cloudové služby, které potřebujete pro vývoj, testování, nasazení a Správa aplikací – s využitím efektivity cloud computingu.

Hostováním aplikací v Azure můžete začít v malém a snadno škálovat aplikaci tak, jak rostou vaše požadavky zákazníků. Azure také nabízí spolehlivost, který je nezbytný pro vysokou dostupnost aplikace, dokonce i včetně převzetí služeb při selhání mezi různými oblastmi. [Webu Azure portal](https://portal.azure.com) umožňuje snadnou správu všech služeb Azure. Služby můžete také spravovat programově pomocí rozhraní API a šablon specifickou pro službu.

**Kdo by měl najdete v tomto**: Tato příručka je úvodem do platformy Azure pro vývojáře aplikací. Poskytuje rady a směrování, která je nutné začít vytváření nových aplikací v Azure nebo migraci stávajících aplikací do Azure.

## <a name="where-do-i-start"></a>Kde mám začít?

Všechny služby, které Azure nabízí může být složitý úkol zjistit, které služby je potřeba pro podporu architektury řešení. Tato část ukazuje služeb Azure, které vývojáři běžně používají. Seznam všech služeb Azure, najdete v článku [dokumentace ke službě Azure](../../index.md).

Nejprve se musíte rozhodnout o tom, jak hostovat vaši aplikaci v Azure. Je potřeba spravovat celou infrastrukturu jako virtuální počítač (VM). Můžete si zařízení správy platformy, které poskytuje Azure? Možná budete potřebovat architektura bez serveru pro hostitele pouze v provádění kódu?

Vaše aplikace potřebuje cloudového úložiště, které Azure nabízí několik možností. Můžete využít výhod Azure pro podnikové ověřování. Nejsou zde také nástroje pro vývoj pro cloudové a monitorování a většina hostitelské služby nabízejí integrace DevOps.

Nyní se Pojďme podívat na některé z konkrétních služeb, které doporučujeme zkoumání pro vaše aplikace.

### <a name="application-hosting"></a>Hostování aplikací

Azure poskytuje několik cloudových výpočetních nabídky ke spuštění aplikace, takže není nutné se starat o podrobnosti infrastruktury. Můžete snadno vertikálně navýšit kapacitu nebo horizontální navýšení kapacity prostředků využití vaší aplikace.

Azure nabízí služby, které podporují vývoje aplikací a potřebám hostování. Azure poskytuje infrastrukturu jako službu (IaaS) vám plnou kontrolu nad hostování aplikací. Platforma Azure jako služba (PaaS) nabídky poskytují plně spravované služby, třeba aplikací. Existuje i true hostování bez serveru v Azure kde všechno, co je třeba provést je napsat kód.

![Možnosti hostování aplikací Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Když chcete nejrychlejší cestu k publikování vašich webových projektů, vezměte v úvahu služby Azure App Service. App Service umožňuje snadné rozšíření vaší webové aplikace k podpoře vašich mobilních klientů a publikovat snadno spotřebované rozhraní REST API. Tato platforma poskytuje ověřování s použitím poskytovatelé služeb sociálních sítí, automatického škálování založeného na provoz, testování v produkčním prostředí a nepřetržitý a kontejnerových nasazení.

Můžete vytvářet webové aplikace, back-EndY mobilních aplikací a API apps.

Protože všechny typy aplikací tři sdílet modul runtime služby App Service, hostování webu, podporovat mobilní klienty a zpřístupněte vaše rozhraní API v Azure, vše z jednoho projektu nebo řešení. Další informace o App Service najdete v tématu [co je Azure Web Apps](../../app-service/overview.md).

App Service byly navržené s DevOps v úvahu. Podporuje různé nástroje pro publikování a průběžné integrace nasazení, včetně Githubu webhooky, Jenkins, Azure DevOps, TeamCity a dalších.

Můžete migrovat existující aplikace do služby App Service s použitím [online nástroje pro migraci](https://www.migratetoazure.net/).

>**Kdy použít**: Migrace stávajících webových aplikací do Azure, a když budete potřebovat plně spravované hostování platformy pro vaše webové aplikace pomocí App Service. App Service můžete použít také když budete potřebovat pro podporu mobilních klientů nebo vystavit rozhraní REST API s vaší aplikací.

>**Začínáme**: App Service umožňuje snadné vytvoření a nasazení vaší první [webovou aplikaci](../../app-service/app-service-web-get-started-dotnet.md), [mobilní aplikace](../../app-service-mobile/app-service-mobile-ios-get-started.md), nebo [aplikace API](../../app-service/app-service-web-tutorial-rest-api.md).

>**Vyzkoušejte si hned teď**: App Service umožňuje zřizovat krátkodobou aplikace a zkusit to platforma bez nutnosti registrace účtu Azure. Vyzkoušejte platformu a [vytvořte aplikaci Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Jako infrastruktura jako služba (IaaS) poskytovatele Azure vám umožní nasadit nebo migrovat aplikace do Windows nebo Linuxem. Společně s Azure Virtual Network Azure Virtual Machines podporuje pro nasazení Windows nebo Linuxem do Azure. S virtuálními počítači máte úplnou kontrolu nad konfigurací na počítači. Pokud používáte virtuální počítače, zodpovídáte za všechny server instalaci, konfiguraci, údržby a operační systém oprav softwaru.

Vzhledem k úrovni ovládací prvek, který máte s virtuálními počítači můžete spustit širokou škálu úloh serveru na Azure, které se nehodí do modelu PaaS. Mezi tyto úlohy patří databázové servery, Windows Server Active Directory a Microsoft SharePoint. Další informace najdete v článku dokumentace k virtuálním počítačům pro buď [Linux](/azure/virtual-machines/linux/) nebo [Windows](/azure/virtual-machines/windows/).

>**Kdy použít**: Použijte virtuální počítače, pokud chtějí mít plnou kontrolu nad vaší aplikační infrastruktury nebo migrovat místní aplikace úlohy do Azure bez nutnosti provádět změny.

>**Začínáme**: Vytvoření [virtuálního počítače s Linuxem](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) nebo [virtuálního počítače Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) z portálu Azure portal.

#### <a name="azure-functions-serverless"></a>Služba Azure Functions (bez serveru)

Spíše než z byste se museli starat o vytváření a správě celou aplikaci nebo infrastrukturu pro spouštění vašeho kódu. Co když může právě napište svůj kód a jeho spouštění v reakci na události nebo podle plánu?  [Služba Azure Functions](../../azure-functions/functions-overview.md) je a "bez serveru"-style nabídka, která umožňuje napsat přesně takový kód, je nutné. Pomocí funkcí provádění kódu se aktivuje požadavky HTTP, webhooky, události cloudové služby, nebo podle plánu. Vám umožní kódování v váš vývojový jazyk podle vlastní volby, například C\#, F\#, Node.js, Python nebo PHP. S využitím fakturace založený na spotřebě, platíte jenom za čas, který se spustí váš kód a Azure škáluje podle potřeby.

>**Kdy použít**: Pomocí Azure Functions v případě, že máte kód, který se aktivuje dalšími službami Azure, webové události nebo podle plánu. Funkce můžete použít také při nepotřebujete režii dokončený projekt hostovaná nebo pokud chcete platit za čas, na kterém běží váš kód. Další informace najdete v tématu [přehled Azure Functions](../../azure-functions/functions-overview.md).

>**Začínáme**: Postupujte podle kurzu rychlý start funkce a [vytvoření první funkce](../../azure-functions/functions-create-first-azure-function.md) z portálu.

>**Vyzkoušejte si hned teď**: Azure Functions umožňuje spuštění kódu bez nutnosti registrace účtu Azure. Vyzkoušejte si to teď za a [vytvoření první funkce Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric je platforma distribuovaných systémů, který usnadňuje vytváření, balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Poskytuje také komplexní možnosti správy aplikací pro zřizování, nasazování, sledování, upgradu nebo opravy chyb a odstraňování nasazených aplikací. Aplikace spouštěné ve sdíleném fondu počítačů můžou začínat v malém a škálování na stovky nebo tisíce počítačů podle potřeby.

Service Fabric podporuje webová rozhraní API s Open Web Interface pro .NET (OWIN) a ASP.NET Core. Poskytuje sady SDK pro vytváření služeb v Linuxu v .NET Core a Javy. Další informace o Service Fabric najdete v tématu [dokumentace ke službě Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

>**Kdy použít:** Service Fabric je dobrou volbou, pokud už vytváříte aplikace nebo přepsání existující aplikace, chcete-li využívají architekturu mikroslužeb. Pomocí Service Fabric, když budete potřebovat další kontrolu nad nebo přímý přístup k základní infrastruktury.

>**Začínáme:** [Vytvoření první aplikace Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Vylepšete své aplikace se službami Azure

Kromě hostování aplikací poskytuje Azure nabídek služeb, které můžete vylepšit funkce, vývoj a údržbu vašich aplikací v cloudu i místní.

#### <a name="hosted-storage-and-data-access"></a>Hostovaná úložiště a přístup k datům

Většina aplikací musí ukládat data, takže bez ohledu na to, jak se rozhodnete hostovat vaši aplikaci v Azure, zvažte jeden nebo více z následujících služeb úložiště a data.

-   **Azure Cosmos DB**: Globálně distribuovaná a vícemodelová databázová služba, která umožňuje Elasticky škálovat propustnost a úložiště napříč libovolným počtem geografických oblastí s komplexní smlouvou SLA. 
    >**Kdy použít:** Pokud vaše aplikace potřebuje dokumentů, tabulka nebo databáze grafů, včetně databází MongoDB s různé jasně definované modely konzistence. 

    >**Začínáme**: [Vytvoření webové aplikace Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Pokud jste vývojář, MongoDB, přečtěte si téma [vytvoření webové aplikace MongoDB pomocí služby Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

-   **Azure Storage**: Nabízí odolné a vysoce dostupné úložiště pro objekty BLOB, fronty, soubory a jiné druhy nerelační data. Úložiště nabízí základy úložiště pro virtuální počítače.

    >**Kdy použít**: Když aplikace ukládá nerelační data, jako jsou páry klíč hodnota (Table), objekty BLOB, sdílených složek nebo zpráv (fronty).

    >**Začínáme**: Vyberte jednu z těchto typů úložiště: [objekty BLOB](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabulky](../../cosmos-db/table-storage-how-to-use-dotnet.md), [fronty](../../storage/queues/storage-dotnet-how-to-use-queues.md), nebo [soubory](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Azure SQL Database**: Založené na Azure verze databázovém stroji Microsoft SQL Server pro ukládání relačních tabulkových dat v cloudu. SQL Database nabízí předvídatelný výkon, škálovatelnost bez výpadků, kontinuita podnikových procesů a ochranu dat.

    >**Kdy použít**: Pokud vaše aplikace vyžaduje úložiště dat poskytuje referenční integritu, transakční podporu a podporu dotazů TSQL.

    >**Začínáme**: [Vytvoření databáze SQL během několika minut pomocí webu Azure portal](../../sql-database/sql-database-get-started.md).


Můžete použít [Azure Data Factory](../../data-factory/introduction.md) pro přesun existujících místních dat do Azure. Pokud vám ještě nejsou připravené pro přesun dat do cloudu, [Hybrid Connections](../../biztalk-services/integration-hybrid-connection-overview.md) v BizTalk Services umožňuje připojení vaší služby App Service hostované aplikace k místním prostředkům. Můžete také připojit do úložiště a Azure data služeb ze svých místních aplikací.

#### <a name="docker-support"></a>Podpora dockeru

Kontejnery dockeru, určitou formu virtualizace operačního systému, umožňují nasazování aplikací efektivnější a předvídatelným způsobem. Kontejnerizované aplikace funguje v produkčním prostředí stejným způsobem jako na vývoj a testování systémy. Správa kontejnerů pomocí standardních nástrojů Dockeru. Vaše stávající znalosti a oblíbených opensourcových nástrojů můžete použít k nasazení a správě kontejnerových aplikací v Azure.

Azure poskytuje několik způsobů, jak používat kontejnery ve svých aplikacích.

-   **Rozšíření Azure Docker VM**: Umožňuje konfigurovat virtuální počítač s nástroji Dockeru tak, aby fungoval jako hostitele Docker.

    >**Kdy použít**: Pokud chcete generovat nasazení kontejnerů konzistentní vzhledem k aplikacím pro vaše aplikace na virtuálním počítači, nebo pokud chcete použít [Docker Compose](https://docs.docker.com/compose/overview/).

    >**Začínáme**: [Vytvoření prostředí pro Docker v Azure za použití rozšíření Docker VM](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Azure Container Service**: Umožňuje vytvářet, konfigurovat a spravovat cluster virtuálních počítačů, které je předem nakonfigurované spouštění kontejnerizovaných aplikací. Další informace o službě Container Service najdete v tématu [Úvod do služby Azure Container Service](../../container-service/container-service-intro.md).

    >**Kdy použít**: Když potřebujete připravené pro produkční prostředí, škálovatelné prostředí sestavení, které poskytují další plánování a nástroje pro správu, nebo při nasazení clusteru Docker Swarm.

    >**Začínáme**: [Nasazení clusteru služby Container Service](../../container-service/dcos-swarm/container-service-deployment.md).

-   **Docker Machine**: Umožňuje nainstalovat a spravovat pomocí příkazů docker-machine modul Docker na virtuálního hostitele.

    >**Kdy použít**: Když potřebujete rychle prototypy aplikace vytvořením jednoho hostitele Dockeru.

-   **Vlastní image Dockeru pro službu App Service**: Umožňuje používat kontejnery Dockeru ze služby container registry nebo kontejner Zákazník při nasazení webové aplikace v Linuxu.

    >**Kdy použít**: Při nasazení webové aplikace v Linuxu do image Dockeru.

    >**Začínáme**: [Použití vlastní image Dockeru pro službu App Service v Linuxu](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Je nezbytné pouze vědět, kdo používá vaše aplikace, ale také zabránit neoprávněnému přístupu k vašim prostředkům. Azure poskytuje několik způsobů, jak ověřovat klienty aplikace.

-   **Azure Active Directory (Azure AD)**: Microsoft víceklientské, cloudových identit a přístupu management service. Jednotného přihlašování (SSO) můžete přidat do vašich aplikací díky integraci s Azure AD. Vlastnosti adresáře můžete přistupovat pomocí Azure AD Graph API přímo nebo pomocí rozhraní Microsoft Graph API. Podpora služby Azure AD pro rozhraní framework autorizace OAuth 2.0 a Open ID Connect můžete integrovat s využitím nativní koncové body HTTP/REST a knihovny ověřování multiplatformní Azure AD.

    >**Kdy použít**: Pokud chcete poskytnout Jednotným přihlašováním, pracovat s daty grafické nebo ověřování založené na doméně uživatelů.

    >**Začínáme**: Další informace najdete v tématu [Příručka pro vývojáře Azure Active Directory](../../active-directory/develop/v1-overview.md).

-   **Ověřování pomocí služby App Service**: Při výběru služby App Service pro hostování vaší aplikace získáte také podporu integrovanou ověřování pro službu Azure AD, spolu s zprostředkovatelů sociálních identit, včetně služby Facebook, Google, Microsoft a Twitter.

    >**Kdy použít**: Pokud chcete povolit ověřování v aplikaci služby App Service pomocí služby Azure AD zprostředkovatele sociální identity, nebo obojí.

    >**Začínáme**: Další informace o ověřování ve službě App Service najdete v tématu [ověřování a autorizace ve službě Azure App Service](../../app-service/overview-authentication-authorization.md).

Další informace o osvědčených postupech zabezpečení v Azure najdete v tématu [osvědčené postupy zabezpečení Azure a vzory](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorování

Vaši aplikaci a běží v Azure můžete potřebovat moct sledovat výkon, podívejte se na problémy a zobrazit jak zákazníci používají vaši aplikaci. Azure poskytuje několik možností monitorování.

-   **Visual Studio Application Insights**: Hostované v Azure rozšiřitelnou analytickou službu, která se integruje se sadou Visual Studio k monitorování živé webové aplikace. Poskytuje data, která je potřeba průběžně vylepšovat výkon a použitelnost aplikací, ať už hostovaný na Azure, nebo ne.

    >**Začínáme**: Postupujte podle [Application Insights kurzu](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: Služba, která vám umožní vizualizovat, dotazy, směrovat, archivování a jednat na základě metrik a protokolů, které jsou generovány infrastrukturu Azure a prostředky. Monitor poskytuje zobrazení dat najdete v článku na webu Azure Portal a je jediným zdrojem pro monitorování prostředků Azure.
 
    >**Začínáme**: [Začínáme se službou Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integrace DevOps

Ať už je zřizování virtuálních počítačů nebo publikování vašich webových aplikací s využitím průběžné integrace, Azure se integruje s většinou oblíbených nástrojů DevOps. Díky podpoře nástrojů, jako je Jenkins, Githubu, Puppet, Chef, TeamCity, Ansible, Azure DevOps a dalších můžete pracovat s nástroji, že už máte a maximálně využívat stávající.

>**Vyzkoušejte si ho hned teď:** [Vyzkoušejte si několik integrace DevOps](https://azure.microsoft.com/try/devops/).

>**Začínáme**: Možnosti DevOps pro aplikace služby App Service najdete v tématu [průběžné nasazování do služby Azure App Service](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Oblast Azure

Azure je globální Cloudová platforma, která je obecně dostupná v mnoha oblastech po celém světě. Když si zřídíte službu, aplikace nebo virtuálního počítače v Azure, budete vyzváni, vyberte oblast, která představuje konkrétní datové centrum, kde běží aplikace nebo data se mají ukládat. Tyto oblasti odpovídají konkrétní umístění, které jsou publikovány na [oblastí Azure](https://azure.microsoft.com/regions/) stránky.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Zvolte nejlepší oblast pro svoji aplikaci a data

Jednou z výhod používání Azure je, že můžete nasadit aplikace do různých datových centrech po celém světě. Oblast, kterou zvolíte, může ovlivnit výkon vaší aplikace. Například je lepší zvolit oblast, která je blíž k většině vašich zákazníků ke snížení latence v síťové požadavky. Můžete také chtít vyberte oblast pro splnění zákonných požadavků na distribuce aplikace v určitých zemích. Vždy je osvědčeným postupem je ukládání dat aplikací ve stejném datovém centru nebo v datacentru jako téměř co nejblíže k datovému centru, který je hostitelem vaší aplikace.

### <a name="multi-region-apps"></a>Aplikace ve více oblastech

I když je nepravděpodobné, není možné celého datového centra přechod do režimu offline z důvodu události jako je přírodní katastrofa nebo selhání Internet. Je vhodné hostovat důležitá obchodní aplikace ve více než jednom datacentru pro zajištění maximální dostupnosti. Použití více oblastí může také snížit latenci pro globální uživatele a poskytnout další příležitosti pro flexibilitu při aktualizaci aplikace.

Některé služby, jako je například virtuální počítač a aplikační služby, použijte [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) povolit podporu více oblastí se převzetí služeb při selhání mezi oblastmi na podporu vysoké dostupnosti pro podniky. Příklad najdete v tématu [referenční architektura Azure: Spuštění webové aplikace v několika oblastech](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Kdy použít**: Pokud máte enterprise a aplikací s vysokou dostupností, které využívají samosprávné převzetí služeb při selhání a replikace.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Jak můžu spravovat svoje aplikace a projekty?

Azure poskytuje bohatou sadu možností pro vás k vytváření a správě prostředků Azure, aplikace a projekty – jak prostřednictvím kódu programu a [webu Azure portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Rozhraní příkazového řádku a Powershellu

Azure nabízí dva způsoby, jak spravovat aplikace a služby z příkazového řádku pomocí prostředí Bash, terminál, příkazový řádek nebo váš nástroj příkazového řádku podle výběru. Obvykle stejné úlohy můžete provádět z příkazového řádku jako v portálu Azure, jako je například vytváření a konfiguraci virtuálních počítačů, virtuálních sítí, webových aplikací a dalších služeb.

-   [Rozhraní příkazového řádku Azure (CLI)](../../xplat-cli-install.md): Umožňuje připojení k předplatnému Azure a programovat různé úlohy s prostředky Azure z příkazového řádku.

-   [Prostředí Azure PowerShell](../../powershell-install-configure.md): Poskytuje sadu modulů s rutinami, které vám umožní spravovat prostředky Azure pomocí prostředí Windows PowerShell.

### <a name="azure-portal"></a>portál Azure

Na webu Azure portal je webová aplikace, který vám pomůže vytvořit, spravovat a odebrání prostředků Azure a služeb. Na webu Azure portal se nachází na <https://portal.azure.com>. Zahrnuje přizpůsobitelný řídicí panel, nástroje pro správu prostředků Azure a přístup k nastavení odběru a fakturační údaje. Další informace najdete v tématu [přehled webu Azure portal](../../azure-portal-overview.md).

### <a name="rest-apis"></a>Rozhraní REST API

Azure je postavený na sadu rozhraní REST API, která podporuje uživatelské rozhraní portálu Azure. Většina těchto rozhraní REST API jsou podporovány také umožňuje programově zřizovat a spravovat vaše prostředky Azure a aplikace z jakéhokoli zařízení využívajících Internet. Kompletní sada dokumentace k rozhraní REST API najdete v článku [reference k sadě Azure REST SDK](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>Rozhraní API

Kromě rozhraní REST API pro řadou služeb Azure vám také umožní prostřednictvím kódu programu spravovat prostředky z vašich aplikací pomocí sady SDK Azure specifické pro platformu, včetně sad SDK pro vývoj s následujícími platformami:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

Služby, jako [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) a [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) poskytuje sady SDK na straně klienta umožňuje přístup ke službám z webové a mobilní klientské aplikace.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Vaše aplikace běžela na Azure pravděpodobně zahrnuje práci s více službami Azure, které použijte stejný životní cyklus a můžete představit jako logickou jednotku. Například může být webové aplikace pomocí Web Apps, SQL Database, Storage, Azure Cache pro Redis a služby Azure Content Delivery Network. [Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md) vám umožňuje pracovat s prostředky v aplikaci jako se skupinou. Můžete nasadit, aktualizovat nebo odstranit všechny prostředky v rámci jediné koordinované operace.

Kromě logicky seskupování a správu souvisejících prostředků, Azure Resource Manageru obsahuje možnosti nasazení, které umožňují přizpůsobit nasazení a konfigurace související prostředky. Například pomocí Resource Manageru můžete nasadit a nakonfigurovat aplikaci, která se skládá z několika virtuálních počítačů, nástroj pro vyrovnávání zatížení a Azure SQL database jako jeden celek.

Vývoj těchto nasazení pomocí šablony Azure Resource Manageru, který je dokument ve formátu JSON. Šablony umožňují definovat nasazení a Správa aplikací pomocí deklarativních šablon místo skriptů. Šablony může fungovat v různých prostředích, jako je například testování, Fázování a produkce. Například pomocí šablony můžete přidat tlačítko do úložiště GitHub, který se nasazuje kód v úložišti sady služeb Azure s jediným kliknutím.

>**Kdy použít**: Použití šablon Resource Manageru, pokud chcete nasazení založené na šablonách pro vaši aplikaci, která může spravovat programově pomocí rozhraní REST API, rozhraní příkazového řádku Azure a Azure Powershellu.

>**Začínáme**: Abyste mohli začít používat šablony, najdete v článku [šablon pro vytváření Azure Resource Manageru](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Vysvětlení účtů, předplatných a fakturace

Jako vývojáři rádi Ponořte se hlouběji do kódu a pokuste se začít pracovat tak rychle s vytvářením naše aplikace spustit. Určitě chcete doporučujeme, abyste mohli začít pracovat v Azure, stejně snadno. Abyste se mohli snadno, Azure nabízí [bezplatnou zkušební verzi](https://azure.microsoft.com/free/). Některé služby probíhat dokonce na "Vyzkoušet zdarma" funkce, jako je třeba [služby Azure App Service](https://tryappservice.azure.com/), který nevyžaduje, aby vám dokonce vytvořit účet. Jako zábavné je to Ponořte se do psaní kódu a nasazení aplikace do Azure, je také důležité pochopit, jak Azure funguje z hlediska uživatelských účtů, předplatných a fakturace nějakou dobu trvat.

### <a name="what-is-an-azure-account"></a>Co je účet Azure?

Aby bylo možné vytvořit nebo pracovat s předplatným Azure, musíte mít účet Azure. Účet Azure je jednoduše identitu ve službě Azure AD nebo v adresáři, jako je pracovní nebo školní organizace, který je důvěryhodný pro Azure AD. Pokud jste nepatří do této organizace, můžete vždy vytvořit odběr pomocí Account Microsoft, která je důvěryhodná pro Azure AD. Další informace o integraci místní Windows Server Active Directory se službou Azure AD najdete v tématu [integrace místních identit s Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Každé předplatné služby Azure má vztah důvěryhodnosti s instancí služby Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Další informace najdete v tématu [předplatné Azure propojeno se službou Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Kromě definování jednotlivé Azure účtu identit, také nazývané *uživatelé*, můžete také definujte *skupiny* ve službě Azure AD. Vytváření skupin uživatelů je dobrým způsobem, jak spravovat přístup k prostředkům v rámci předplatného pomocí řízení přístupu na základě role (RBAC). Zjistěte, jak vytvářet skupiny, najdete v článku [vytvoření skupiny v Azure Active Directory ve verzi preview](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Můžete také vytvořit a spravovat skupiny podle [pomocí prostředí PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Správa předplatných

Předplatné je logické seskupení služeb Azure, který je propojený s účtem Azure. Jeden účet Azure může obsahovat několik předplatných. Fakturace za služby Azure se provádí na základě za předplatné. Seznam nabídek k dispozici předplatné podle typu, najdete v části [podrobnosti nabídky Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Předplatná Azure mít účet správce, který má plnou kontrolu nad předplatné, a Správce služby, který má kontrolu nad všechny služby v rámci předplatného. Informace o správcích klasickém modelu předplatného najdete v tématu [přidat nebo změnit správce předplatného Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Kromě správci, lze udělit jednotlivým účtům podrobné řízení prostředků Azure pomocí [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Skupiny prostředků

Když si zřídíte nové služby Azure, můžete udělat v daném předplatném. Jednotlivé služby Azure, které se také označují jako prostředky, se vytvoří v rámci skupiny prostředků. Skupiny prostředků usnadňují nasazování a správě prostředků vaší aplikace. Skupina prostředků by měl obsahovat všechny prostředky pro vaše aplikace, která chcete pro práci s jako celek. Prostředky lze přesunout mezi skupinami prostředků a dokonce i do různých předplatných. Další informace o přesun prostředků najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../resource-group-move-resources.md).

V Průzkumníku prostředků Azure je skvělý nástroj pro vizualizaci prostředky, které jste vytvořili v rámci vašeho předplatného. Další informace najdete v tématu [Průzkumníku prostředků Azure použijte k zobrazení a úpravám prostředků](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Udělení přístupu k prostředkům

Když povolíte přístup k prostředkům Azure, je vždy vhodné zajistit uživatele s nejnižšími oprávněními, který je potřeba provést danou úlohu.

-   **Řízení přístupu na základě role (RBAC)**: V Azure, můžete udělit přístup k uživatelským účtům (objekty zabezpečení) v zadaném oboru: předplatné, skupinu prostředků nebo jednotlivé prostředky. RBAC umožňuje nasadit sadu prostředků do skupiny prostředků a udělení oprávnění pro konkrétního uživatele nebo skupiny. To vám také umožní omezit přístup k prostředkům, které patří k cílové skupině prostředků. Můžete také udělit přístup na jeden prostředek, jako je například virtuální počítač nebo virtuální sítě. Pokud chcete udělit přístup, přiřadíte roli pro uživatele, skupinu nebo instanční objekt. Existuje mnoho předdefinovaných rolí, a můžete také definovat vlastní role. Další informace najdete v tématu [co je řízení přístupu na základě rolí (RBAC)?](../../role-based-access-control/overview.md).

    >**Kdy použít**: Když potřebujete propracovanou správu přístupu pro uživatele a skupiny, nebo když budete chtít nastavit uživatele jako vlastníka předplatného.

    >**Začínáme**: Další informace najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md).

-   **Instanční objekty služby**: Kromě poskytování přístupu pro objekty zabezpečení uživatelů a skupin, můžete stejný přístup udělit instančnímu objektu služby.

    > **Kdy použít**: Když už prostřednictvím kódu programu správu prostředků Azure nebo udělení přístupu pro aplikace. Další informace najdete v tématu [vytvoření aplikace Active Directory a instančního objektu](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Značky

Azure Resource Manager vám umožňuje přiřazení vlastních značek k jednotlivé prostředky. Značky, které jsou páry klíč hodnota, může být užitečné, když potřebujete uspořádat prostředky pro fakturaci nebo monitorování. Značky poskytují způsob, jak sledovat prostředky v rámci více skupin prostředků. Můžete přiřadit značky na portálu pro šablonu Azure Resource Manageru, nebo prostřednictvím kódu programu, pomocí rozhraní REST API, rozhraní příkazového řádku Azure nebo Powershellu. Jednotlivé prostředky můžete přiřadit více značek. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Fakturace

V přesunu místních výpočetních služeb hostovaných v cloudu sledování a odhadnout využití služeb a související náklady jsou důležité aspekty. Je důležité, abyste mohli provést odhad nové prostředky nákladů pro spuštění na každý měsíc. Musíte také mít možnost do projektu, jak vypadá fakturace za daný měsíc podle aktuální útratu.

#### <a name="get-resource-usage-data"></a>Získat data o využití prostředků

Azure poskytuje sadu REST API pro fakturaci, které poskytují přístup k využití prostředků a informace metadat pro předplatná Azure. Tato rozhraní API Billing vám tak umožnili lepší předpovídat a spravovat náklady na Azure. Můžete sledovat a analyzují výdaje hodinový přírůstky, vytvářet útraty upozornění a předpovídat budoucí fakturace na základě aktuálního využití trendů.

>**Začínáme**: Další informace o použití rozhraní API pro fakturaci, viz [přehled Azure Billing využití a RateCard API](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Předpovídat budoucí náklady

I když je náročné odhadnout náklady na předem, Azure má [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) , které můžete použít při odhadnout náklady na nasazené prostředky. Můžete také použít okno fakturace na portálu a rozhraní REST API pro fakturaci odhadnout budoucí náklady podle aktuálního využití.

>**Začínáme**: Zobrazit [přehled Azure Billing využití a RateCard API](../../billing-usage-rate-card-overview.md).
