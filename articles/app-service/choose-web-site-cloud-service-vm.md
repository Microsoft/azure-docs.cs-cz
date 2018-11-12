---
title: Porovnání služeb Azure App Service, Virtual Machines, Service Fabric a Cloud Services | Dokumentace Microsoftu
description: Zjistěte, jak si pro hostování webových aplikací vybrat mezi službami Azure App Service, Virtual Machines, Service Fabric a Cloud Services.
services: app-service\web, virtual-machines, cloud-services
documentationcenter: ''
author: cephalin
manager: jeconnoc
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 56d5f3b480ddb000f9118fbcc36e39817cb0bce8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238501"
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Porovnání služeb App Service, Virtual Machines, Service Fabric a Cloud Services

Azure nabízí několik způsobů hostování webů: [Azure App Service][Azure App Service], [Virtual Machines][Virtual Machines], [Service Fabric][Service Fabric] a [Cloud Services][Cloud Services]. Tento článek vám pomůže porozumět možnostem a učinit správnou volbu pro vaši webovou aplikaci.

Azure App Service je nejlepší volbou pro většinu webových aplikací. Nasazování a správa jsou integrované přímo do platformy, weby se rychle škálují pro zvládnutí vysokého přenosového zatížení a integrované vyrovnávání zatížení a služba Traffic Manager zajišťují vysokou dostupnost. Do služby Azure App Service můžete snadno přesunout existující weby pomocí [online nástroje pro migraci][migrate-tool], můžete použít open source aplikaci z galerie webových aplikací nebo vytvořit nový web pomocí rozhraní a nástrojů podle vašeho výběru. Díky funkci [WebJobs][WebJobs] můžete do své webové aplikace App Service snadno přidat zpracování úloh na pozadí.

Service Fabric je dobrou volbou v případě, že vytváříte novou aplikaci nebo přepisujete existující aplikaci pro využití architektury mikroslužeb. Aplikace spouštěné ve sdíleném fondu počítačů můžou začínat jako malé a podle potřeby se rozrůstat a masivně škálovat na stovky nebo tisíce počítačů. Stavové služby usnadňují konzistentní a spolehlivé ukládání stavu aplikace a Service Fabric automaticky spravuje dělení, škálování a dostupnost služeb za vás.  Service Fabric také podporuje webová rozhraní API s Open Web Interface pro .NET (OWIN) a ASP.NET Core.  V porovnání se službou App Service poskytuje Service Fabric také větší kontrolu nad základní infrastrukturou nebo přímý přístup k ní. Ke svým serverům se můžete vzdáleně připojit nebo na nich nakonfigurovat úlohy při spuštění. Služba Cloud Services je podobná Service Fabric, co se týče úrovně kontroly a snadnosti použití, ale už se jedná o starší službu a pro nová nasazení doporučujeme použít Service Fabric.

Pokud máte existující aplikace, které by pro spouštění ve službě App Service nebo v Service Fabric vyžadovaly výrazné změny, můžete migraci do cloudu zjednodušit zvolením služby Virtual Machines. Správná konfigurace, zabezpečení a údržba virtuálních počítačů však v porovnání se službou Azure App Service a Service Fabric vyžaduje mnohem více času a odborných znalostí v oblasti IT. Pokud zvažujete službu Azure Virtual Machines, nezapomeňte vzít v úvahu náročnost průběžné údržby, kterou vyžadují opravy, aktualizace a správa vašeho prostředí virtuálních počítačů. Azure Virtual Machines je služba IaaS (infrastruktura jako služba), zatímco App Service a Service Fabric jsou službami PaaS (platforma jako služba). 

## <a name="features"></a>Porovnání funkcí
Následující tabulka porovnává možnosti služeb App Service, Cloud Services, Virtual Machines a Service Fabric a pomůže vám správně se rozhodnout. Aktuální informace o smlouvách SLA pro jednotlivé možnosti najdete na stránce [Smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/).

| Funkce | App Service (webové aplikace) | Cloud Services (webové role) | Virtuální počítače | Service Fabric | Poznámky |
| --- | --- | --- | --- | --- | --- |
| Téměř okamžité nasazení |× | | |× |Nasazení aplikace nebo aktualizace aplikace do cloudové služby nebo vytvoření virtuálního počítače trvá minimálně několik minut. Nasazení aplikace do webové aplikace trvá několik sekund. |
| Vertikální navýšení kapacity na větší počítače bez opětovného nasazení |× | | |× | |
| Instance webových serverů sdílejí obsah a konfiguraci, což znamená, že při škálování nemusíte znovu provádět nasazení ani konfiguraci. |× | | |× | |
| Více prostředí pro nasazení (produkční a přípravné) |× |× | |× |Service Fabric umožňuje mít pro aplikace více prostředí nebo nasazovat různé verze aplikace vedle sebe. |
| Automatická správa aktualizací operačního systému |× |× | | |Částečná podpora prostřednictvím aplikace pro orchestraci oprav (POA) a úplná podpora v budoucnu. |
| Bezproblémové přepínání platformy (snadný přesun mezi 32bitovou a 64bitovou platformou) |× |× | | | |
| Nasazení kódu pomocí Gitu a protokolu FTP |× | |× | | |
| Nasazení kódu pomocí rozšíření Nasazení webu |× | |× | |Služba Cloud Services podporuje použití rozšíření Nasazení webu k nasazování aktualizací do jednotlivých instancí rolí. Nemůžete ho však použít k počátečnímu nasazení role, a pokud použijete Nasazení webu pro aktualizaci, musíte ji nasadit do každé instance role samostatně. Více instancí se vyžaduje k získání nároku na smlouvu SLA na službu Cloud Services pro produkční prostředí. |
| Podpora WebMatrixu |× | |× | | |
| Přístup ke službám, jako jsou Service Bus, Storage a SQL Database |× |× |× |× | |
| Hostování vrstvy webů nebo webových služeb z vícevrstvé architektury |× |× |× |× | |
| Hostování střední vrstvy vícevrstvé architektury |× |× |× |× |Webové aplikace App Service můžou snadno hostovat střední vrstvu rozhraní REST API a funkce [WebJobs](https://go.microsoft.com/fwlink/?linkid=390226) může hostovat úlohy zpracování na pozadí. WebJobs můžete spouštět na vyhrazeném webu a dosáhnout tak nezávislého škálování této úrovně. |
| Integrovaná podpora MySQL jako služby |× |× | | | |
| Podpora ASP.NET, klasického ASP, Node.js, PHP a Pythonu |× |× |× |× |Service Fabric podporuje vytvoření webového front-endu pomocí [ASP.NET 5](../service-fabric/service-fabric-reliable-services-communication-aspnetcore.md), případně můžete nasadit jakýkoli typ aplikace (Node.js, Java atd.) jako [spustitelný soubor typu Host](../service-fabric/service-fabric-guest-executables-introduction.md). |
| Horizontální navýšení kapacity na několik instancí bez opětovného nasazení |× |× |× |× |Služba Virtual Machines se může škálovat na více instancí, ale služby v ní spuštěné musí být napsané tak, aby si s tímto škálováním na více instancí poradily. Je potřeba nakonfigurovat nástroj pro vyrovnávání zatížení, který bude přesměrovávat požadavky do počítačů, a ujistit se, že máte v [sadě dostupnosti](../virtual-machines/windows/manage-availability.md) více než jednu instanci virtuálního počítače. |
| Podpora protokolu SSL |× |× |× |× |Webové aplikace App Service podporují SSL pro vlastní názvy domén pouze v režimu Basic a Standard. Informace o použití SSL s webovými aplikacemi najdete v tématu [Konfigurace certifikátu SSL pro web Azure](app-service-web-tutorial-custom-ssl.md). |
| Integrace se sadou Visual Studio |× |× |× |× | |
| Vzdálené ladění |× |× |× | | |
| Nasazení kódu pomocí sady Team Foundation Server |× |× |× |× | |
| Izolace sítě pomocí služby [Azure Virtual Network](/azure/virtual-network/) |× |× |× |× |Viz také [Integrace služby Virtual Network ve službě Azure Websites](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| Podpora služby [Azure Traffic Manager](/azure/traffic-manager/) |× |× |× |× | |
| Integrované monitorování koncových bodů |× |× |× | | |
| Přístup k serveru přes Vzdálenou plochu | |× |× |× | |
| Instalace jakékoli vlastní Instalační služby MSI | |× |× |× |Service Fabric umožňuje hostovat jakýkoli spustitelný soubor jako [spustitelný soubor typu Host](../service-fabric/service-fabric-guest-executables-introduction.md), případně můžete na virtuální počítače nainstalovat jakoukoli aplikaci. |
| Možnost definovat a spouštět úlohy při spuštění | |× |× |× | |
| Možnost naslouchat událostem Trasování událostí pro Windows | |× |× |× | |

## <a name="scenarios"></a>Scénáře a doporučení
Tady je několik běžných scénářů aplikací s doporučeními, jaká možnost hostování webů Azure by mohla pro každý z nich být nejvhodnější.

* [Potřebuji webový front-end se zpracováním na pozadí a back-end s databází pro provoz obchodních aplikací integrovaných s místními prostředky.](#onprem)
* [Potřebuji spolehlivě hostovat podnikový web, který se bude dobře škálovat a bude globálně dostupný.](#corp)
* [Mám aplikaci služby IIS6, která běží na Windows Serveru 2003.](#iis6)
* [Vlastním malý podnik a potřebuji levně hostovat svůj web, avšak s ohledem na budoucí rozvoj.](#smallbusiness)
* [Jsem návrhář/ka webů nebo grafiky a chci navrhovat a vytvářet weby pro své zákazníky.](#designer)
* [Migruji svou vícevrstvou aplikaci s webovým front-endem do cloudu.](#multitier)
* [Moje aplikace závisí na vysoce přizpůsobených prostředích Windows nebo Linuxu a chci ji přesunout do cloudu.](#custom)
* [Můj web využívá open source software a chci ho hostovat v Azure.](#oss)
* [Mám obchodní aplikaci, která vyžaduje připojení k podnikové síti.](#lob)
* [Chci hostovat rozhraní REST API nebo webovou službu pro mobilní klienty.](#mobile)

### <a id="onprem"></a>Potřebuji webový front-end se zpracováním na pozadí a back-end s databází pro provoz obchodních aplikací integrovaných s místními prostředky
Azure App Service je skvělým řešením pro složité obchodní aplikace. Umožňuje vyvíjet aplikace, které se automaticky škálují na platformě s vyrovnáváním zatížení, jsou zabezpečené pomocí služby Active Directory a připojují se k místním prostředkům. Usnadňuje správu těchto aplikací prostřednictvím špičkového portálu a rozhraní API a díky nástrojům App Insights umožňuje získat přehled o způsobu, jakým zákazníci tyto aplikace používají. Funkce [WebJobs][Webjobs] umožňuje spouštět procesy a úlohy na pozadí jako součást vrstvy webu, zatímco hybridní možnosti připojení a funkce virtuálních sítí usnadňují zpětné připojení k místním prostředkům. Azure App Service poskytuje pro webové aplikace smlouvy SLA s 99,9% dostupností a umožňuje:

* Spolehlivý provoz aplikací na cloudové platformě s automatickými opravami.
* Automatické škálování napříč globální sítí datacenter.
* Zotavení po havárii díky zálohování a obnovení.
* Zajištění kompatibility se standardy ISO, SOC2 a PCI.
* Integrace se službou Active Directory

### <a id="corp"></a>Potřebuji spolehlivě hostovat podnikový web, který se bude dobře škálovat a bude globálně dostupný
Azure App Service je skvělým řešením pro hostování podnikových webů. Umožňuje podle potřeby rychlé a snadné škálování webových aplikací napříč globální sítí datacenter. Nabízí místní dosah, odolnost proti chybám a inteligentní řízení provozu. To vše na jedné platformě, která poskytuje špičkové nástroje pro správu, pomocí kterých můžete rychle a snadno získat přehled o stavu webu a provozu na webu. Azure App Service poskytuje pro webové aplikace smlouvy SLA s 99,9% dostupností a umožňuje:

* Spolehlivý provoz webů na cloudové platformě s automatickými opravami.
* Automatické škálování napříč globální sítí datacenter.
* Zotavení po havárii díky zálohování a obnovení.
* Správu protokolu a provozu pomocí integrovaných nástrojů.
* Zajištění kompatibility se standardy ISO, SOC2 a PCI.
* Integrace se službou Active Directory

### <a id="iis6"></a>Mám aplikaci služby IIS6, která běží na Windows Serveru 2003
Se službou Azure App Service se snadno vyhnete nákladům na infrastrukturu souvisejícím s migrací starších aplikací služby IIS6. Společnost Microsoft vytvořila [snadno použitelné nástroje pro migraci a podrobné doprovodné materiály k migraci][migrate-tool], díky kterým můžete zkontrolovat kompatibilitu a identifikovat případné změny, které je potřeba provést. Integrace se sadou Visual Studio, Team Foundation Server a běžnými nástroji CMS usnadňuje nasazování aplikací služby IIS6 přímo do cloudu. Po nasazení najdete na webu Azure Portal robustní nástroje pro správu, pomocí kterých můžete vertikálně snížit kapacitu a snížit tak náklady, nebo podle potřeby vertikálně zvýšit kapacitu. Tento nástroj pro migraci umožňuje:

* Rychle a snadno migrovat starší verze webových aplikací z Windows Serveru 2003 do cloudu.
* Rozhodnout se ponechat připojenou databázi SQL v místním prostředí a vytvořit hybridní aplikaci.
* Automaticky přesunout databázi SQL společně se starší verzí aplikace.

### <a id="smallbusiness"></a>Vlastním malý podnik a potřebuji levně hostovat svůj web, avšak s ohledem na budoucí rozvoj
Služba Azure App Service je skvělým řešením pro tento scénář, protože ji můžete začít používat zdarma a následně podle potřeby přidávat další funkce. Každá bezplatná webová aplikace zahrnuje doménu od Azure (*vaše_společnost*.azurewebsites.net) a platforma zahrnuje integrované nástroje pro nasazování a správu a také galerii aplikací, která vám pomůže začít. Existuje řada dalších služeb a možností škálování, které umožňují rozvoj webu s rostoucí poptávkou uživatelů. Azure App Service umožňuje:

* Začít s bezplatnou úrovní a podle potřeby vertikálně navyšovat kapacitu.
* Použít galerii aplikací k rychlému nastavení oblíbených webových aplikací, jako je například WordPress.
* Přidávat do aplikace další služby a funkce Azure podle potřeby.
* Zabezpečit webovou aplikaci pomocí protokolu HTTPS.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a id="designer"></a>Jsem návrhář/ka webů nebo grafiky a chci navrhovat a vytvářet weby pro své zákazníky
Pro účely vývojářů a návrhářů webů se Azure App Service snadno integruje s nejrůznějšími architekturami a nástroji, zahrnuje podporu nasazení pomocí Gitu a protokolu FTP a nabízí úzkou integraci s nástroji a službami, jako jsou sada Visual Studio a služba SQL Database. App Service umožňuje:

* Používat nástroje příkazového řádku pro [automatizované úlohy][scripting].
* Pracovat s oblíbenými jazyky, jako jsou [.Net][dotnet], [PHP][PHP], [Node.js][nodejs] a [Python][Python].
* Výběr ze tří různých úrovní škálování pro zajištění velmi velkého vertikálního navýšení kapacity.
* Integraci s dalšími službami Azure, jako jsou[SQL Database][sqldatabase], [Service Bus][servicebus] a [Storage][Storage], nebo nabídkami partnerů z [Azure Storu][azurestore], jako jsou MySQL a MongoDB.
* Integraci s nástroji, jako je sada Visual Studio, Git, WebMatrix, Nasazení webu, sada TFS a protokol FTP.

### <a id="multitier"></a>Migruji svou vícevrstvou aplikaci s webovým front-endem do cloudu
Pokud používáte vícevrstvou aplikaci, například webový server, který se připojuje k databázi, je dobrou možností služba Azure App Service, která nabízí úzkou integraci se službou Azure SQL Database. Kromě toho můžete využít funkci WebJobs ke spouštění procesů na pozadí.

Zvolte pro jednu nebo několik vrstev Service Fabric, pokud potřebujete větší kontrolu nad serverovým prostředím, například možnost vzdáleného připojení k serveru nebo konfigurace úloh při spuštění serveru.

Zvolte pro jednu nebo několik vrstev službu Virtual Machines, pokud chcete použít vlastní image počítače nebo spouštět serverový software nebo služby, které nemůžete nakonfigurovat v Service Fabric.

### <a id="custom"></a>Moje aplikace závisí na vysoce přizpůsobených prostředích Windows nebo Linuxu a chci ji přesunout do cloudu
Pokud vaše aplikace vyžaduje složitou instalace nebo konfiguraci softwaru a operačního systému, nejlepším řešením je pravděpodobně služba Virtual Machines. Služba Virtual Machines umožňuje:

* Začít s operačním systémem, jako je Windows nebo Linux, z galerie virtuálních počítačů a následně ho přizpůsobit pro potřeby vaší aplikace.
* Vytvořit a nahrát vlastní image existujícího místního serveru, která poběží na virtuálním počítači v Azure.

### <a id="oss"></a>Můj web využívá open source software a chci ho hostovat v Azure
Pokud se vaše open source architektura podporuje ve službě App Service, pak se jazyky a rozhraní, které vaše aplikace vyžaduje, automaticky nakonfigurují za vás. App Service umožňuje:

* Používat řadu oblíbených open source jazyků, jako jsou [.NET][dotnet], [PHP][PHP], [Node.js][nodejs] a [Python][Python].
* Nastavit WordPress, Drupal, Umbraco, DNN a řadu dalších webových aplikací třetích stran.
* Migrovat existující aplikaci nebo vytvořit novou z galerie aplikací.

Pokud se vaše open source architektura ve službě App Service nepodporuje, můžete k jejímu provozu použít některou z dalších možností hostování webů Azure. V případě služby Virtual Machines instalujete a konfigurujete software v imagi počítače, která může být založená na Windows nebo Linuxu.

### <a id="lob"></a>Mám obchodní aplikaci, která vyžaduje připojení k podnikové síti
Pokud chcete vytvořit obchodní aplikaci, váš web možná bude vyžadovat přímý přístup ke službám nebo datům v podnikové síti. To je možné v App Service, Service Fabric a službě Virtual Machines pomocí [služby Azure Virtual Network](/azure/virtual-network/). Ve službě App Service můžete použít [funkci integrace virtuální sítě](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), která umožňuje provoz vašich aplikací Azure, jako kdyby byly ve vaší podnikové síti.

### <a id="mobile"></a>Chci hostovat rozhraní REST API nebo webovou službu pro mobilní klienty
Webové služby založené na protokolu HTTP umožňují podporovat širokou škálu klientů, včetně mobilních klientů. Rozhraní, jako je webové rozhraní API ASP.NET, se integrují se sadou Visual Studio a usnadňují tak vytváření a využívání služeb REST.  Tyto služby zveřejňuje webový koncový bod, takže je možné pro podporu tohoto scénáře použít jakoukoli techniku hostování webů v Azure. App Service je však skvělou volbou pro hostování rozhraní REST API. App Service umožňuje:

* Rychle vytvořit [mobilní aplikaci](../app-service-mobile/app-service-mobile-value-prop.md) nebo aplikaci API pro hostování webové služby HTTP v některém z globálně distribuovaných datacenter Azure.
* Migrovat existující služby nebo vytvářet nové.
* Dosáhnout smlouvy SLA na dostupnost s jedinou instancí nebo horizontálně navýšit kapacitu na několik vyhrazených počítačů.
* Použít publikovaný web k poskytování rozhraní REST API jakýmkoli klientům protokolu HTTP, včetně mobilních klientů.

> [!NOTE]
> Pokud chcete začít používat Azure App Service před registrací účtu, přejděte na adresu <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, kde si můžete hned a bezplatně vytvořit krátkodobou úvodní aplikaci ve službě Azure App Service. Nevyžaduje se žádná platební karta a nevzniká žádný závazek.
> 
> 

## <a id="nextsteps"></a>Další kroky
Další informace o těchto třech možnostech hostování webů najdete v tématu [Představujeme Azure](../fundamentals-introduction-to-azure.md).

Následující materiály vám pomůžou začít s možnostmi, které jste pro svou aplikaci zvolili:

* [Azure App Service](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Azure Virtual Machines](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
[migrate-tool]: https://www.movemetothecloud.net/
