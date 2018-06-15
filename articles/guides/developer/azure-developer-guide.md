---
title: Get spuštěn Průvodce pro vývojáře v Azure | Microsoft Docs
description: Toto téma obsahuje základní informace pro vývojáře, kteří chtějí Začínáme pomocí platformy Microsoft Azure pro potřeby jejich vývoj.
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
ms.openlocfilehash: e0a04d44edf39c90533c59d67ea2dfce69f092cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33771088"
---
# <a name="get-started-guide-for-azure-developers"></a>Úvodní příručka pro vývojáře v Azure

## <a name="what-is-azure"></a>Co je Azure?

Azure je kompletní cloud platforma, která může hostování existující aplikace, zjednodušují vývoj nových aplikací a i zvýšit místní aplikace. Azure integruje cloudové služby, které potřebujete k vývoji, testování, nasazení a Správa aplikací – s využitím efektivitu technologie cloud computing.

Hostováním aplikací v Azure, můžete začněte v malém rozsahu a snadného škálování aplikace s růstem vaší poptávku zákazníků. Azure také nabízí spolehlivost, který je potřeba pro vysokou dostupnost aplikace, i včetně převzetí služeb při selhání mezi různých oblastech. [Portál Azure](https://portal.azure.com) umožňuje snadno spravovat všechny vaše služby Azure. Můžete také spravovat vaše služby programově pomocí rozhraní API a šablony specifickou pro službu.

**Komu to určen**: Tento průvodce je Úvod do platformy Azure pro vývojáře aplikací. Poskytuje pokyny a směr, který je nutné začít vytvářet nové aplikace v Azure nebo migraci existujících aplikací do Azure.

## <a name="where-do-i-start"></a>Kde mám začít?

U všech služeb, které nabízí Azure může být obtížné a pokuste se zjistit služby, které budete potřebovat k podpoře vaší architektury řešení. V této části jsou zdůrazněné služby Azure, které vývojáři běžně používají. Seznam všech služeb Azure, najdete v článku [dokumentace k Azure](../../index.md).

Nejdřív musíte rozhodnout o tom, jak hostovat svoji aplikaci v Azure. Potřebujete spravovat celé infrastruktury jako virtuální počítač (VM). Můžete použít možnosti správy platformy, které poskytuje Azure? Možná potřebovat bez serveru rozhraní pro provádění kódu hostitele pouze?

Aplikace musí cloudového úložiště, které Azure poskytuje několik možností. Můžete využít výhod ověřování Azure enterprise. Existují zde také nástrojů pro vývoj cloudové a monitorování a většina hostitelských služeb nabízejí integraci DevOps.

Nyní se podívejme se na některé z konkrétní služby, které doporučujeme příčin pro vaše aplikace.

### <a name="application-hosting"></a>Hostování aplikací

Azure poskytuje že několik cloudové výpočetní nabídky k aplikaci spustit, takže nemusíte starat o podrobnostech infrastruktury. Můžete snadno škálovat nahoru i horizontální navýšení kapacity vašich prostředků s růstem využití vaší aplikace.

Azure nabízí služby, které podporují vaší aplikace vývoj a hostování potřebám. Azure poskytuje infrastrukturu jako službu (IaaS) tak, abyste získali plnou kontrolu nad hostování aplikací. Azure platforma jako služba (PaaS) nabídky poskytují plně spravované služby potřebné k výkonu aplikace. Existuje i true bez serveru hostování v Azure kde všechny, které musíte udělat je zápis kódu.

![Hostování možnosti Azure aplikace](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Pokud chcete, aby nejrychlejší cestu k publikování vašich webových projektů, zvažte Azure App Service. Služby App Service umožňuje snadno rozšířit vaše webové aplikace pro podporu mobilních klientů a publikování snadno spotřebované rozhraní REST API. Tato platforma poskytuje ověřování s využitím sociálních sítí, na základě provoz automatické škálování testování v produkčním a nepřetržitý a na základě kontejner nasazení.

Můžete vytvořit webové aplikace, back-EndY mobilní aplikace a aplikace API.

Protože všechny typy aplikací tři sdílet runtime služby App Service, hostí web, podporu mobilních klientů a vystavit vaše rozhraní API v Azure, všechny ze stejné projekt nebo řešení. Další informace o službě App Service naleznete v tématu [co je Azure Web Apps](../../app-service/app-service-web-overview.md).

Služby App Service má byly navrženy s DevOps v paměti. Podporuje různé nástroje pro publikování a nepřetržité integrace nasazení, včetně Githubu webhooků, volaných, Visual Studio Team Services, TeamCity a dalších.

Můžete migrovat existující aplikace do služby App Service pomocí [nástroj pro migraci online](https://www.migratetoazure.net/).

>**Kdy použít**: App Service použít při jste migraci existující webové aplikace do Azure, a pokud budete potřebovat plně spravovaná hostující platforma pro webové aplikace. Také můžete službě App Service když potřebujete pro podporu mobilních klientů nebo vystavit rozhraní REST API s vaší aplikací.

>**Začínáme**: služby App Service umožňuje snadno vytvářet a nasazovat první [webové aplikace](../../app-service/app-service-web-get-started-dotnet.md), [mobilní aplikace](../../app-service-mobile/app-service-mobile-ios-get-started.md), nebo [aplikace API](../../app-service/app-service-web-tutorial-rest-api.md).

>**Teď vyzkoušet**: App Service umožňuje zřídit krátkodobou aplikaci a zkuste to platformou bez nutnosti zaregistrujte si účet Azure. Zkuste platformu a [vytvořit aplikaci aplikační služby Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Jako infrastruktura jako služba (IaaS) zprostředkovatele Azure umožňuje nasadit nebo migrovat aplikace systému Windows nebo virtuální počítače s Linuxem. Virtuální počítače Azure společně s Azure Virtual Network, podporuje nasazení systému Windows nebo virtuální počítače s Linuxem do Azure. S virtuálními počítači máte celkový kontrolu nad konfigurace počítače. Pokud používáte virtuální počítače, jste zodpovědná za serveru instalaci, konfiguraci, údržby a operační systém opravy softwaru.

Z důvodu úroveň kontroly, zda máte s virtuálními počítači můžete spustit širokou škálu úloh serveru v Azure, které se nehodí do modelu PaaS. Mezi tyto úlohy patří databázových serverů, Windows Server Active Directory a Microsoft SharePoint. Další informace najdete v dokumentaci virtuálních počítačů pro buď [Linux](/azure/virtual-machines/linux/) nebo [Windows](/azure/virtual-machines/windows/).

>**Kdy použít**: virtuální počítače použít pokud chcete úplné řízení přes infrastrukturu aplikace nebo místní aplikace úlohy migrace do Azure bez nutnosti provádět změny.

>**Začínáme**: vytvoření [virtuálního počítače s Linuxem](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) nebo [virtuální počítač s Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) z portálu Azure.

#### <a name="azure-functions-serverless"></a>Azure Functions (bez serveru)

Spíše než z starosti se vytváření a správě celou aplikaci nebo infrastrukturu pro spouštění vašeho kódu. Co v případě může právě zápisu kódu a mějte ho spustit v reakci na události nebo podle plánu?  [Azure Functions](../../azure-functions/functions-overview.md) je a "bez serveru"-nabídky Styl, který umožňuje zapisovat pouze kód, budete potřebovat. S funkcemi provádění kódu se aktivuje požadavků HTTP, webhooků, události cloudové služby, nebo podle plánu. Můžete kód v jazyce vývoj podle vlastní volby, například C\#, F\#, Node.js, Python nebo PHP. S fakturace na základě spotřeby platíte jen za čas, která se spustí kódu a Azure škáluje podle potřeby.

>**Kdy použít**: Azure Functions použít až budete mít kód, který je aktivován jinými službami Azure webové události, nebo podle plánu. Můžete také použít funkce Pokud nepotřebujete nároky na dokončení hostované projektu nebo pokud chcete platit za čas, kdy byl kód spuštěný. Další informace najdete v tématu [přehled Azure Functions](../../azure-functions/functions-overview.md).

>**Začínáme**: postupujte podle funkce Rychlý úvodní kurz pro [vytvoření první funkce](../../azure-functions/functions-create-first-azure-function.md) z portálu.

>**Teď vyzkoušet**: Azure Functions umožňuje svůj kód spustit bez nutnosti zaregistrujte si účet Azure. Vyzkoušet nyní na a [vytvoření první funkce Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric je platforma distribuovaných systémů, která umožňuje snadno vytvářet, balíčku, nasazení a správě škálovatelného a spolehlivého mikroslužeb. Také poskytuje komplexní aplikace možnosti správy pro zřizování, nasazení, monitorování, upgrade nebo opravy a odstranění nasazené aplikace. Aplikace, které se spouštějí na sdílenému fondu počítačů, můžete začněte v malém rozsahu a škálovat stovek nebo tisíců počítačů podle potřeby.

Service Fabric podporuje WebAPI s Open Web Interface pro .NET (OWIN) a ASP.NET Core. Poskytuje sady SDK pro vytváření služeb v systému Linux v .NET Core a Java. Další informace o Service Fabric, najdete v článku [Service Fabric studijní](https://azure.microsoft.com/documentation/learning-paths/service-fabric/).

>**Kdy použít:** Service Fabric je vhodné použít při vytváření aplikací nebo přepisování stávající aplikace pro použití architektury mikroslužby. Service Fabric použijte, pokud potřebujete další kontrolu nad nebo přímý přístup k podkladové infrastruktury.

>**Začínáme:** [vytvoření první aplikace Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Vylepšení aplikace se službami Azure

Kromě hostování aplikací Azure poskytuje nabídky služeb, zlepšující funkce, vývoj a údržba vaší aplikace v cloudové i místní.

#### <a name="hosted-storage-and-data-access"></a>Hostované úložiště a přístup k datům

Většina aplikací musí ukládání dat, takže bez ohledu na to, jak se rozhodnete hostování vaší aplikace v Azure, zvažte jednu nebo více z následujících služeb úložiště a data.

-   **Azure Cosmos DB**: Služba globálně distribuované a více modelech databáze, která umožňuje Elasticky škálovat propustnost a úložiště napříč libovolný počet zeměpisné oblasti s komplexní SLA. 
    >**Kdy použít:** Pokud aplikace potřebuje dokument, tabulka nebo graf databáze, včetně databáze MongoDB, s více dobře definovaný konzistence modelů. 

    >**Začínáme**: [sestavení webové aplikace Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Pokud jste vývojář MongoDB, najdete v části [sestavení MongoDB webovou aplikaci s Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

-   **Úložiště Azure**: nabízí odolné, vysoce dostupné úložiště pro objekty BLOB, fronty, soubory a jiné druhy nonrelational data. Úložiště nabízí základy úložiště pro virtuální počítače.

    >**Kdy použít**: Pokud aplikace ukládá nonrelational dat, jako jsou páry klíč hodnota (tabulky), objekty BLOB, soubory sdílených složek nebo zpráv (fronty).

    >**Začínáme**: vyberte jednu z těchto typů úložiště: [objekty BLOB](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabulky](../../cosmos-db/table-storage-how-to-use-dotnet.md), [fronty](../../storage/queues/storage-dotnet-how-to-use-queues.md), nebo [soubory](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Azure SQL Database**: verzi služby Azure modul Microsoft SQL Server pro ukládání relačních tabulková data v cloudu. SQL Database nabízí předvídatelný výkon, škálovatelnost bez výpadku, kontinuity podnikových procesů a ochranu dat.

    >**Kdy použít**: Pokud vaše aplikace vyžaduje úložiště dat se referenční integrity, transakční podporu a podporu pro dotazy TSQL.

    >**Začínáme**: [vytvořit databázi SQL v minut pomocí portálu Azure](../../sql-database/sql-database-get-started.md).


Můžete použít [Azure Data Factory](../../data-factory/introduction.md) přesunout existující místní data do Azure. Pokud jste ještě nejsou připraveny pro přesun dat do cloudu, [hybridní připojení](../../biztalk-services/integration-hybrid-connection-overview.md) v BizTalk Services umožňuje připojení App Service hostované aplikace k místním prostředkům. Můžete také připojit k Azure data a úložiště služby z vaší místní aplikace.

#### <a name="docker-support"></a>Podpora docker

Docker kontejnery, formu virtualizace operačního systému, umožňuje nasadit aplikace způsobem efektivnější a předvídatelný. Kontejnerizované aplikace funguje v produkčním prostředí stejným způsobem jako na vaše systémy vývoj a testování. Kontejnery můžete spravovat pomocí nástrojů pro standardní Docker. Existujících dovedností a oblíbených open source nástroje, můžete použít k nasazení a správě aplikace založené na kontejneru v Azure.

Azure poskytuje několik způsobů, jak používat kontejnery ve svých aplikacích.

-   **Rozšíření Azure virtuální počítač Docker**: vám umožní nakonfigurovat virtuální počítač pomocí nástroje Docker tak, aby fungoval jako Docker hostitele.

    >**Kdy použít**: Pokud chcete generovat nasazení konzistentní kontejner pro vaše aplikace na virtuálním počítači, nebo pokud chcete použít [Docker Compose](https://docs.docker.com/compose/overview/).

    >**Začínáme**: [vytvořit prostředí Docker v Azure pomocí rozšíření virtuálního počítače Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Azure Container Service**: umožňuje vytvářet, konfigurovat a spravovat cluster virtuálních počítačů, které jsou předem nakonfigurován ke spuštění kontejnerizované aplikací. Další informace o kontejneru služby najdete v tématu [Azure Container Service ÚVOD](../../container-service/container-service-intro.md).

    >**Kdy použít**: Pokud potřebujete vytvořit produkční prostředí, škálovatelné prostředí, které poskytují další plánování a nástroje pro správu, nebo když nasazujete clusteru Docker Swarm.

    >**Začínáme**: [nasadit cluster Container Service](../../container-service/dcos-swarm/container-service-deployment.md).

-   **Počítač docker**: umožňuje nainstalovat a spravovat pomocí příkazů počítač docker modulu Docker na virtuální hostitele.

    >**Kdy použít**: když potřebujete rychle prototypu aplikace tak, že vytvoříte jeden hostitel Docker.

-   **Vlastní image Docker pro službu App Service**: umožňuje používat kontejnery Docker z registru kontejneru nebo kontejner zákazníka při nasazení webové aplikace v systému Linux.

    >**Kdy použít**: při nasazení webové aplikace v systému Linux do bitové kopie Docker.

    >**Začínáme**: [používat vlastní image Docker pro službu App Service v systému Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Je velmi důležitý nejenom vědět, kdo používá vaše aplikace, ale také kvůli zabránění neoprávněného přístupu k prostředkům. Azure poskytuje několik způsobů, jak ověřit vaši klienti aplikace.

-   **Azure Active Directory (Azure AD)**: Microsoft víceklientských, cloudových identit a přístupu správy služby. Díky integraci s Azure AD můžete přidat jednotného přihlašování (SSO) pro vaše aplikace. Vlastnosti directory můžete přistupovat pomocí Azure AD Graph API přímo nebo Microsoft Graph API. Můžete integrovat s podporou služby Azure AD pro OAuth2.0 autorizace framework a Open ID Connect pomocí nativní koncové body protokolu HTTP nebo REST a knihovny ověřování multiplatform Azure AD.

    >**Kdy použít**: Pokud chcete zadat přihlašováním, pracovat s daty na základě grafu nebo ověřování založené na doméně uživatelů.

    >**Začínáme**: Další informace najdete v tématu [Příručka pro vývojáře Azure Active Directory](../../active-directory/develop/active-directory-developers-guide.md).

-   **Ověřování služby aplikace**: když zvolíte služby App Service k hostování vaší aplikace, získáte také integrované ověření podporu pro Azure AD, společně s zprostředkovatelů identity sociálních – včetně Facebook, Google, Microsoft a Twitter.

    >**Kdy použít**: Pokud chcete povolit ověřování v aplikaci služby App Service pomocí Azure AD, zprostředkovatelů sociálních identity, nebo obojí.

    >**Začínáme**: Další informace o ověřování v App Service naleznete v tématu [ověřování a autorizace ve službě Azure App Service](../../app-service/app-service-authentication-overview.md).

Další informace o doporučeném zabezpečení v Azure najdete v tématu [osvědčené postupy zabezpečení Azure a vzory](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorování

Vaši aplikaci a běží v Azure budete potřebovat moci sledovat výkon, sledovat problémy a najdete v části jak zákazníci používají vaši aplikaci. Azure poskytuje několik možností monitorování.

-   **Visual Studio Application Insights**: hostované služby Azure rozšiřitelnou analytickou službu, který se integruje s Visual Studio pro monitorování provozu webových aplikací. Poskytuje data, která budete muset neustále zlepšovat výkonnost a použitelnost aplikací, zda jste hostované v Azure, nebo ne.

    >**Začínáme**: postupujte podle [Application Insights kurzu](../../application-insights/app-insights-overview.md).

-   **Azure monitorování**: služba, která umožňuje vizualizovat, dotaz, směrování, archivování a fungují v metriky a protokoly, které jsou generovány nástrojem infrastrukturu Azure a prostředky. Monitorování poskytuje zobrazení dat najdete v článku na webu Azure portal a je jednoho zdroje pro monitorování prostředků Azure.
 
    >**Začínáme**: [Začínáme s Azure monitorování](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integrace DevOps

Jestli je zřizování virtuálních počítačů nebo publikování webových aplikacích s průběžnou integraci, Azure se integruje s většinou oblíbených nástrojů DevOps. S podporou nástroje, například volaných, Githubu, Puppet, Chef, TeamCity, Ansible, služby VSTS a dalších můžete pracovat s nástroji, že už máte a maximalizovat vaše stávající prostředí.

>**Teď vyzkoušet:** [vyzkoušet několik integrace DevOps](https://azure.microsoft.com/try/devops/).

>**Začínáme**: možnosti DevOps pro aplikaci služby App Service najdete v sekci [průběžné nasazování do služby Azure App Service](../../app-service/app-service-continuous-deployment.md).


## <a name="azure-regions"></a>Oblast Azure

Azure je globální Cloudová platforma, která je obecně k dispozici v mnoha oblastech po celém světě. Při zřizování služby, aplikace nebo virtuální počítač v Azure, budete vyzváni k vyberte oblast, která představuje konkrétní datové centrum, kde běží aplikace nebo kde jsou data uložena. Tyto oblasti odpovídají konkrétní umístění, které jsou publikovány na [oblastí Azure](https://azure.microsoft.com/regions/) stránky.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Vyberte oblast nejvhodnější pro aplikace a data

Jednou z výhod používání Azure je, že můžete nasadit aplikace do různých datových centrech po celém světě. Oblasti, který zvolíte, může ovlivnit výkon aplikace. Například je lepší zvolit oblasti, která bude co nejblíže ke většinu zákazníkům snížit latenci v síťové požadavky. Také můžete chtít vyberte oblast pro splnění zákonných požadavků na distribuci aplikace v některých zemích. Vždycky je osvědčeným postupem ukládání dat aplikací ve stejném datovém centru nebo v datovém centru jako téměř nejblíže do datového centra, který je hostitelem vaší aplikace.

### <a name="multi-region-apps"></a>Aplikace s více oblast

I když je nepravděpodobné, není znemožňuje, aby celého datového centra pro přechod offline z důvodu událost například přírodní katastrofě nebo selhání Internetu. Je osvědčeným postupem důležitá obchodní aplikace hostitele ve více než jeden datovém centru k dosažení maximální dostupnosti. Použití více oblastí může také snížit latenci pro globální uživatele a poskytují další možnosti flexibilitu při aktualizaci aplikace.

Některé služby, jako je virtuální počítač a aplikační služby, použijte [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) povolení podpory více oblasti s převzetí služeb při selhání mezi oblastmi k podpoře vysoké dostupnosti podnikové aplikace. Příklad, naleznete v části [Azure referenční architektura: webové aplikace s vysokou dostupností](../../guidance/guidance-web-apps-multi-region.md).

>**Kdy použít**: Pokud máte enterprise a vysoká dostupnost aplikací využívajících replikaci a převzetí služeb při selhání.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Jak spravovat mých aplikací a projektů?

Azure poskytuje bohatou sadu možností pro můžete vytvářet a spravovat prostředky Azure, aplikací a projektů – jak programově a v [portál Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Rozhraní příkazového řádku a prostředí PowerShell

Azure nabízí dva způsoby, jak spravovat vašim aplikacím a službám z příkazového řádku pomocí Bash, terminálu, příkazový řádek nebo vaše příkazového řádku nástroje výběru. Obvykle můžete stejné úlohy provádět z příkazového řádku jako v portálu Azure, jako je například vytváření a konfigurace virtuálních počítačů, virtuálních sítí, webové aplikace a dalším službám.

-   [Rozhraní příkazového řádku Azure (CLI)](../../xplat-cli-install.md): umožňuje připojení k předplatnému Azure a program různé úlohy s prostředky Azure z příkazového řádku.

-   [Prostředí Azure PowerShell](../../powershell-install-configure.md): poskytuje sadu modulů s rutinami, které vám umožní spravovat prostředky Azure pomocí prostředí Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

Portál Azure je webové aplikace, která slouží k vytváření, správu a odebrat prostředky Azure a služby. Portál Azure se nachází v <https://portal.azure.com>. Zahrnuje přizpůsobitelný řídicího panelu, nástroje pro správu prostředků Azure a přístup k nastavení odběru a fakturační informace. Další informace najdete v tématu [přehled portálu Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>Rozhraní REST API

Azure je založen na sadu rozhraní REST API, která podporuje uživatelské rozhraní portálu Azure. Většina těchto rozhraní REST API jsou podporovány také umožnit vám prostřednictvím kódu programu zřizovat a spravovat z libovolného zařízení využívajících Internet vašich prostředků Azure a v aplikacích. Kompletní sadu dokumentace k REST API, najdete v článku [referenční informace sady Azure REST SDK](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>Rozhraní API

Kromě rozhraní REST API řadou služeb Azure vám také umožní programově spravovat prostředky z vaší aplikace pomocí sady SDK specifické pro platformu Azure, včetně sady SDK pro následující vývojové platformy:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

Služby, jako [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) a [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) zadejte klientské sady SDK, abyste mohli získat přístup ke službám z webových a mobilních klientských aplikací.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Spuštění aplikace v Azure pravděpodobně zahrnuje práci s více služeb Azure, což postupujte podle stejný životní cyklus a si lze představit jako logickou jednotku. Webové aplikace může například použít webové aplikace, databáze SQL, úložiště, Azure Redis Cache a Azure Content Delivery Network services. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) vám umožňuje pracovat s prostředky v aplikaci jako se skupinou. Můžete nasadit, aktualizovat nebo odstranit všechny prostředky v rámci jediné koordinované operace.

Kromě logicky seskupování a správu souvisejících prostředků, správce prostředků Azure obsahuje možnosti nasazení, které umožňují přizpůsobit nasazení a konfigurace související prostředky. Například můžete pomocí Správce prostředků, nasadit a nakonfigurovat aplikaci, která se skládá z více virtuálních počítačů, nástroj pro vyrovnávání zatížení a Azure SQL database jako na jednu jednotku.

Tato nasazení můžete vyvíjet pomocí šablony Azure Resource Manager, který je dokument formátu JSON. Šablony umožňují definovat nasazení a Správa aplikací pomocí deklarativní šablony, nikoli skripty. Vaše šablony může fungovat v různých prostředích, jako je například testování, pracovní a provozní. Například můžete pomocí šablon přidání tlačítka do úložiště GitHub, která nasazuje kód v úložišti na sadu služeb Azure s jedním kliknutím.

>**Kdy použít**: šablon pomocí Resource Manageru, pokud chcete nasazení na základě šablon pro vaši aplikaci, kterou lze spravovat programově pomocí rozhraní REST API, rozhraní příkazového řádku Azure a prostředí Azure PowerShell.

>**Začínáme**: Chcete-li začít používat šablony, přečtěte si téma [šablon pro tvorbu Azure Resource Manageru](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Vysvětlení účtů, odběry a fakturace

Jako vývojáři rádi pusťte do kód a zkuste začít co nejrychleji s provedení naše aplikace spustit. Chceme určitě doporučujeme začít pracovat v Azure jako snadno. Chcete-li pomoci zajistit, aby ji snadno, Azure nabízí [bezplatnou zkušební verzi](https://azure.microsoft.com/free/). Některé služby i mít "Vyzkoušet zdarma" funkce, jako je třeba [Azure App Service](https://tryappservice.azure.com/), který nevyžaduje, můžete dokonce vytvořit účet. Jako fun jak je podrobně kódování a nasazení aplikace do Azure, je také důležité pochopit, jak funguje Azure z hlediska uživatelské účty, odběry a fakturace chvíli trvat.

### <a name="what-is-an-azure-account"></a>Co je Azure účet?

Abyste mohli vytvořit nebo pracovat s předplatné Azure, musíte mít účet Azure. Účet Azure je jednoduše identity ve službě Azure AD nebo v adresáři, jako je například pracovní nebo školní organizace, která je důvěryhodná Azure AD. Pokud jste nepatří do této organizace, můžete vždy vytvořit odběr pomocí Account Microsoft, který je důvěryhodný pro Azure AD. Další informace o integraci místního systému Windows Server Active Directory s Azure AD najdete v tématu [integrace místních identit s Azure Active Directory](../../active-directory/active-directory-aadconnect.md).

Každé předplatné služby Azure má vztah důvěryhodnosti s instancí služby Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Další informace najdete v tématu [asociování předplatných Azure se službou Azure Active Directory](../../active-directory/active-directory-how-subscriptions-associated-directory.md).

Kromě jednotlivých Azure definováním účet identity, označované taky jako *uživatelé*, můžete také definovat *skupiny* ve službě Azure AD. Vytvoření skupiny uživatelů je dobrý způsob, jak spravovat přístup k prostředkům v předplatném pomocí řízení přístupu na základě role (RBAC). Naučte se vytvářet skupiny, najdete v tématu [vytvořte skupinu ve verzi preview služby Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md). Můžete také vytvořit a spravovat skupiny s [pomocí prostředí PowerShell](../../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Správa předplatných

Předplatné je logické jednotce služeb Azure, která je propojená k účtu Azure. Každý přidružený účet má roli v předplatném. Fakturace služby Azure se provádí na základě za předplatné. Pro seznam nabídky k dispozici předplatného podle typu, najdete v části [Microsoft Azure nabízí podrobnosti](https://azure.microsoft.com/support/legal/offer-details/).

#### <a name="administrator-roles"></a>Role správce

Předplatné Azure má několik rolí správce účtu, které můžete kdykoli přiřadit.

-   **Správce účtu**: Tato role má plnou kontrolu nad předplatné a je účet, který je zodpovědný za fakturace.

-   **Správce služeb**: Tato role má kontrolu nad všechny služby v rámci předplatného. Ve výchozím nastavení se jedná o stejný účet jako správce účtu.

-   **Spolusprávcem**: Tato role má stejný přístup jako správce služeb s tím rozdílem, že ho nelze změnit přidružení předplatné na adresář služby Azure.

Další informace o rolích správce najdete v tématu [postup přidání nebo změna role Správce služby Azure](../../billing/billing-add-change-azure-subscription-administrator.md#add-an-admin-for-a-subscription).

#### <a name="resource-groups"></a>Skupiny prostředků

Při zřizování nových služeb Azure, můžete udělat v daném předplatném. Jednotlivé služby Azure, které se také nazývají prostředky, jsou vytvořeny v rámci skupiny prostředků. Skupiny prostředků usnadňují nasadit a spravovat prostředky aplikace. Skupiny prostředků by měl obsahovat všechny prostředky pro aplikaci, kterou chcete pracovat jako jednotku. Prostředky můžete přesouvat mezi skupinami prostředků a to i do různých předplatných. Další informace o přesun prostředků najdete v tématu [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../../resource-group-move-resources.md).

Průzkumníka prostředků Azure je skvělý nástroj pro vizualizaci prostředky, které jste už vytvořili v rámci vašeho předplatného. Další informace najdete v tématu [použití Průzkumníku prostředků Azure k zobrazení a úpravám prostředků](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Udělení přístupu k prostředkům

Když povolíte přístup k prostředkům Azure, vždycky je osvědčeným postupem uživatelům poskytnout nejnižší oprávnění, které je nutné provést danou úlohu.

-   **Řízení přístupu na základě role (RBAC)**: V Azure, můžete udělit přístup k uživatelské účty (objektů) v zadaném oboru: předplatné, skupinu prostředků nebo jednotlivé prostředky. RBAC umožňuje nasadit sadu prostředků do skupiny prostředků a udělení oprávnění pro konkrétního uživatele nebo skupiny. Je také umožňují omezit přístup jenom na prostředky, které patří do cílová skupina prostředků. Můžete také udělit přístup k jediný zdroj, například virtuální počítač nebo virtuální sítě. Pokud chcete udělit přístup, přiřadíte roli uživatele, skupiny nebo objektu služby. Existuje mnoho předdefinovaných rolí, a můžete také definovat vlastní role.

    >**Kdy použít**: když potřebujete vyladění správy přístupu pro uživatele a skupiny.

    >**Začínáme**: Další informace najdete v tématu [Začínáme se správou přístupu na portálu Azure](../../role-based-access-control/overview.md).

-   **Hlavní objekty služeb**: Kromě zajištění přístupu k objekty uživatele a skupiny, můžete udělit stejný přístup k objektu služby.

    > **Kdy použít**: když je prostřednictvím kódu programu správu prostředků Azure, nebo zda udělení přístupu pro aplikace. Další informace najdete v tématu [vytvoření aplikace Active Directory a objektu zabezpečení](../../resource-group-create-service-principal-portal.md).

#### <a name="tags"></a>Značky

Azure Resource Manager umožňuje přiřazení vlastních značek k jednotlivým prostředkům. Značky, které jsou páry klíč hodnota, může být užitečné, pokud budete potřebovat k uspořádání prostředků pro fakturace nebo monitorování. Značky poskytují způsob, jak sledovat prostředky v několika skupinách prostředků. Můžete přiřadit značky na portálu, a to v šablony Azure Resource Manageru nebo programově pomocí rozhraní REST API, rozhraní příkazového řádku Azure nebo PowerShell. Více značek můžete přiřadit každého prostředku. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Fakturace

V přesunu z výpočetních k hostované cloudové služby na místě sledování a odhadnout využití služby a související náklady jsou důležité aspekty. Je důležité moci odhadnout nové prostředky náklady ke spuštění měsíčně. Také musíte být schopni projektu, jak vypadá fakturaci pro daný měsíc podle aktuální výdaje.

#### <a name="get-resource-usage-data"></a>Získat data použití prostředků

Azure poskytuje sadu fakturace REST API, která umožňují přístup k spotřeby prostředků a informace metadat pro předplatná Azure. Tato rozhraní API fakturace získáte možnost lépe předpovědi a spravovat Azure náklady. Můžete sledovat a analyzují výdaje rozdělený, vytvářet výstrahy, útraty a předpovídat budoucí fakturace na základě aktuální trendů využití.

>**Začínáme**: Další informace o používání rozhraní API fakturace najdete v tématu [přehled využití fakturace Azure a rozhraní API RateCard](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Předpovídat budoucí náklady

I když je náročné odhadnout náklady dopředu, Azure má [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete použít při odhadnout náklady na nasazení prostředků. Také můžete v okně fakturace v portálu a rozhraní API REST fakturace odhad budoucích náklady na základě aktuální spotřeby.

>**Začínáme**: najdete v části [přehled využití fakturace Azure a rozhraní API RateCard](../../billing-usage-rate-card-overview.md).

#### <a name="set-up-billing-alerts"></a>Nastavení upozornění fakturace

Poté, co nasadíte vaší aplikace nebo řešení v Azure, můžete vytvořit výstrahy, které odesílání že e-mailem při přístupu útraty limity, které jsou definovány ve výstraze.

>**Začínáme**: Další informace najdete v tématu [nastavit výstrahy pro vaše předplatné Microsoft Azure billing](../../billing-set-up-alerts.md).
