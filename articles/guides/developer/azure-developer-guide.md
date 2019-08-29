---
title: Příručka Začínáme pro vývojáře v Azure | Microsoft Docs
description: V tomto tématu najdete základní informace pro vývojáře, kteří chtějí začít používat Microsoft Azure platformu pro potřeby vývoje.
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
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: f04dd628ae0999edd202c98521526ae70e1ff0af
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141222"
---
# <a name="get-started-guide-for-azure-developers"></a>Úvodní příručka pro vývojáře v Azure

## <a name="what-is-azure"></a>Co je Azure?

Azure je kompletní cloudová platforma, která může hostovat vaše stávající aplikace, zjednodušit vývoj nových aplikací a dokonce i zlepšit místní aplikace. Azure integruje cloudové služby, které potřebujete k vývoji, testování, nasazování a správě vašich aplikací, a přitom využije efektivity cloud computingu.

Hostováním aplikací v Azure můžete začít s malým a snadno škálovat aplikaci podle toho, jak vaše zákaznická poptávka roste. Azure také nabízí spolehlivost potřebnou pro aplikace s vysokou dostupností, včetně převzetí služeb při selhání mezi různými oblastmi. [Azure Portal](https://portal.azure.com) vám umožňuje snadno spravovat všechny služby Azure. Služby můžete spravovat také programově pomocí rozhraní API a šablon pro konkrétní služby.

**Kdo by se měl číst**: Tato příručka představuje úvod k platformě Azure pro vývojáře aplikací. Poskytuje pokyny a směr, abyste mohli začít sestavovat nové aplikace v Azure nebo migrovat stávající aplikace do Azure.

## <a name="where-do-i-start"></a>Kde mám začít?

Se všemi službami, které nabízí Azure, může být těžké úkolem zjistit, které služby potřebujete k podpoře vaší architektury řešení. V této části se vysvětlují služby Azure, které vývojáři často používají. Seznam všech služeb Azure najdete v [dokumentaci k Azure](../../index.md).

Nejprve se musíte rozhodnout, jak hostovat aplikaci v Azure. Potřebujete spravovat celou infrastrukturu jako virtuální počítač (VM). Můžete používat zařízení pro správu platformy, která poskytuje Azure? Je možné, že budete potřebovat server bez serveru k hostování pouze pro provádění kódu?

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

App Service byla navržena s ohledem na DevOps. Podporuje různé nástroje pro publikování a nepřetržitou integraci nasazení, včetně webhooků GitHubu, Jenkinse, Azure DevOps, TeamCity a dalších.

Stávající aplikace můžete migrovat na App Service pomocí [Nástroje pro online migraci](https://www.migratetoazure.net/).

> **Kdy použít**: Pokud migrujete stávající webové aplikace do Azure a potřebujete plně spravovanou platformu hostování pro vaše webové aplikace, použijte App Service. App Service můžete použít také v případě, že potřebujete podporovat mobilní klienty nebo zveřejnit rozhraní REST API s vaší aplikací.
> 
> **Začínáme**: App Service usnadňuje vytvoření a nasazení první [webové aplikace](../../app-service/app-service-web-get-started-dotnet.md), [mobilní aplikace](../../app-service-mobile/app-service-mobile-ios-get-started.md)nebo [aplikace API](../../app-service/app-service-web-tutorial-rest-api.md).
> 
> **Vyzkoušejte hned teď**: App Service vám umožní zřídit krátkodobou aplikaci pro vyzkoušení platformy bez nutnosti registrace účtu Azure. Vyzkoušejte si platformu a [vytvořte aplikaci Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure vám jako poskytovatel infrastruktury jako služby (IaaS) umožňuje nasazení nebo migraci vaší aplikace do virtuálních počítačů se systémem Windows nebo Linux. V kombinaci s Azure Virtual Network podporuje Azure Virtual Machines nasazení virtuálních počítačů s Windows nebo Linux do Azure. U virtuálních počítačů máte celkovou kontrolu nad konfigurací počítače. Při používání virtuálních počítačů zodpovídáte za veškerou instalaci, konfiguraci, údržbu a opravy operačního systému na serverovém softwaru.

Z důvodu úrovně kontroly, kterou máte s virtuálními počítači, můžete v Azure spustit celou řadu úloh serveru, které se nevejdou do modelu PaaS. Mezi tyto úlohy patří databázové servery, Windows Server Active Directory a Microsoft SharePoint. Další informace najdete v dokumentaci k Virtual Machines pro [Linux](/azure/virtual-machines/linux/) nebo [Windows](/azure/virtual-machines/windows/).

> **Kdy použít**: Použijte Virtual Machines, pokud chcete mít plnou kontrolu nad infrastrukturou aplikace nebo migrujete místní aplikační úlohy do Azure bez nutnosti provádět změny.
> 
> **Začínáme**: Z Azure Portal vytvořte virtuální počítač se systémem [Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) nebo [virtuální počítač s Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) .

#### <a name="azure-functions-serverless"></a>Azure Functions (bez serveru)

Nemusíte si dělat starosti se sestavou a správou celé aplikace nebo infrastruktury pro spuštění kódu. Co když byste mohli jenom napsat kód a nechat ho běžet v reakci na události nebo podle plánu?  [Azure Functions](../../azure-functions/functions-overview.md) je nabídka se stylem bez serveru, která umožňuje napsat jenom kód, který potřebujete. S funkcemi se provádění kódu spouští požadavky HTTP, Webhooky, události cloudové služby nebo podle plánu. Můžete si kód zvolit ve vývojovém jazyce, jako je například C\#, F\#, Node. js, Python nebo php. S fakturací na základě spotřeby platíte jenom za čas, kdy se váš kód spouští, a Azure podle potřeby škáluje.

> **Kdy použít**: Použijte Azure Functions, když máte kód aktivovaný jinými službami Azure, webovými událostmi nebo podle plánu. Funkce můžete použít také v případě, že nepotřebujete režii dokončeného hostovaného projektu nebo pokud chcete platit jenom za čas, kdy je kód spuštěný. Další informace najdete v tématu [přehled Azure Functions](../../azure-functions/functions-overview.md).
> 
> **Začínáme**: Postupujte podle kurzu rychlý Start k funkcím a [vytvořte svou první funkci](../../azure-functions/functions-create-first-azure-function.md) z portálu.
> 
> **Vyzkoušejte hned teď**: Azure Functions umožňuje spuštění kódu bez nutnosti registrace účtu Azure. Vyzkoušejte si to teď a [Vytvořte svoji první funkci Azure Functions](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric je platforma distribuovaných systémů usnadňující sestavování, balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Poskytuje také komplexní možnosti správy aplikací pro zřizování, nasazování, monitorování, upgrade/opravy a odstraňování nasazených aplikací. Aplikace, které se spouštějí ve sdíleném fondu počítačů, můžou v případě potřeby začít využívat malý objem a škálovat na stovky nebo tisíce počítačů.

Service Fabric podporuje WebAPI s otevřeným webovým rozhraním pro .NET (OWIN) a ASP.NET Core. Poskytuje sady SDK pro vytváření služeb na platformě Linux v rozhraní .NET Core i Java. Další informace o Service Fabric najdete v dokumentaci k [Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Kdy použít:** Service Fabric je vhodná volba při vytváření aplikace nebo přepisování existující aplikace, aby používala architekturu mikroslužeb. Použijte Service Fabric, když potřebujete větší kontrolu nad základní infrastrukturou nebo k ní máte přímý přístup.
> 
> **Začínáme:** [Vytvořte svou první aplikaci Service Fabric pro Azure](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Vylepšení aplikací pomocí služeb Azure

Kromě hostování aplikací poskytuje Azure nabídky služeb, které můžou zlepšit funkčnost, vývoj a údržbu vašich aplikací v cloudu i v místním prostředí.

#### <a name="hosted-storage-and-data-access"></a>Hostované úložiště a přístup k datům

Většina aplikací musí ukládat data, takže bez ohledu na to, jak se rozhodnete hostovat aplikaci v Azure, vezměte v úvahu jednu nebo více z následujících úložišť a datových služeb.

- **Azure Cosmos DB**: Globálně distribuovaná databázová služba pro více modelů, která umožňuje elasticky škálovat propustnost a úložiště napříč libovolným počtem geografických oblastí s komplexní smlouvou SLA. 
  > **Kdy použít:** Když vaše aplikace potřebuje databáze dokumentů, tabulek nebo grafů, včetně databází MongoDB, s několika jasně definovanými modely konzistence. 
  > 
  > **Začínáme**: [Sestavte Azure Cosmos DB webovou aplikaci](../../cosmos-db/create-sql-api-dotnet.md). Pokud jste vývojář MongoDB, přečtěte si téma [Vytvoření webové aplikace v MongoDB pomocí Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: Nabízí odolné a vysoce dostupné úložiště pro objekty blob, fronty, soubory a další druhy nerelačních dat. Storage poskytuje základ úložiště pro virtuální počítače.

  > **Kdy použít**: Když vaše aplikace ukládá nerelační data, jako jsou páry klíč-hodnota (tabulky), objekty blob, sdílené složky nebo zprávy (fronty).
  > 
  > **Začínáme**: Vyberte jeden z těchto typů úložiště: [objekty blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabulky](../../cosmos-db/table-storage-how-to-use-dotnet.md), [fronty](../../storage/queues/storage-dotnet-how-to-use-queues.md)nebo [soubory](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: Verze Microsoft SQL Serverho modulu založená na Azure pro ukládání relačních tabulkových dat v cloudu. SQL Database poskytuje předvídatelný výkon, škálovatelnost bez výpadků, provozní kontinuitu a ochranu dat.

  > **Kdy použít**: Pokud vaše aplikace vyžaduje úložiště dat s referenční integritou, transakční podporou a podporou pro dotazy TSQL.
  > 
  > **Začínáme**: [Vytvořte databázi SQL během několika minut pomocí Azure Portal](../../sql-database/sql-database-get-started.md).


Pomocí [Azure Data Factory](../../data-factory/introduction.md) můžete přesunout existující místní data do Azure. Pokud nejste připraveni přesunout data do cloudu, [Hybrid Connections](../../biztalk-services/integration-hybrid-connection-overview.md) v BizTalk Services umožňuje připojit vaši aplikaci v App Service hostované k místním prostředkům. Můžete se také připojit ke službě Azure data a Storage z vašich místních aplikací.

#### <a name="docker-support"></a>Podpora Docker

Kontejnery Docker, což je forma virtualizace operačního systému, umožňují nasadit aplikace efektivněji a předvídatelným způsobem. Kontejnerová aplikace funguje v produkčním prostředí stejným způsobem jako ve vývojovém a testovacím systému. Kontejnery můžete spravovat pomocí standardních nástrojů Docker. K nasazení a správě kontejnerových aplikací v Azure můžete využít své stávající dovednosti a oblíbené open source nástroje.

Azure nabízí několik způsobů použití kontejnerů ve vašich aplikacích.

- **Rozšíření Azure Docker VM**: Umožňuje nakonfigurovat virtuální počítač pomocí nástrojů Docker tak, aby fungoval jako hostitel Docker.

  > **Kdy použít**: Když chcete vygenerovat konzistentní nasazení kontejnerů pro vaše aplikace na virtuálním počítači, nebo pokud chcete použít [Docker Compose](https://docs.docker.com/compose/overview/).
  > 
  > **Začínáme**: [Vytvořte prostředí Docker v Azure pomocí rozšíření Docker VM](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Azure Container Service**: Umožňuje vytvářet, konfigurovat a spravovat cluster virtuálních počítačů, které jsou předem nakonfigurované pro spouštění kontejnerových aplikací. Další informace o službě Container Service najdete v tématu [Azure Container Service Úvod](../../container-service/container-service-intro.md).

  > **Kdy použít**: Pokud potřebujete sestavit škálovatelné prostředí připravené pro produkční prostředí, které poskytuje další nástroje pro plánování a správu, nebo když nasazujete cluster Docker Swarm.
  > 
  > **Začínáme**: [Nasazení clusteru služby Container Service](../../container-service/dcos-swarm/container-service-deployment.md).

- **Docker Machine**: Umožňuje nainstalovat a spravovat modul Docker na virtuálních hostitelích pomocí příkazů Docker-Machine.

  >**Kdy použít**: Pokud potřebujete rychle vytvořit prototyp aplikace vytvořením jednoho hostitele Docker.

- **Vlastní image Docker pro App Service**: Umožňuje použít kontejnery Docker z registru kontejneru nebo kontejneru zákazníka při nasazení webové aplikace v systému Linux.

  > **Kdy použít**: Při nasazení webové aplikace v systému Linux do bitové kopie Docker.
  > 
  > **Začínáme**: [Použijte vlastní image Docker pro App Service v systému Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Ověřování

Je velmi důležité nejen zjistit, kdo používá vaše aplikace, ale také k tomu, aby se zabránilo neoprávněnému přístupu k prostředkům. Azure nabízí několik způsobů, jak ověřit klienty aplikace.

- **Azure Active Directory (Azure AD)** : Cloudová služba pro správu identit a přístupu, která je založená na víceklientské architektuře Microsoftu. Integraci s Azure AD můžete přidat k vašim aplikacím pomocí jednotného přihlašování (SSO). K vlastnostem adresáře můžete přistupovat pomocí Graph API Azure AD přímo nebo rozhraní Microsoft Graph API. Můžete integrovat s podporou Azure AD pro autorizační rozhraní OAuth 2.0 a otevřít ID připojit pomocí nativních koncových bodů HTTP/REST a multiplatformní knihoven ověřování Azure AD.

  > **Kdy použít**: Pokud chcete poskytovat prostředí jednotného přihlašování, pracujte s daty založenými na grafu nebo ověřovat uživatele založené na doméně.
  > 
  > **Začínáme**: Další informace najdete v příručce pro [vývojáře Azure Active Directory](../../active-directory/develop/v1-overview.md).

- **Ověřování App Service**: Když zvolíte App Service pro hostování aplikace, získáte také integrovanou podporu ověřování pro Azure AD společně se zprostředkovateli sociálních identit, včetně Facebooku, Google, Microsoft a Twitteru.

  > **Kdy použít**: Pokud chcete v aplikaci App Service povolit ověřování pomocí služby Azure AD, poskytovatelů sociálních identit nebo obou.
  > 
  > **Začínáme**: Další informace o ověřování v App Service najdete v tématu [ověřování a autorizace v Azure App Service](../../app-service/overview-authentication-authorization.md).

Další informace o osvědčených postupech zabezpečení v Azure najdete v tématu [osvědčené postupy a vzory zabezpečení Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorování

Když vaše aplikace běží v Azure, musíte být schopni monitorovat výkon, sledovat problémy a zjistit, jak zákazníci používají vaši aplikaci. Azure nabízí několik možností monitorování.

-   **Application Insights sady Visual Studio**: Rozšiřitelná analytická služba Azure, která se integruje se sadou Visual Studio a monitoruje vaše živé webové aplikace. Poskytuje vám data, která potřebujete k průběžnému zlepšování výkonu a použitelnosti vašich aplikací, ať už jsou hostované v Azure, nebo ne.

    >**Začínáme**: Postupujte podle [kurzu Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: Služba, která pomáhá vizualizovat metriky a protokoly, které vygenerovala vaše infrastruktura a prostředky Azure, a reagovat na ně. Monitor poskytuje zobrazení dat zobrazená v Azure Portal a je jedním zdrojem pro monitorování prostředků Azure.
 
    >**Začínáme**: [Začínáme s Azure monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integrace DevOps

Bez ohledu na to, jestli je zřizování virtuálních počítačů nebo publikování vašich webových aplikací pomocí průběžné integrace, se Azure integruje s většinou oblíbených DevOps nástrojů. Díky podpoře nástrojů jako Jenkinse, GitHub, Puppet, GitHub, TeamCity, Ansible, Azure DevOps a dalších můžete pracovat s nástroji, které už máte, a maximalizovat stávající prostředí.

> **Vyzkoušejte hned teď:** [Vyzkoušejte některé z DevOps integrací](https://azure.microsoft.com/try/devops/).
> 
> **Začínáme**: Pokud chcete zobrazit možnosti DevOps pro aplikaci App Service, přečtěte si téma [průběžné nasazování do Azure App Service](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Oblasti Azure

Azure je globální cloudová platforma, která je obecně dostupná v mnoha oblastech po celém světě. Při zřizování služby, aplikace nebo virtuálního počítače v Azure budete požádáni o výběr oblasti, která představuje konkrétní datové centrum, ve kterém je vaše aplikace spuštěná, nebo kde jsou uložená vaše data. Tyto oblasti odpovídají konkrétním umístěním, která jsou publikována na stránce [oblastí Azure](https://azure.microsoft.com/regions/) .

### <a name="choose-the-best-region-for-your-application-and-data"></a>Volba nejlepší oblasti pro vaši aplikaci a data

Jednou z výhod používání Azure je to, že můžete své aplikace nasadit do různých Datacenter po celém světě. Oblast, kterou zvolíte, může ovlivnit výkon aplikace. Například je lepší vybrat oblast, která je nejblíže většině zákazníků, aby se snížila latence v síťových požadavcích. Možná budete chtít vybrat oblast, která bude vyhovovat zákonným požadavkům pro distribuci aplikace v určitých zemích nebo oblastech. Je vždycky vhodné ukládat data aplikací ve stejném datovém centru nebo v datovém centru co nejblíže k datovému centru, který hostuje vaši aplikaci.

### <a name="multi-region-apps"></a>Aplikace pro více oblastí

I když není pravděpodobné, nemůžete pro celé datacentrum přejít do režimu offline kvůli události, jako je přírodní havárie nebo selhání Internetu. Osvědčeným postupem je hostování důležitých podnikových aplikací ve více než jednom datovém centru za účelem zajištění maximální dostupnosti. Použití více oblastí může také snížit latenci u globálních uživatelů a poskytovat další možnosti pro flexibilitu při aktualizaci aplikací.

Některé služby, jako je třeba virtuální počítač a App Services, využívají [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) k zajištění podpory více oblastí s převzetím služeb při selhání mezi oblastmi, aby podporovaly podnikové aplikace s vysokou dostupností. Příklad najdete v tématu [referenční architektura Azure: Spuštění webové aplikace v několika oblastech](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Kdy použít**: Pokud máte aplikace s podnikovou a vysokou dostupností, které využívají převzetí služeb při selhání a replikaci.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Návody Spravovat moje aplikace a projekty?

Azure poskytuje bohatou sadu funkcí pro vytváření a správu prostředků, aplikací a projektů Azure, a to jak programově, tak i v [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Rozhraní příkazového řádku a prostředí PowerShell

Azure poskytuje dva způsoby, jak spravovat vaše aplikace a služby z příkazového řádku pomocí bash, terminálu, příkazového řádku nebo vašeho nástroje příkazového řádku podle vlastního výběru. Obvykle můžete provádět stejné úlohy z příkazového řádku jako v Azure Portal, jako je například vytváření a konfigurace virtuálních počítačů, virtuálních sítí, webových aplikací a dalších služeb.

-   [Rozhraní příkazového řádku Azure (CLI)](../../xplat-cli-install.md): Umožňuje připojit se k předplatnému Azure a programovat různé úlohy proti prostředkům Azure z příkazového řádku.

-   [Azure PowerShell](../../powershell-install-configure.md): Poskytuje sadu modulů s rutinami, které vám umožní spravovat prostředky Azure pomocí Windows PowerShellu.

### <a name="azure-portal"></a>portál Azure

Azure Portal je webová aplikace, kterou můžete použít k vytváření, správě a odebírání prostředků a služeb Azure. Azure Portal se nachází na adrese <https://portal.azure.com>. Zahrnuje přizpůsobitelný řídicí panel, nástroje pro správu prostředků Azure a přístup k nastavením a fakturačním údajům předplatného. Další informace najdete v [přehledu Azure Portal](../../azure-portal-overview.md).

### <a name="rest-apis"></a>Rozhraní REST API

Azure je založený na sadě rozhraní REST API, které podporují uživatelské rozhraní Azure Portal. Většina těchto rozhraní REST API se taky podporuje, aby vám umožnila programové zřizování a správu prostředků a aplikací Azure z libovolného zařízení s podporou Internetu. Úplnou sadu REST API dokumentaci najdete v tématu [referenční informace k sadě Azure REST SDK](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>API

Kromě rozhraní REST API umožňují řada služeb Azure také programově spravovat prostředky z vašich aplikací pomocí sad Azure SDK specifických pro konkrétní platformy, včetně sad SDK pro následující vývojové platformy:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

Služby jako [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) a [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) poskytují sady SDK na straně klienta, které umožňují přístup ke službám z webových a mobilních klientských aplikací.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Spuštění vaší aplikace v Azure pravděpodobně zahrnuje práci s více službami Azure, a to vše se řídí stejným životním cyklem a je možné si je představit jako logická jednotka. Webová aplikace může například používat Web Apps, SQL Database, úložiště, Azure cache pro Redis a služby Azure Content Delivery Network. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) vám umožní pracovat s prostředky v aplikaci jako se skupinou. Všechny prostředky můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace.

Kromě logického seskupování a správy souvisejících prostředků Azure Resource Manager zahrnuje možnosti nasazení, které umožňují přizpůsobit nasazení a konfiguraci souvisejících prostředků. Například pomocí Správce prostředků můžete nasadit a nakonfigurovat aplikaci, která se skládá z několika virtuálních počítačů, nástroje pro vyrovnávání zatížení a databáze SQL Azure jako jedné jednotky.

Tato nasazení vyvíjíte pomocí šablony Azure Resource Manager, což je dokument ve formátu JSON. Šablony umožňují definovat nasazení a spravovat aplikace pomocí deklarativních šablon namísto skriptů. Šablony mohou fungovat v různých prostředích, jako jsou testování, příprava a produkce. Například pomocí šablon můžete přidat tlačítko do úložiště GitHub, které nasadí kód v úložišti do sady služeb Azure jediným kliknutím.

> **Kdy použít**: Šablony Správce prostředků použijte v případě, že chcete nasadit šablonu na základě šablon pro aplikaci, kterou můžete spravovat programově pomocí rozhraní REST API, rozhraní příkazového řádku Azure a Azure PowerShell.
> 
> **Začínáme**: Chcete-li začít používat šablony, přečtěte si téma [vytváření Azure Resource Manager šablon](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Principy účtů, předplatných a fakturace

Jako vývojáři chceme podrobně přímo do kódu a při provádění našich aplikací je možné začít co nejrychleji. Určitě chceme začít pracovat v Azure co nejrychleji. Díky tomu Azure nabízí [bezplatnou zkušební verzi](https://azure.microsoft.com/free/). Některé služby obsahují i funkci vyzkoušet si bezplatnou službu, například [Azure App Service](https://tryappservice.azure.com/), což nevyžaduje ani vytvoření účtu. Stejně jako při podrobněi kódování a nasazování vaší aplikace do Azure je také důležité porozumět tomu, jak Azure funguje z hlediska uživatelských účtů, předplatných a fakturace.

### <a name="what-is-an-azure-account"></a>Co je to účet Azure?

Aby bylo možné vytvořit nebo pracovat s předplatným Azure, musíte mít účet Azure. Účet Azure je jednoduše identita ve službě Azure AD nebo v adresáři, jako je například pracovní nebo školní organizace, která je důvěryhodná pro Azure AD. Pokud k takové organizaci nepatříte, můžete předplatné kdykoli vytvořit pomocí účtu Microsoft, který je důvěryhodný pro Azure AD. Další informace o integraci místní služby Windows Server Active Directory se službou Azure AD najdete v tématu [Integrace místních identit s Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Každé předplatné služby Azure má vztah důvěryhodnosti s instancí služby Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Další informace najdete v tématu [jak jsou předplatná Azure přidružená k Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Kromě definování individuálních identit účtů Azure, označovaných také jako *Uživatelé*, můžete také definovat *skupiny* v Azure AD. Vytváření skupin uživatelů je dobrým způsobem, jak spravovat přístup k prostředkům v rámci předplatného pomocí řízení přístupu na základě role (RBAC). Další informace o vytváření skupin najdete v tématu [Vytvoření skupiny v Azure Active Directory Preview](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Skupiny můžete také vytvářet a spravovat [pomocí prostředí PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Spravovat předplatná

Předplatné je logické seskupení služeb Azure, které jsou propojené s účtem Azure. Jeden účet Azure může obsahovat několik předplatných. Fakturace služeb Azure se provádí na základě jednotlivých předplatných. Seznam dostupných nabídek pro předplatné podle typu najdete v tématu [informace o nabídce Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Předplatná Azure mají správce účtu, který má plnou kontrolu nad předplatným, a správce služeb, který má kontrolu nad všemi službami v rámci předplatného. Informace o klasických správcích předplatných najdete v tématu [Přidání nebo změna správců předplatného Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Kromě správců může být jednotlivým účtům přiděleno podrobné řízení prostředků Azure pomocí [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Skupiny prostředků

Když zřizujete nové služby Azure, provedete to v daném předplatném. Jednotlivé služby Azure, které se také nazývají prostředky, se vytvářejí v kontextu skupiny prostředků. Skupiny prostředků usnadňují nasazení a správu prostředků vaší aplikace. Skupina prostředků by měla obsahovat všechny prostředky pro vaši aplikaci, se kterými chcete pracovat jako s jednotkou. Prostředky můžete přesouvat mezi skupinami prostředků a dokonce i s různými předplatnými. Další informace o přesouvání prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo](../../resource-group-move-resources.md)předplatného.

Azure Resource Explorer je skvělý nástroj pro vizualizaci prostředků, které jste už ve svém předplatném vytvořili. Další informace najdete v tématu [použití Azure Resource Explorer k zobrazení a úpravě prostředků](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Udělení přístupu k prostředkům

Když povolíte přístup k prostředkům Azure, doporučuje se uživatelům poskytnout nejnižší oprávnění, která jsou potřebná k provedení dané úlohy.

- **Řízení přístupu na základě role (RBAC)** : V Azure můžete udělit přístup k uživatelským účtům (UPN) v zadaném oboru: předplatné, skupina prostředků nebo jednotlivé prostředky. RBAC umožňuje nasadit sadu prostředků do skupiny prostředků a udělit oprávnění pro konkrétního uživatele nebo skupinu. Umožňuje taky omezit přístup jenom na prostředky, které patří do cílové skupiny prostředků. Můžete taky udělit přístup k jednomu prostředku, jako je třeba virtuální počítač nebo virtuální síť. Chcete-li udělit přístup, přiřaďte roli uživateli, skupině nebo objektu služby. Existuje mnoho předdefinovaných rolí a můžete také definovat vlastní role. Další informace najdete v tématu [co je řízení přístupu na základě role (RBAC)?](../../role-based-access-control/overview.md).

  > **Kdy použít**: Pokud potřebujete jemně odstupňovanou správu přístupu pro uživatele a skupiny, nebo když potřebujete nastavit uživatele jako vlastníka předplatného.
  > 
  > **Začínáme**: Další informace najdete v tématu [Správa přístupu pomocí RBAC a Azure Portal](../../role-based-access-control/role-assignments-portal.md).

- **Instanční objekty služby**: Kromě poskytnutí přístupu k objektům zabezpečení a skupinám uživatelů můžete udělit stejný přístup k instančnímu objektu.

  > **Kdy použít**: Když spravujete prostředky Azure nebo udělíte přístup k aplikacím, budete programově. Další informace najdete v tématu [Vytvoření aplikace služby Active Directory a instančního objektu](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Tags

Azure Resource Manager umožňuje přiřadit k jednotlivým prostředkům vlastní značky. Značky, které jsou páry klíč-hodnota, mohou být užitečné v případě, že potřebujete organizovat prostředky pro účely fakturace nebo monitorování. Značky poskytují způsob, jak sledovat prostředky v několika skupinách prostředků. Značky můžete přiřadit na portálu, v Azure Resource Manager šabloně nebo programově pomocí REST API, rozhraní příkazového řádku Azure nebo PowerShellu. Každému prostředku můžete přiřadit více značek. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Fakturace

V rámci přesunu z místního prostředí do služeb hostovaných v cloudu jsou sledování a odhadace využití služeb a související náklady významnými aspekty. Je důležité, abyste mohli odhadnout, jaké nové náklady na jednotlivé prostředky budou běžet měsíčně. Musíte být také schopni projektovat, jak fakturace vyhledává určitý měsíc na základě aktuální útraty.

#### <a name="get-resource-usage-data"></a>Získat data o využití prostředků

Azure poskytuje sadu rozhraní REST API pro fakturaci, která poskytují přístup k informacím o spotřebě prostředků a metadatům pro předplatná Azure. Tyto rozhraní API pro fakturaci poskytují možnost lépe předpovídat a spravovat náklady na Azure. Můžete sledovat a analyzovat útraty v hodinových přírůstcích, vytvářet upozornění na útraty a předpovídat budoucí fakturaci na základě aktuálních trendů využití.

>**Začínáme**: Další informace o používání rozhraní API pro fakturaci najdete v tématu [Přehled využití fakturace Azure a rozhraní RateCard API](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Předpověď budoucích nákladů

I když je obtížné odhadnout náklady předem, Azure má cenovou kalkulačku [](https://azure.microsoft.com/pricing/calculator/) , kterou můžete použít při odhadu nákladů na nasazené prostředky. Můžete také použít okno fakturace na portálu a fakturační rozhraní REST API k odhadu budoucích nákladů na základě aktuální spotřeby.

>**Začínáme**: Viz [Přehled využití fakturace Azure a rozhraní RateCard API](../../billing-usage-rate-card-overview.md).
