---
title: Příručka Začínáme pro vývojáře v Azure | Microsoft Docs
description: Tento článek poskytuje základní informace pro vývojáře, kteří chtějí začít používat Microsoft Azure platformu pro potřeby vývoje.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: 8694c403b14234a70b0a67f9f4defb7817ba3ae3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005380"
---
# <a name="get-started-guide-for-azure-developers"></a>Úvodní příručka pro vývojáře v Azure

## <a name="what-is-azure"></a>Co je Azure?

Azure je kompletní cloudová platforma, která může hostovat vaše stávající aplikace a zjednodušit vývoj nových aplikací. Azure může dokonce zlepšit i místní aplikace. Azure integruje cloudové služby, které potřebujete k vývoji, testování, nasazování a správě vašich aplikací, a to vše a přitom využívá efektivitu cloud computingu.

Hostováním aplikací v Azure můžete začít s malým a snadno škálovat aplikaci podle toho, jak vaše zákaznická poptávka roste. Azure také nabízí spolehlivost potřebnou pro aplikace s vysokou dostupností, včetně převzetí služeb při selhání mezi různými oblastmi. [Azure Portal](https://portal.azure.com) vám umožňuje snadno spravovat všechny služby Azure. Služby můžete spravovat také programově pomocí rozhraní API a šablon pro konkrétní služby.

Tato příručka představuje úvod k platformě Azure pro vývojáře aplikací. Poskytuje pokyny a směr, abyste mohli začít sestavovat nové aplikace v Azure nebo migrovat stávající aplikace do Azure.

## <a name="where-do-i-start"></a>Kde mám začít?

Se všemi službami, které nabízí Azure, může být zastrašování úkolem zjistit, které služby potřebujete k podpoře vaší architektury řešení. V této části se vysvětlují služby Azure, které vývojáři často používají. Seznam všech služeb Azure najdete v [dokumentaci k Azure](../../index.yml).

Nejprve se musíte rozhodnout, jak hostovat aplikaci v Azure. Potřebujete spravovat celou infrastrukturu jako virtuální počítač (VM)? Můžete používat zařízení pro správu platformy, která poskytuje Azure? Je možné, že budete potřebovat server bez serveru k hostování pouze pro provádění kódu?

Vaše aplikace potřebuje cloudové úložiště, které Azure nabízí několik možností pro. Můžete využít výhod podnikového ověřování Azure. K dispozici jsou také nástroje pro cloudové vývoj a monitorování a většina hostitelských služeb nabízí integraci DevOps.

Teď se podíváme na některé z konkrétních služeb, které doporučujeme prozkoumat pro vaše aplikace.

### <a name="application-hosting"></a>Hostování aplikací

Azure poskytuje několik cloudových výpočetních nabídek pro spuštění vaší aplikace, takže se nemusíte starat o podrobnosti o infrastruktuře. Můžete snadno škálovat nebo škálovat prostředky při zvětšování využití aplikací.

Azure nabízí služby, které podporují vývoj a potřeby hostování vaší aplikace. Azure poskytuje infrastrukturu jako službu (IaaS), která vám poskytne plnou kontrolu nad hostováním vaší aplikace. Nabídky platformy Azure jako služby (PaaS) poskytují plně spravované služby, které jsou potřeba pro výkon svých aplikací. V Azure je dokonce i skutečné hostování bez serveru, kde stačí napsat svůj kód.

![Možnosti hostování aplikací Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service

Pokud chcete nejrychlejší cestu k publikování webových projektů, zvažte Azure App Service. App Service usnadňuje rozšíření webových aplikací tak, aby podporovaly mobilní klienty a mohla publikovat snadno využívaná rozhraní REST API. Tato platforma poskytuje ověřování pomocí poskytovatelů sociálních sítí, automatického škálování na základě provozu, testování v produkčním prostředí a průběžných a kontejnerových nasazení.

Můžete vytvářet webové aplikace, back-endy mobilních aplikací a aplikace API.

Vzhledem k tomu, že všechny tři typy aplikací sdílejí modul runtime App Service, můžete hostovat web, podporovat mobilní klienty a zveřejnit rozhraní API v Azure, a to vše ze stejného projektu nebo řešení. Další informace o App Service najdete v tématu [co je Azure Web Apps](../../app-service/overview.md).

App Service byla navržena s ohledem na DevOps. Podporuje různé nástroje pro publikování a nepřetržitou integraci nasazení. Mezi tyto nástroje patří Webhooky GitHubu, Jenkinse, Azure DevOps, TeamCity a další.

Stávající aplikace můžete migrovat na App Service pomocí [Nástroje pro online migraci](https://appmigration.microsoft.com/).

> **Kdy použít**: Použijte App Service, když migrujete existující webové aplikace do Azure a potřebujete plně spravovanou hostující platformu pro vaše webové aplikace. App Service můžete použít také v případě, že potřebujete podporovat mobilní klienty nebo zveřejnit rozhraní REST API s vaší aplikací.
>
> **Začínáme**: App Service usnadňuje vytvoření a nasazení první [webové aplikace](../../app-service/quickstart-dotnetcore.md), [mobilní aplikace](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started)nebo [aplikace API](../../app-service/app-service-web-tutorial-rest-api.md).
>
> **Vyzkoušejte** si to: App Service umožňuje zřídit krátkodobou aplikaci pro vyzkoušení platformy bez nutnosti registrace účtu Azure. Vyzkoušejte si platformu a [vytvořte aplikaci Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure vám jako poskytovatel infrastruktury jako služby (IaaS) umožňuje nasazení nebo migraci vaší aplikace do virtuálních počítačů se systémem Windows nebo Linux. V kombinaci s Azure Virtual Network podporuje Azure Virtual Machines nasazení virtuálních počítačů s Windows nebo Linux do Azure. U virtuálních počítačů máte celkovou kontrolu nad konfigurací počítače. Při používání virtuálních počítačů zodpovídáte za veškerou instalaci, konfiguraci, údržbu a opravy operačního systému na serverovém softwaru.

Z důvodu úrovně kontroly, kterou máte s virtuálními počítači, můžete v Azure spustit celou řadu úloh serveru, které se nevejdou do modelu PaaS. Mezi tyto úlohy patří databázové servery, Windows Server Active Directory a Microsoft SharePoint. Další informace najdete v dokumentaci k Virtual Machines pro [Linux](../../virtual-machines/linux/index.yml) nebo [Windows](../../virtual-machines/windows/index.yml).

> **Kdy použít**: použijte Virtual Machines, pokud chcete mít plnou kontrolu nad infrastrukturou aplikace nebo migrujete místní aplikační úlohy do Azure bez nutnosti provádět změny.
>
> **Začínáme**: Vytvořte [virtuální počítač](../../virtual-machines/linux/quick-create-portal.md) se systémem Linux nebo [virtuální počítač s Windows](../../virtual-machines/windows/quick-create-portal.md) z Azure Portal.

#### <a name="azure-functions-serverless"></a>Azure Functions (bez serveru)

Místo toho, abyste se museli starat o sestavování a správu celé aplikace nebo infrastruktury pro spuštění kódu, co kdybyste mohli napsat kód a spustit ho jako reakci na události nebo podle plánu?  [Azure Functions](../../azure-functions/functions-overview.md) je nabídka se stylem bez serveru, která umožňuje napsat jenom kód, který potřebujete. Pomocí funkcí můžete aktivovat provádění kódu s požadavky HTTP, Webhooky, událostmi cloudové služby nebo podle plánu. Můžete si kód zvolit ve vývojovém jazyce, jako je například C \# , F \# , Node.js, Python nebo php. S fakturací na základě spotřeby platíte jenom za čas, kdy se váš kód spouští, a Azure podle potřeby škáluje.

> **Kdy použít**: použijte Azure Functions, když máte kód aktivovaný jinými službami Azure, webovými událostmi nebo podle plánu. Funkce můžete použít také v případě, že nepotřebujete režii dokončeného hostovaného projektu nebo pokud chcete platit jenom za čas, kdy je kód spuštěný. Další informace najdete v tématu [přehled Azure Functions](../../azure-functions/functions-overview.md).
>
> **Začínáme**: postupujte podle kurzu rychlý Start pro funkce a [Vytvořte svoji první funkci](../../azure-functions/functions-create-first-azure-function.md) z portálu.
>
> **Vyzkoušejte teď**: Azure Functions umožňuje spustit kód bez nutnosti registrace účtu Azure. Vyzkoušejte si to teď a [Vytvořte svoji první funkci Azure Functions](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric je platforma distribuovaných systémů. Tato platforma usnadňuje sestavování, balení, nasazování a správu škálovatelných a spolehlivých mikroslužeb. Poskytuje také komplexní možnosti správy aplikací, jako například:

* Zřizování
* Nasazení
* Monitorování
* Upgrade/opravy
* odstraňování

Aplikace, které se spouštějí ve sdíleném fondu počítačů, můžou v případě potřeby začít využívat malý objem a škálovat na stovky nebo tisíce počítačů.

Service Fabric podporuje WebAPI s otevřeným webovým rozhraním pro .NET (OWIN) a ASP.NET Core. Poskytuje sady SDK pro vytváření služeb na platformě Linux v rozhraní .NET Core i Java. Další informace o Service Fabric najdete v dokumentaci k [Service Fabric](../../service-fabric/index.yml).

> **Kdy použít:** Service Fabric je vhodná volba při vytváření aplikace nebo přepisování existující aplikace, aby používala architekturu mikroslužeb. Použijte Service Fabric, když potřebujete větší kontrolu nad základní infrastrukturou nebo k ní máte přímý přístup.
>
> **Začínáme:** [vytvoření první aplikace Service Fabric v Azure](../../service-fabric/service-fabric-tutorial-create-dotnet-app.md).

### <a name="enhance-your-applications-with-azure-services"></a>Vylepšení aplikací pomocí služeb Azure

Společně s hostováním aplikací poskytuje Azure nabídky služeb, které mohou zlepšit funkčnost. Azure může také zlepšit vývoj a údržbu vašich aplikací v cloudu i v místním prostředí.

#### <a name="hosted-storage-and-data-access"></a>Hostované úložiště a přístup k datům

Většina aplikací musí ukládat data, takže se ale rozhodnete hostovat svou aplikaci v Azure, vezměte v úvahu jednu nebo více z následujících úložišť a datových služeb.

- **Azure Cosmos DB**: globálně distribuovaná databázová služba pro více modelů. Tato databáze vám umožní elasticky škálovat propustnost a úložiště napříč libovolným počtem geografických oblastí s komplexní smlouvou SLA.

  > **Kdy použít:** Když vaše aplikace potřebuje databáze dokumentů, tabulek nebo grafů, včetně databází MongoDB, s několika jasně definovanými modely konzistence.
  >
  > **Začínáme**: [Vytvoření webové aplikace v Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Pokud jste vývojář MongoDB, přečtěte si téma [Vytvoření webové aplikace v MongoDB pomocí Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: nabízí trvalé, vysoce dostupné úložiště pro objekty blob, fronty, soubory a další druhy nerelačních dat. Storage poskytuje základ úložiště pro virtuální počítače.

  > **Kdy použít**: když vaše aplikace ukládá nerelační data, například páry klíč-hodnota (tabulky), objekty blob, sdílené soubory nebo zprávy (fronty).
  >
  > **Začínáme**: vyberte jeden z těchto typů úložiště: [objekty blob](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [tabulky](../../cosmos-db/tutorial-develop-table-dotnet.md), [fronty](../../storage/queues/storage-dotnet-how-to-use-queues.md)nebo [soubory](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: verze Microsoft SQL Server modulu založená na Azure pro ukládání relačních tabulkových dat v cloudu. SQL Database poskytuje předvídatelný výkon, škálovatelnost bez výpadků, provozní kontinuitu a ochranu dat.

  > **Kdy použít**: když vaše aplikace vyžaduje úložiště dat s referenční integritou, transakční podporou a podporou pro dotazy TSQL.
  >
  > **Začínáme**: [vytvoření databáze v Azure SQL Database v řádu minut pomocí Azure Portal](../../azure-sql/database/single-database-create-quickstart.md).


Pomocí [Azure Data Factory](../../data-factory/introduction.md) můžete přesunout existující místní data do Azure. Pokud nejste připraveni přesunout data do cloudu, [Hybrid Connections](../../app-service/app-service-hybrid-connections.md) v Azure App Service umožňuje připojit vaši aplikaci v App Service hostované k místním prostředkům. Můžete se také připojit ke službě Azure data a Storage z vašich místních aplikací.

#### <a name="docker-support"></a>Podpora Docker

Kontejnery Docker, což je forma virtualizace operačního systému, umožňují nasadit aplikace efektivněji a předvídatelným způsobem. Kontejnerová aplikace funguje v produkčním prostředí stejným způsobem jako ve vývojovém a testovacím systému. Kontejnery můžete spravovat pomocí standardních nástrojů Docker. K nasazení a správě kontejnerových aplikací v Azure můžete využít své stávající dovednosti a oblíbené open source nástroje.

Azure nabízí několik způsobů použití kontejnerů ve vašich aplikacích.


- **Služba Azure Kubernetes**: umožňuje vytváření, konfiguraci a správu clusteru virtuálních počítačů, které jsou předem nakonfigurované pro spouštění kontejnerových aplikací. Další informace o službě Azure Kubernetes najdete v tématu [Úvod do služby Azure Kubernetes](../../aks/intro-kubernetes.md).

  > **Kdy použít**: když potřebujete vytvořit škálovatelná prostředí pro produkční prostředí, která poskytují další nástroje pro plánování a správu, nebo když nasazujete cluster Docker Swarm.
  >
  > **Začínáme**: [nasazení clusteru služby Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md)

- **Docker Machine**: umožňuje nainstalovat a spravovat modul Docker na virtuálních hostitelích pomocí příkazů Docker-Machine.

  >**Kdy použít**: když potřebujete rychle vytvořit prototyp aplikace vytvořením jednoho hostitele Docker.

- **Vlastní image Docker pro App Service**: umožňuje použít kontejnery Docker z registru kontejneru nebo kontejneru zákazníka při nasazení webové aplikace v systému Linux.

  > **Kdy použít**: při nasazení webové aplikace v systému Linux do bitové kopie Docker.
  >
  > **Začínáme**: [použijte vlastní image docker pro App Service v systému Linux](../../app-service/quickstart-custom-container.md?pivots=platform-linux%253fpivots%253dplatform-linux).

### <a name="authentication"></a>Authentication

Je velmi důležité nejen zjistit, kdo používá vaše aplikace, ale také k tomu, aby se zabránilo neoprávněnému přístupu k prostředkům. Azure nabízí několik způsobů, jak ověřit klienty aplikace.

- **Azure Active Directory (Azure AD)**: cloudová služba pro správu identit a přístupu od Microsoftu pro víceklientské platformy. Integraci s Azure AD můžete přidat k vašim aplikacím pomocí jednotného přihlašování (SSO). K vlastnostem adresáře můžete přistupovat pomocí Graph API Azure AD přímo nebo rozhraní Microsoft Graph API. Můžete integrovat s podporou Azure AD pro autorizační rozhraní OAuth 2.0 a otevřít ID připojit pomocí nativních koncových bodů HTTP/REST a multiplatformní knihoven ověřování Azure AD.

  > **Kdy použít**: když chcete poskytnout možnosti jednotného přihlašování, pracovat s daty založenými na grafech nebo ověřovat uživatele založené na doméně.
  >
  > **Začínáme**: Další informace najdete v [příručce pro vývojáře Azure Active Directory](../../active-directory/develop/v2-overview.md).

- **Ověřování App Service**: když zvolíte App Service k hostování vaší aplikace, získáte také integrovanou podporu ověřování pro Azure AD společně se zprostředkovateli sociálních identit, včetně Facebooku, Google, Microsoft a Twitteru.

  > **Kdy použít**: když chcete v aplikaci App Service povolit ověřování pomocí služby Azure AD, poskytovatelů sociálních identit nebo obojího.
  >
  > **Začínáme**: Další informace o ověřování v App Service najdete v tématu [ověřování a autorizace v Azure App Service](../../app-service/overview-authentication-authorization.md).

Další informace o osvědčených postupech zabezpečení v Azure najdete v tématu [osvědčené postupy a vzory zabezpečení Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorování

Když vaše aplikace běží v Azure, je potřeba monitorovat výkon, sledovat problémy a zjistit, jak zákazníci používají vaši aplikaci. Azure nabízí několik možností monitorování.

-   **Application Insights**: rozšiřitelná služba Extensible Analytics hostovaná v Azure, která se integruje se sadou Visual Studio a monitoruje vaše živé webové aplikace. Poskytuje data, která potřebujete k nepřetržitému zlepšení výkonu a použitelnosti aplikací. K tomuto vylepšení dochází bez ohledu na to, jestli hostete své aplikace v Azure.

    >**Začínáme**: postupujte podle [kurzu Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure monitor**: služba, která pomáhá vizualizovat metriky a protokoly, které vygenerujete pomocí vaší infrastruktury a prostředků Azure, a pracovat s nimi a vydávat dotazy, směrovat je, archivovat je a reagovat na ně. Monitorování je jeden zdroj pro monitorování prostředků Azure a poskytuje zobrazení dat, která vidíte v Azure Portal.

    >**Začínáme**: začněte [s Azure monitor](../../azure-monitor/overview.md).

### <a name="devops-integration"></a>Integrace DevOps

Bez ohledu na to, jestli je zřizování virtuálních počítačů nebo publikování vašich webových aplikací pomocí průběžné integrace, se Azure integruje s většinou oblíbených DevOps nástrojů. Můžete pracovat s nástroji, které už máte, a maximalizovat stávající prostředí s podporou nástrojů jako:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Začínáme**: Pokud si chcete zobrazit DevOps možnosti pro aplikaci App Service, přečtěte si téma [průběžné nasazování do Azure App Service](../../app-service/deploy-continuous-deployment.md).
>
> **Vyzkoušejte si to teď:** [Vyzkoušejte některé z DevOps integrací](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Oblast Azure

Azure je globální cloudová platforma, která je obecně dostupná v mnoha oblastech po celém světě. Při zřizování služby, aplikace nebo virtuálního počítače v Azure budete požádáni o výběr oblasti. Tato oblast představuje konkrétní datové centrum, ve kterém je vaše aplikace spuštěná, nebo kde jsou uložená vaše data. Tyto oblasti odpovídají konkrétním umístěním, která jsou publikována na stránce [oblastí Azure](https://azure.microsoft.com/regions/) .

### <a name="choose-the-best-region-for-your-application-and-data"></a>Volba nejlepší oblasti pro vaši aplikaci a data

Jednou z výhod používání Azure je to, že můžete své aplikace nasadit do různých Datacenter po celém světě. Oblast, kterou zvolíte, může ovlivnit výkon aplikace. Například je lepší vybrat oblast, která je nejblíže většině zákazníků, aby se snížila latence v síťových požadavcích. Možná budete chtít vybrat oblast, která bude vyhovovat zákonným požadavkům pro distribuci aplikace v určitých zemích nebo oblastech. Je vždycky vhodné ukládat data aplikací ve stejném datovém centru nebo v datovém centru co nejblíže k datovému centru, který hostuje vaši aplikaci.

### <a name="multi-region-apps"></a>Aplikace pro více oblastí

I když není pravděpodobné, nemůžete pro celé datacentrum přejít do režimu offline kvůli události, jako je přírodní havárie nebo selhání Internetu. Osvědčeným postupem je hostování důležitých podnikových aplikací ve více než jednom datovém centru za účelem zajištění maximální dostupnosti. Použití více oblastí může také snížit latenci u globálních uživatelů a poskytovat další možnosti pro flexibilitu při aktualizaci aplikací.

Některé služby, jako je třeba virtuální počítač a App Services, využívají [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) k zajištění podpory více oblastí s převzetím služeb při selhání mezi oblastmi, aby podporovaly podnikové aplikace s vysokou dostupností. Příklad najdete v tématu [Referenční architektura Azure: spuštění webové aplikace ve více oblastech](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Kdy použít**: když máte aplikace s podnikovou a vysokou dostupností, které využívají převzetí služeb při selhání a replikaci.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Jak můžu spravovat svoje aplikace a projekty?

Azure poskytuje bohatou sadu funkcí pro vytváření a správu prostředků, aplikací a projektů Azure, a to jak programově, tak i v [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Rozhraní příkazového řádku a prostředí PowerShell

Azure poskytuje dva způsoby, jak spravovat aplikace a služby z příkazového řádku. Můžete použít nástroje, jako je bash, terminál, příkazový řádek nebo vámi zvolený nástroj příkazového řádku. Obvykle můžete provádět stejné úlohy z příkazového řádku jako v Azure Portal, jako je například vytváření a konfigurace virtuálních počítačů, virtuálních sítí, webových aplikací a dalších služeb.

-   Rozhraní příkazového řádku [(CLI) pro azure Command-Line](/cli/azure/install-azure-cli): umožňuje připojit se k předplatnému Azure a programovat různé úlohy s prostředky Azure z příkazového řádku.

-   [Azure PowerShell](/powershell/azure/): poskytuje sadu modulů s rutinami, které vám umožní spravovat prostředky Azure pomocí Windows PowerShellu.

### <a name="azure-portal"></a>portál Azure

[Azure Portal](https://portal.azure.com) je webová aplikace. Pomocí Azure Portal můžete vytvářet, spravovat a odebírat prostředky a služby Azure. Obsahuje:

* Konfigurovatelný řídicí panel
* Nástroje pro správu prostředků Azure
* Přístup k nastavení předplatného a fakturační informace. Další informace najdete v [přehledu Azure Portal](https://azure.microsoft.com/features/azure-portal/).

### <a name="rest-apis"></a>Rozhraní REST API

Azure je založený na sadě rozhraní REST API, které podporují uživatelské rozhraní Azure Portal. Většina těchto rozhraní REST API se taky podporuje, aby vám umožnila programové zřizování a správu prostředků a aplikací Azure z libovolného zařízení s podporou Internetu. Úplnou sadu REST API dokumentaci najdete v tématu [referenční informace k sadě Azure REST SDK](/rest/api/).

### <a name="apis"></a>Rozhraní API

Spolu s rozhraními REST API vám řada služeb Azure také umožňuje programově spravovat prostředky z vašich aplikací pomocí sad Azure SDK specifických pro konkrétní platformy, včetně sad SDK pro následující vývojové platformy:

-   [.NET](/dotnet/api/)
-   [Node.js](/azure/developer/javascript/)
-   [Java](/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Přejít](/azure/go)

Služby jako [Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) a [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) poskytují sady SDK na straně klienta, které umožňují přístup ke službám z webových a mobilních klientských aplikací.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Spuštění vaší aplikace v Azure asi zahrnuje práci s více službami Azure. Tyto služby se řídí stejným životním cyklem a lze je představit jako logickou jednotku. Webová aplikace může například používat Web Apps, SQL Database, úložiště, Azure cache pro Redis a služby Azure Content Delivery Network. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) vám umožní pracovat s prostředky v aplikaci jako se skupinou. Všechny prostředky můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace.

Společně s logicky seskupením a správou souvisejících prostředků Azure Resource Manager zahrnuje možnosti nasazení, které umožňují přizpůsobit nasazení a konfiguraci souvisejících prostředků. Můžete například použít Správce prostředků nasazení a konfiguraci aplikace. Tato aplikace se může skládat z několika virtuálních počítačů, nástroje pro vyrovnávání zatížení a databáze v Azure SQL Database jako jedna jednotka.

Tato nasazení vyvíjíte pomocí šablony Azure Resource Manager, což je dokument ve formátu JSON. Šablony umožňují definovat nasazení a spravovat aplikace pomocí deklarativních šablon namísto skriptů. Šablony mohou fungovat v různých prostředích, jako jsou testování, příprava a produkce. Pomocí šablon můžete například přidat tlačítko do úložiště GitHub, které nasadí kód v úložišti do sady služeb Azure jediným kliknutím.

> **Kdy použít**: použijte šablony Správce prostředků, když chcete, aby bylo nasazení založené na šablonách pro aplikaci, které můžete spravovat programově pomocí rozhraní REST API, rozhraní příkazového řádku Azure a Azure PowerShell.
>
> **Začínáme**: Pokud chcete začít používat šablony, přečtěte si téma [vytváření Azure Resource Manager šablon](../../azure-resource-manager/templates/template-syntax.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Vysvětlení účtů, předplatných a fakturace

Jako vývojáři chceme podrobně přímo do kódu a při provádění našich aplikací je možné začít co nejrychleji. Určitě chceme začít pracovat v Azure co nejrychleji. Díky tomu Azure nabízí [bezplatnou zkušební verzi](https://azure.microsoft.com/free/). Některé služby obsahují i funkci vyzkoušet si bezplatnou službu, například [Azure App Service](https://tryappservice.azure.com/), což nevyžaduje ani vytvoření účtu. Stejně jako při podrobněi kódování a nasazování vaší aplikace do Azure je také důležité, abyste porozuměli tomu, jak Azure funguje. Konkrétně byste měli pochopit, jak funguje z hlediska uživatelských účtů, předplatných a fakturace.

### <a name="what-is-an-azure-account"></a>Co je účet Azure?

Pokud chcete vytvořit předplatné Azure nebo pracovat s ním, musíte mít účet Azure. Účet Azure je jednoduše identita ve službě Azure AD nebo v adresáři, jako je pracovní nebo školní organizace, kterou důvěřuje Azure AD. Pokud k takové organizaci nepatříte, můžete předplatné kdykoli vytvořit pomocí účtu Microsoft, který je důvěryhodný pro Azure AD. Další informace o integraci místní služby Windows Server Active Directory se službou Azure AD najdete v tématu [Integrace místních identit s Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Každé předplatné služby Azure má vztah důvěryhodnosti s instancí služby Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Další informace najdete v tématu [jak jsou předplatná Azure přidružená k Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

I definování individuálních identit účtů Azure, označovaných také jako *Uživatelé*, můžete definovat *skupiny* v Azure AD. Vytváření skupin uživatelů je dobrým způsobem, jak spravovat přístup k prostředkům v rámci předplatného pomocí řízení přístupu na základě role (RBAC). Další informace o vytváření skupin najdete v tématu [Vytvoření skupiny v Azure Active Directory Preview](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Skupiny můžete také vytvářet a spravovat [pomocí prostředí PowerShell](../../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Správa odběrů

Předplatné je logické seskupení služeb Azure, které jsou propojené s účtem Azure. Jeden účet Azure může obsahovat několik předplatných. Fakturace služeb Azure se provádí na základě jednotlivých předplatných. Seznam dostupných nabídek pro předplatné podle typu najdete v tématu [informace o nabídce Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Předplatná Azure mají správce účtu, který má plnou kontrolu nad předplatným. Mají také správce služeb, který má kontrolu nad všemi službami v rámci předplatného. Informace o klasických správcích předplatných najdete v tématu [Přidání nebo změna správců předplatného Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). Jednotlivým účtům se dá udělit detailní řízení prostředků Azure pomocí [řízení přístupu založeného na rolích Azure (Azure RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Skupiny prostředků

Když zřizujete nové služby Azure, provedete to v daném předplatném. Jednotlivé služby Azure, které se také nazývají prostředky, se vytvářejí v kontextu skupiny prostředků. Skupiny prostředků usnadňují nasazení a správu prostředků vaší aplikace. Skupina prostředků by měla obsahovat všechny prostředky pro vaši aplikaci, se kterými chcete pracovat jako s jednotkou. Prostředky můžete přesouvat mezi skupinami prostředků a dokonce i s různými předplatnými. Další informace o přesouvání prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Azure Resource Explorer je skvělý nástroj pro vizualizaci prostředků, které jste už ve svém předplatném vytvořili. Další informace najdete v tématu [použití Azure Resource Explorer k zobrazení a úpravě prostředků](/rest/api/).

#### <a name="grant-access-to-resources"></a>Udělení přístupu k prostředkům

Když povolíte přístup k prostředkům Azure, doporučuje se uživatelům poskytnout nejnižší oprávnění, která jsou potřebná k provedení dané úlohy.

- **Řízení přístupu na základě role v Azure (Azure RBAC)**: v Azure můžete udělit přístup k uživatelským účtům v zadaném oboru: předplatné, skupina prostředků nebo jednotlivé prostředky. Azure RBAC umožňuje nasazení prostředků do skupiny prostředků a udělení oprávnění určitému uživateli nebo skupině. Umožňuje taky omezit přístup jenom na prostředky, které patří do cílové skupiny prostředků. Můžete taky udělit přístup k jednomu prostředku, jako je třeba virtuální počítač nebo virtuální síť. Chcete-li udělit přístup, přiřaďte roli uživateli, skupině nebo objektu služby. Existuje mnoho předdefinovaných rolí a můžete také definovat vlastní role. Další informace najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md).

  > **Kdy použít**: když pro uživatele a skupiny potřebujete jemně odstupňovanou správu přístupu, nebo když potřebujete nastavit uživatele jako vlastníka předplatného.
  >
  > **Začínáme**: Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).

- **Instanční objekty služby**: společně s poskytnutím přístupu k objektům zabezpečení a skupinám můžete udělit stejný přístup k instančnímu objektu.

  > **Kdy použít**: když programově spravujete prostředky Azure nebo udělíte přístup k aplikacím. Další informace najdete v tématu [Vytvoření aplikace služby Active Directory a instančního objektu](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Značky

Azure Resource Manager umožňuje přiřadit k jednotlivým prostředkům vlastní značky. Značky, které jsou páry klíč-hodnota, mohou být užitečné v případě, že potřebujete organizovat prostředky pro účely fakturace nebo monitorování. Značky poskytují způsob, jak sledovat prostředky v několika skupinách prostředků. Značky můžete přiřadit následujícím způsobem:

* Na portálu
* V šabloně Azure Resource Manager
* S využitím REST API
* Použití Azure CLI
* Použití PowerShellu

Každému prostředku můžete přiřadit více značek. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../../azure-resource-manager/management/tag-resources.md).

### <a name="billing"></a>Fakturace

V rámci přesunu z místního prostředí do služeb hostovaných v cloudu jsou sledování a odhadace využití služeb a související náklady významnými aspekty. Je důležité odhadnout, jaké nové náklady na jednotlivé prostředky se mají měsíčně provádět. Můžete také promítnout, jak vyúčtování vyhledává daný měsíc na základě aktuální útraty.

#### <a name="get-resource-usage-data"></a>Získat data o využití prostředků

Azure poskytuje sadu rozhraní REST API pro fakturaci, která poskytují přístup k informacím o spotřebě prostředků a metadatům pro předplatná Azure. Tyto rozhraní API pro fakturaci poskytují možnost lépe předpovídat a spravovat náklady na Azure. Můžete sledovat a analyzovat útraty v hodinových přírůstcíchch a vytvářet upozornění na útraty. Můžete také předpovědět budoucí fakturaci na základě aktuálních trendů využití.

>**Začínáme**: Další informace o používání rozhraní API pro fakturaci najdete v tématu [Přehled využití fakturace Azure a rozhraní RateCard API](../../cost-management-billing/manage/usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Předpověď budoucích nákladů

I když je obtížné odhadnout náklady předem, Azure nabízí nástroje, které vám můžou usnadnit. Má [cenovou kalkulačku](https://azure.microsoft.com/pricing/calculator/) , která vám pomůže odhadnout náklady na nasazené prostředky. K odhadu budoucích nákladů na základě aktuální spotřeby můžete také použít prostředky fakturace na portálu a vyúčtováním rozhraní REST API.

>**Začínáme**: Přečtěte si [Přehled využití fakturace Azure a rozhraní RateCard API](../../cost-management-billing/manage/usage-rate-card-overview.md).