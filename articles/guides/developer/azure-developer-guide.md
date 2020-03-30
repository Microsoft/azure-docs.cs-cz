---
title: Příručka Začínáme pro vývojáře v Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje základní informace pro vývojáře, kteří chtějí začít používat platformu Microsoft Azure pro své potřeby vývoje.
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
ms.openlocfilehash: d74fd2e3f6b5cc090c6313aba67a1e139c713b85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245002"
---
# <a name="get-started-guide-for-azure-developers"></a>Příručka Začínáme pro vývojáře Azure

## <a name="what-is-azure"></a>Co je Azure?

Azure je kompletní cloudová platforma, která může hostovat vaše stávající aplikace a zjednodušit vývoj nových aplikací. Azure může dokonce vylepšit místní aplikace. Azure integruje cloudové služby, které potřebujete k vývoji, testování, nasazení a správě aplikací, a to vše s využitím efektivity cloud computingu.

Hostováním aplikací v Azure můžete spustit malé a snadné škálování aplikace s rostoucí poptávkou zákazníků. Azure také nabízí spolehlivost, která je potřeba pro aplikace s vysokou dostupností, včetně převzetí služeb při selhání mezi různými oblastmi. [Portál Azure](https://portal.azure.com) vám umožní snadno spravovat všechny služby Azure. Služby můžete také spravovat programově pomocí řešení API a šablon specifických pro službu.

Tato příručka je úvodem k platformě Azure pro vývojáře aplikací. Poskytuje pokyny a směr, které potřebujete k zahájení vytváření nových aplikací v Azure nebo migraci existujících aplikací do Azure.

## <a name="where-do-i-start"></a>Kde mám začít?

Se všemi službami, které Azure nabízí, může být zastrašující úkol zjistit, které služby potřebujete pro podporu architektury řešení. Tato část upozorňuje na služby Azure, které vývojáři běžně používají. Seznam všech služeb Azure najdete v [dokumentaci k Azure](../../index.yml).

Nejprve se musíte rozhodnout, jak hostovat vaši aplikaci v Azure. Potřebujete spravovat celou infrastrukturu jako virtuální počítač. Můžete použít zařízení pro správu platformy, které Azure poskytuje? Možná budete potřebovat framework bez serveru pro hostování pouze spuštění kódu?

Vaše aplikace potřebuje cloudové úložiště, pro které Azure nabízí několik možností. Můžete využít podnikové ověřování Azure. K dispozici jsou také nástroje pro cloudový vývoj a monitorování a většina hostingových služeb nabízí integraci DevOps.

Nyní se podívejme na některé konkrétní služby, které doporučujeme prozkoumat pro vaše aplikace.

### <a name="application-hosting"></a>Hostování aplikací

Azure poskytuje několik cloudových výpočetních nabídek pro spuštění vaší aplikace, takže se nemusíte starat o podrobnosti o infrastruktuře. Můžete snadno vertikálně navýšit kapacitu nebo vertikálně navýšit kapacitu prostředků s růstem využití aplikace.

Azure nabízí služby, které podporují vaše potřeby vývoje aplikací a hostování. Azure poskytuje infrastrukturu jako službu (IaaS), která vám poskytne plnou kontrolu nad hostováním aplikací. Nabídky Platform as a Service (PaaS) azure poskytují plně spravované služby potřebné k napájení vašich aplikací. V Azure je dokonce skutečný hosting bez serveru, kde stačí napsat kód.

![Možnosti hostování aplikací Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service

Pokud chcete nejrychlejší cestu k publikování webových projektů, zvažte Azure App Service. Služba App Service usnadňuje rozšíření webových aplikací tak, aby podporovaly mobilní klienty, a publikuje snadno spotřebovávaná uživatelská zařízení REST API. Tato platforma poskytuje ověřování pomocí sociálních zprostředkovatelů, automatické škálování na základě provozu, testování v produkčním prostředí a průběžné a kontejnerové nasazení.

Můžete vytvářet webové aplikace, back-endy mobilních aplikací a aplikace API.

Vzhledem k tomu, že všechny tři typy aplikací sdílejí runtime služby App Service, můžete hostovat web, podporovat mobilní klienty a vystavit vaše api v Azure, vše ze stejného projektu nebo řešení. Další informace o službě App Service najdete v tématu [Co je Azure Web Apps](../../app-service/overview.md).

Služba App Service byla navržena s ohledem na devops. Podporuje různé nástroje pro publikování a průběžné integrace nasazení. Mezi tyto nástroje patří webhooky GitHub, Jenkins, Azure DevOps, TeamCity a další.

Stávající aplikace můžete migrovat do služby App Service pomocí [nástroje online migrace](https://appmigration.microsoft.com/).

> **Použití**: Použití služby App Service při migraci existujících webových aplikací do Azure a při použití plně spravované hostitelské platformy pro vaše webové aplikace. Službu App Service můžete použít také v případě, že potřebujete podporovat mobilní klienty nebo vystavit rest api s vaší aplikací.
>
> **Začínáme**: Služba App Service usnadňuje vytváření a nasazování první [webové aplikace](../../app-service/app-service-web-get-started-dotnet.md), [mobilní aplikace](../../app-service-mobile/app-service-mobile-ios-get-started.md)nebo aplikace [API](../../app-service/app-service-web-tutorial-rest-api.md).
>
> **Vyzkoušejte to teď**: Služba App Service vám umožní zřídit krátkodobou aplikaci, abyste vyzkoušeli platformu, aniž byste se museli zaregistrovat k účtu Azure. Vyzkoušejte platformu a [vytvořte aplikaci Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Jako poskytovatel infrastruktury jako služby (IaaS) vám Azure umožňuje nasadit nebo migrovat vaši aplikaci do virtuálních aplikací windows nebo linux. Virtuální počítače Azure spolu s Virtuální sítí Azure podporují nasazení virtuálních počítačů s Windows nebo Linuxem do Azure. S virtuálními počítači máte úplnou kontrolu nad konfigurací počítače. Při používání virtuálních počítačů jste zodpovědní za všechny opravy softwaru serveru, konfigurace, údržby a operačního systému.

Vzhledem k úrovni kontroly, kterou máte s virtuálními počítači, můžete spustit širokou škálu úloh serveru v Azure, které se nevejdou do modelu PaaS. Mezi tyto úlohy patří databázové servery, služba Windows Server Active Directory a služba Microsoft SharePoint. Další informace naleznete v dokumentaci k virtuálním počítačům pro [Linux](/azure/virtual-machines/linux/) nebo [Windows](/azure/virtual-machines/windows/).

> **Použití**: Virtuální počítače používejte, když chcete mít plnou kontrolu nad infrastrukturou aplikací nebo migrovat místní úlohy aplikací do Azure, aniž byste museli provádět změny.
>
> **Začínáme:** Vytvoření [virtuálního počítače s Linuxem](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) nebo [virtuálního počítače s Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) z webu Azure Portal.

#### <a name="azure-functions-serverless"></a>Funkce Azure (bez serveru)

Spíše než starat o budování a správu celé aplikace nebo infrastruktury pro spuštění kódu, co kdybyste mohli jen napsat kód a nechat ho spustit v reakci na události nebo podle plánu?  [Azure Functions](../../azure-functions/functions-overview.md) je nabídka ve stylu "bez serveru", která umožňuje napsat přesně ten kód, který potřebujete. Pomocí funkce můžete aktivovat spuštění kódu pomocí požadavků HTTP, webhooků, událostí cloudové služby nebo podle plánu. Můžete kód ve zvoleném jazyce vývoje, například C\#, F\#, Node.js, Python nebo PHP. S fakturací založenou na spotřebě platíte jenom za dobu, po kterou se váš kód spustí, a Azure se podle potřeby škáluje.

> **Použití**: Použití funkce Azure, pokud máte kód, který se aktivuje jinými službami Azure, webové události nebo podle plánu. Funkce můžete také použít, když nepotřebujete režii kompletního hostovaného projektu nebo pokud chcete platit pouze za čas, který je spuštěn váš kód. Další informace najdete v [tématu Přehled funkcí Azure](../../azure-functions/functions-overview.md).
>
> **Začínáme**: Postupujte podle funkce rychlý start kurz [k vytvoření první funkce](../../azure-functions/functions-create-first-azure-function.md) z portálu.
>
> **Vyzkoušejte to teď:** Funkce Azure vám umožní spustit kód, aniž byste museli zaregistrovat účet Azure. Vyzkoušejte si to nyní a [vytvořte si první funkci Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric je platforma distribuovaných systémů. Tato platforma usnadňuje vytváření, balení, nasazování a správu škálovatelných a spolehlivých mikroslužeb. Poskytuje také komplexní možnosti správy aplikací, jako jsou:

* Zřizování
* Nasazení
* Monitorování
* Inovace/opravy
* Odstranění

Aplikace, které běží na sdíleném fondu počítačů, můžete spustit malé a škálovat na stovky nebo tisíce počítačů podle potřeby.

Service Fabric podporuje webapi s otevřeným webovým rozhraním pro .NET (OWIN) a ASP.NET Core. Poskytuje sady SDK pro vytváření služeb na Linuxu v rozhraní .NET Core i v jazyce Java. Další informace o službě Fabric naleznete v [dokumentaci service fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Kdy použít:** Service Fabric je dobrou volbou při vytváření aplikace nebo přepisování existující aplikace pro použití architektury mikroslužeb. Service Fabric použijte, když potřebujete větší kontrolu nad základní infrastrukturou nebo přímý přístup k ní.
>
> **Začínáme:** [Vytvořte si první aplikaci Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Vylepšete své aplikace pomocí služeb Azure

Spolu s hostováním aplikací poskytuje Azure nabídky služeb, které můžou vylepšit funkce. Azure můžete také zlepšit vývoj a údržbu vašich aplikací, a to jak v cloudu, tak v místním prostředí.

#### <a name="hosted-storage-and-data-access"></a>Hostované úložiště a přístup k datům

Většina aplikací musí ukládat data, takže se však rozhodnete hostovat aplikaci v Azure, zvažte jednu nebo více následujících služeb úložiště a dat.

- **Azure Cosmos DB**: Globálně distribuovaná databázová služba s více modely. Tato databáze umožňuje elasticky škálovat propustnost a úložiště v libovolném počtu geografických oblastí s komplexní smlouvy SLA.

  > **Kdy použít:** Když vaše aplikace potřebuje databáze dokumentů, tabulek nebo grafů, včetně databází MongoDB, s více dobře definovanými modely konzistence.
  >
  > **Začínáme:** [Vytvoření webové aplikace Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Pokud jste vývojář MongoDB, [přečtěte si článek Vytvoření webové aplikace MongoDB s Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: Nabízí trvalé, vysoce dostupné úložiště pro objekty BLOB, fronty, soubory a další druhy nerelačních dat. Úložiště poskytuje základ úložiště pro virtuální chod.

  > **Použití**: Když aplikace ukládá nerelační data, jako jsou dvojice klíč-hodnota (tabulky), objekty BLOB, sdílené složky souborů nebo zprávy (fronty).
  >
  > **Začínáme**: Vyberte si z jednoho z těchto typů úložiště: [objekty BLOB](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabulky](../../cosmos-db/table-storage-how-to-use-dotnet.md), [fronty](../../storage/queues/storage-dotnet-how-to-use-queues.md)nebo [soubory](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: Verze modulu Microsoft SQL Server založená na Azure pro ukládání relačních tabulkových dat v cloudu. SQL Database poskytuje předvídatelný výkon, škálovatelnost bez prostojů, kontinuity provozu a ochrany dat.

  > **Použití**: Pokud vaše aplikace vyžaduje ukládání dat s referenční integritou, transakční podporou a podporou dotazů TSQL.
  >
  > **Začínáme**: [Vytvořte databázi SQL během několika minut pomocí portálu Azure](../../sql-database/sql-database-get-started.md).


[Azure Data Factory](../../data-factory/introduction.md) můžete použít k přesunutí existujících místních dat do Azure. Pokud nejste připraveni přesunout data do cloudu, [hybridní připojení](../../app-service/app-service-hybrid-connections.md) ve službě Azure App Service vám umožní připojit hostovku služby App Service k místním prostředkům. Můžete se také připojit ke službám dat a úložiště Azure z místních aplikací.

#### <a name="docker-support"></a>Podpora pro Docker

Kontejnery Dockeru, což je forma virtualizace operačního režimu, umožňují nasazovat aplikace efektivnějším a předvídatelnějším způsobem. Kontejnerizovaná aplikace pracuje v produkčním prostředí stejným způsobem jako ve vývoji a testovacích systémech. Kontejnery můžete spravovat pomocí standardních nástrojů Dockeru. Své stávající dovednosti a oblíbené open source nástroje můžete použít k nasazení a správě aplikací založených na kontejnerech v Azure.

Azure poskytuje několik způsobů, jak používat kontejnery ve vašich aplikacích.

- **Rozšíření virtuálního počítače Azure Dockeru**: Umožňuje nakonfigurovat virtuální počítač s nástroji Dockeru tak, aby působiljako hostitel Dockeru.

  > **Kdy použít**: Pokud chcete generovat konzistentní nasazení kontejnerů pro vaše aplikace na virtuálním počítači nebo když chcete použít [Docker Compose](https://docs.docker.com/compose/overview/).
  >
  > **Začínáme**: [Vytvořte prostředí Dockeru v Azure pomocí rozšíření virtuálního počítače Dockeru](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Služba Azure Kubernetes:** Umožňuje vytvářet, konfigurovat a spravovat cluster virtuálních počítačů, které jsou předem nakonfigurované pro spouštění kontejnerizovaných aplikací. Další informace o službě Azure Kubernetes service najdete v [tématu Azure Kubernetes Service introduction](../../aks/intro-kubernetes.md).

  > **Použití**: Když potřebujete vytvořit produkční, škálovatelná prostředí, která poskytují další nástroje pro plánování a správu, nebo když nasazujete cluster Docker Swarm.
  >
  > **Začínáme**: [Nasazení clusteru služeb Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Docker Machine**: Umožňuje nainstalovat a spravovat Modul Dockeru na virtuálních hostitelích pomocí příkazů docker-machine.

  >**Kdy použít**: Když potřebujete rychle vytvořit prototyp aplikace vytvořením jednoho hostitele Dockeru.

- **Image vlastního Dockeru pro službu App Service**: Umožňuje používat kontejnery Dockeru z registru kontejnerů nebo kontejneru zákazníka při nasazení webové aplikace v Linuxu.

  > **Kdy použít**: Při nasazování webové aplikace na Linux u image Dockeru.
  >
  > **Začínáme**: [Použijte vlastní image Dockeru pro službu App Service na Linuxu](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Ověřování

Je důležité nejen vědět, kdo používá vaše aplikace, ale také zabránit neoprávněnému přístupu k vašim prostředkům. Azure nabízí několik způsobů ověření klientů aplikace.

- **Azure Active Directory (Azure AD):** Microsoft víceklientské, cloudové identity a služby pro správu přístupu. Můžete přidat jednotné přihlášení (SSO) do vašich aplikací integrací s Azure AD. K vlastnostem adresáře můžete přistupovat přímo pomocí rozhraní Azure AD Graph API nebo rozhraní Microsoft Graph API. S podporou Azure AD pro autorizační architekturu OAuth2.0 a Open ID Connect můžete integrovat pomocí nativních koncových bodů HTTP/REST a víceplatformových knihoven ověřování Azure AD.

  > **Použití**: Pokud chcete poskytnout prostředí s přistaním systému zabezpečení, pracujte s daty založenými na grafu nebo ověřujte uživatele založené na doméně.
  >
  > **Začínáme:** Další informace najdete v [průvodci vývojářem služby Azure Active Directory](../../active-directory/develop/v2-overview.md).

- **Ověřování pomocí app služby:** Když zvolíte službu App Service pro hostování vaší aplikace, získáte také integrovanou podporu ověřování pro Azure AD spolu s poskytovateli sociální identity – včetně Facebooku, Googlu, Microsoftu a Twitteru.

  > **Použití**: Pokud chcete povolit ověřování v aplikaci Služby aplikace pomocí Azure AD, zprostředkovatelů sociální identity nebo obojího.
  >
  > **Začínáme**: Další informace o ověřování ve službě App Service najdete [v tématu Ověřování a autorizace ve službě Azure App Service](../../app-service/overview-authentication-authorization.md).

Další informace o doporučených postupech zabezpečení v Azure najdete v [tématu Doporučené postupy a vzory zabezpečení Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorování

Když je vaše aplikace v Azure spuštěná, musíte monitorovat výkon, sledovat problémy a zjistit, jak zákazníci vaši aplikaci používají. Azure poskytuje několik možností monitorování.

-   **Application Insights:** Rozšiřitelná analytická služba hostovaná v Azure, která se integruje s Visual Studio a monitoruje vaše živé webové aplikace. Poskytuje vám data, která potřebujete ke zlepšení výkonu a použitelnosti vašich aplikací nepřetržitě. K tomuto zlepšení dochází bez ohledu na to, zda hostujete aplikace v Azure nebo ne.

    >**Začínáme**: Postupujte podle [kurzu Přehledy aplikací](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: Služba, která vám pomůže vizualizovat, dotazovat, směrovat, archivovat a na základě metrik a protokolů, které generujete s infrastrukturou azure a prostředky. Monitorování je jediný zdroj pro monitorování prostředků Azure a poskytuje zobrazení dat, která se zobrazí na webu Azure Portal.

    >**Začínáme:** [Začínáme s Azure Monitorem](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integrace DevOps

Ať už se jedná o zřizování virtuálních počítačů nebo publikování webových aplikací s průběžnou integrací, Azure se integruje s většinou oblíbených nástrojů DevOps. Můžete pracovat s nástroji, které již máte, a maximalizovat své stávající zkušenosti s podporou nástrojů, jako jsou:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Začínáme**: Informace o možnostech DevOps pro aplikaci App Service najdete [v tématu Průběžné nasazování do služby Azure App Service](../../app-service/deploy-continuous-deployment.md).
>
> **Vyzkoušejte to teď:** [Vyzkoušejte několik integrací DevOps](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Oblast Azure

Azure je globální cloudová platforma, která je obecně dostupná v mnoha oblastech po celém světě. Když v Azure zřídíte službu, aplikaci nebo virtuální počítač, budete vyzváni k výběru oblasti. Tato oblast představuje konkrétní datové centrum, kde je aplikace spuštěna nebo kde jsou uložena data. Tyto oblasti odpovídají konkrétním umístěním, které jsou publikovány na stránce [oblasti Azure.](https://azure.microsoft.com/regions/)

### <a name="choose-the-best-region-for-your-application-and-data"></a>Výběr nejlepší oblasti pro vaši aplikaci a data

Jednou z výhod používání Azure je, že můžete nasadit aplikace do různých datových center po celém světě. Oblast, kterou zvolíte, může ovlivnit výkon aplikace. Například je lepší zvolit oblast, která je blíže k většině vašich zákazníků, aby se snížila latence v síťových požadavcích. Můžete také vybrat oblast, abyste splnili zákonné požadavky na distribuci aplikace v určitých zemích nebo oblastech. Je vždy osvědčeným postupem pro ukládání dat aplikací ve stejném datovém centru nebo v datovém centru co nejblíže k datovému centru, které je hostitelem vaší aplikace.

### <a name="multi-region-apps"></a>Aplikace s více oblastmi

I když je nepravděpodobné, není nemožné pro celé datové centrum přejít do offline z důvodu události, jako je například přírodní katastrofa nebo selhání Internetu. Je osvědčeným postupem pro hostování důležitých podnikových aplikací ve více než jednom datovém centru, abyste zajistili maximální dostupnost. Použití více oblastí může také snížit latenci pro globální uživatele a poskytnout další příležitosti pro flexibilitu při aktualizaci aplikací.

Některé služby, jako je virtual machine a App Services, používají [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) k povolení podpory ve více oblastech s převzetím služeb při selhání mezi oblastmi pro podporu podnikových aplikací s vysokou dostupností. Příklad najdete v tématu [referenční architektura Azure: Spuštění webové aplikace ve více oblastech](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Kdy použít**: Pokud máte podnikové a vysoce dostupnostové aplikace, které těží z převzetí služeb při selhání a replikace.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Jak můžu spravovat svoje aplikace a projekty?

Azure poskytuje bohatou sadu možností pro vytváření a správu prostředků, aplikací a projektů Azure – programově i na [webu Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Rozhraní příkazového řádku a Prostředí PowerShell

Azure poskytuje dva způsoby správy aplikací a služeb z příkazového řádku. Můžete použít nástroje, jako je Bash, Terminál, příkazový řádek nebo nástroj příkazového řádku. Obvykle můžete dělat stejné úkoly z příkazového řádku jako na webu Azure Portal – jako je vytváření a konfigurace virtuálních počítačů, virtuálních sítí, webových aplikací a dalších služeb.

-   [Rozhraní Příkazového řádku Azure (CLI):](../../xplat-cli-install.md)Umožňuje připojení k předplatnému Azure a naprogramovat různé úlohy proti prostředkům Azure z příkazového řádku.

-   [Azure PowerShell](../../powershell-install-configure.md): Poskytuje sadu modulů s rutinami, které umožňují spravovat prostředky Azure pomocí Windows PowerShellu.

### <a name="azure-portal"></a>portál Azure

[Portál Azure](https://portal.azure.com) je webová aplikace. Na webu Azure Portal můžete vytvářet, spravovat a odebírat prostředky a služby Azure. Obsahuje:

* Konfigurovatelný řídicí panel
* Nástroje pro správu prostředků Azure
* Přístup k nastavení předplatného a fakturačním údajům. Další informace najdete v přehledu [portálu Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>Rozhraní REST API

Azure je postavenna sadě rest API, které podporují ui portálu Azure. Většina těchto rozhraní REST API jsou také podporovány, aby vám umožní programově zřizování a správu prostředků a aplikací Azure z libovolného zařízení s podporou Internetu. Úplnou sadu dokumentace rozhraní REST API najdete v [tématu odkaz na sadu Azure REST SDK](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>Rozhraní API

Spolu s rest api, mnoho služeb Azure také umožňují programově spravovat prostředky z vašich aplikací pomocí sady Azure SDK specifické pro platformu, včetně sad SDK pro následující vývojové platformy:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Přejít](https://docs.microsoft.com/azure/go)

Služby, jako jsou [mobilní aplikace](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) a [mediální služby Azure,](../../media-services/previous/media-services-dotnet-how-to-use.md) poskytují sady SDK na straně klienta, které vám umožní přístup ke službám z webových a mobilních klientských aplikací.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Spuštění aplikace v Azure pravděpodobně zahrnuje práci s více službami Azure. Tyto služby sledují stejný životní cyklus a lze je považovat za logickou jednotku. Webová aplikace může například používat webové aplikace, databázi SQL, úložiště, mezipaměť Azure pro Redis a služby Azure Content Delivery Network. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) umožňuje pracovat s prostředky ve vaší aplikaci jako skupina. Můžete nasadit, aktualizovat nebo odstranit všechny prostředky v jedné koordinované operaci.

Spolu s logickým seskupením a správou souvisejících prostředků azure resource manager zahrnuje možnosti nasazení, které vám umožní přizpůsobit nasazení a konfiguraci souvisejících prostředků. Můžete například použít Správce prostředků nasadit a nakonfigurovat aplikaci. Tato aplikace se může skládat z více virtuálních počítačů, nástroje pro vyrovnávání zatížení a databáze Azure SQL jako jedné jednotky.

Tato nasazení můžete vyvíjet pomocí šablony Azure Resource Manager, což je dokument ve formátu JSON. Šablony umožňují definovat nasazení a spravovat aplikace pomocí deklarativních šablon, nikoli skriptů. Šablony mohou fungovat v různých prostředích, jako je testování, příprava a výroba. Šablony můžete například použít k přidání tlačítka do úložiště GitHub, které nasazuje kód v úložišti do sady služeb Azure jediným kliknutím.

> **Použití**: Použití šablon Správce prostředků, když chcete pro vaši aplikaci nasazení založené na šabloně, které můžete spravovat programově pomocí api REST, azure cli a Azure PowerShellu.
>
> **Začínáme**: Pokud chcete začít používat šablony, přečtěte si informace [o vytváření šablon Azure Resource Manageru](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Vysvětlení účtů, předplatných a fakturace

Jako vývojáři se rádi ponoříme přímo do kódu a snažíme se co nejrychleji začít s tím, aby naše aplikace běžely. Určitě vás chceme povzbudit, abyste začali pracovat v Azure co nejsnadněji. Azure nabízí [bezplatnou zkušební verzi.](https://azure.microsoft.com/free/) Některé služby mají dokonce funkci "Vyzkoušejte to zdarma", jako je [Azure App Service](https://tryappservice.azure.com/), která nevyžaduje ani vytvoření účtu. I když je zábavné ponořit se do kódování a nasazení vaší aplikace do Azure, je také důležité chvíli trvat, než pochopíte, jak Azure funguje. Konkrétně byste měli pochopit, jak to funguje z hlediska uživatelských účtů, předplatných a fakturace.

### <a name="what-is-an-azure-account"></a>Co je účet Azure?

Pokud chcete vytvořit předplatné Azure nebo s ním pracovat, musíte mít účet Azure. Účet Azure je jednoduše identita ve službě Azure AD nebo v adresáři, jako je pracovní nebo školní organizace, které Azure AD důvěřuje. Pokud do takové organizace nepatříte, můžete si vždy vytvořit předplatné pomocí účtu Microsoft, kterému Azure AD důvěřuje. Další informace o integraci místníslužby Windows Server Active Directory s Azure AD najdete v [tématu Integrace místních identit pomocí Služby Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Každé předplatné služby Azure má vztah důvěryhodnosti s instancí služby Azure AD. To znamená, že tomuto adresáři svěřuje ověřování uživatelů, služeb i zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale jedno předplatné důvěřuje pouze jednomu adresáři. Další informace najdete v tématu [Jak jsou předplatná Azure přidružená k Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Kromě definování jednotlivých identit účtu Azure, nazývaných se také *jako uživatelé*, můžete definovat *skupiny* ve službě Azure AD. Vytváření skupin uživatelů je dobrý způsob, jak spravovat přístup k prostředkům v předplatném pomocí řízení přístupu na základě rolí (RBAC). Informace o vytváření skupin najdete [v tématu Vytvoření skupiny ve verzi Azure Active Directory ve verzi Preview](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Skupiny můžete také vytvářet a spravovat [pomocí prostředí PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Správa odběrů

Předplatné je logické seskupení služeb Azure, které je propojené s účtem Azure. Jeden účet Azure může obsahovat více předplatných. Fakturace pro služby Azure se provádí na základě předplatného. Seznam dostupných nabídek předplatného podle typu najdete v [tématu Podrobnosti o nabídce Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Předplatná Azure mají správce účtu, který má plnou kontrolu nad předplatným. Mají také správce služeb, který má kontrolu nad všemi službami v předplatném. Informace o klasických správcích předplatného najdete v tématu [Přidání nebo změna správců předplatného Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). Jednotlivé účty lze udělit podrobné řízení prostředků Azure pomocí [řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Skupiny prostředků

Když zřídíte nové služby Azure, uděláte to v daném předplatném. Jednotlivé služby Azure, které se také nazývají prostředky, se vytvářejí v kontextu skupiny prostředků. Skupiny prostředků usnadňují nasazení a správu prostředků vaší aplikace. Skupina prostředků by měla obsahovat všechny prostředky pro vaši aplikaci, se kterou chcete pracovat jako jednotka. Prostředky můžete přesunout mezi skupinami prostředků a dokonce i do různých předplatných. Další informace o přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Průzkumník prostředků Azure je skvělý nástroj pro vizualizaci prostředků, které jste už vytvořili ve vašem předplatném. Další informace najdete [v tématu Použití Průzkumníka prostředků Azure k zobrazení a úpravě prostředků](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Udělení přístupu k prostředkům

Když povolíte přístup k prostředkům Azure, je vždy osvědčeným postupem poskytnout uživatelům nejmenší oprávnění, která je vyžadována k provedení daného úkolu.

- **Řízení přístupu na základě rolí (RBAC)**: V Azure můžete udělit přístup k uživatelským účtům (objekty zabezpečení) v zadaném oboru: předplatné, skupina prostředků nebo jednotlivé prostředky. RBAC umožňuje nasadit prostředky do skupiny prostředků a udělit oprávnění konkrétnímu uživateli nebo skupině. Umožňuje také omezit přístup pouze na prostředky, které patří do cílové skupiny prostředků. Můžete také udělit přístup k jednomu prostředku, jako je například virtuální počítač nebo virtuální síť. Chcete-li udělit přístup, přiřadíte roli uživateli, skupině nebo instančnímu objektu. Existuje mnoho předdefinovaných rolí a můžete také definovat vlastní role. Další informace naleznete v [tématu Co je řízení přístupu na základě rolí (RBAC)?](../../role-based-access-control/overview.md).

  > **Použití**: Pokud potřebujete jemně odstupňovanou správu přístupu pro uživatele a skupiny nebo když potřebujete, aby uživatel byl vlastníkem předplatného.
  >
  > **Začínáme**: Další informace najdete v [tématu Správa přístupu pomocí RBAC a portálu Azure](../../role-based-access-control/role-assignments-portal.md).

- **Objekty instančního objektu**: Spolu s poskytováním přístupu k uživatelským objektům a skupinám můžete udělit stejný přístup k instančnímu objektu.

  > **Použití**: Když programově spravujete prostředky Azure nebo udělujete přístup aplikacím. Další informace naleznete v [tématu Vytvoření instančního objektu a objektu zabezpečení služby Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Značky

Azure Resource Manager umožňuje přiřadit vlastní značky k jednotlivým prostředkům. Značky, které jsou dvojice klíč hodnota, může být užitečné, když potřebujete uspořádat prostředky pro fakturaci nebo monitorování. Značky poskytují způsob, jak sledovat prostředky ve více skupinách prostředků. Značky můžete přiřadit následujícími způsoby:

* Na portálu
* V šabloně Azure Resource Manager
* S využitím REST API
* Použití Azure CLI
* Pomocí prostředí PowerShell

Každému prostředku můžete přiřadit více značek. Další informace najdete v tématu [Použití značek k uspořádání prostředků Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Fakturace

Při přechodu od místního výpočetního prostředí ke službám hostovaným v cloudu je sledování a odhad využití služeb a souvisejících nákladů významným problémem. Je důležité odhadnout, jaké náklady na nové zdroje se budou spouštět měsíčně. Můžete také promítnout, jak fakturace vypadá za daný měsíc, na základě aktuálních výdajů.

#### <a name="get-resource-usage-data"></a>Získání dat o využití prostředků

Azure poskytuje sadu rozhraní API pro fakturaci REST, která poskytují přístup k využití prostředků a informacím o metadatech pro předplatná Azure. Tato fakturační api vám dávají možnost lépe předvídat a spravovat náklady Azure. Můžete sledovat a analyzovat výdaje v hodinových přírůstcích a vytvářet upozornění na výdaje. Můžete také předpovědět budoucí fakturaci na základě aktuálních trendů využití.

>**Začínáme**: Další informace o používání fakturačních api najdete v [tématu Azure Billing Usage and RateCard API overview](../../cost-management-billing/manage/usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Předvídejte budoucí náklady

I když je náročné odhadnout náklady dopředu, Azure má nástroje, které vám můžou pomoct. Má [cenovou kalkulačku,](https://azure.microsoft.com/pricing/calculator/) která vám pomůže odhadnout náklady na nasazené prostředky. Můžete také použít fakturační prostředky na portálu a fakturační rozhraní REST api odhadnout budoucí náklady, na základě aktuální spotřeby.

>**Začínáme:** Viz [Využití fakturace Azure a RateCard API přehled](../../cost-management-billing/manage/usage-rate-card-overview.md).
