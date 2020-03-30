---
title: Úvod do zabezpečení Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o Azure Security, jeho službách a o tom, jak funguje.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2019
ms.author: TomSh
ms.openlocfilehash: a1dbabafe32e013d526ed88a83e446ee765cdb7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045867"
---
# <a name="introduction-to-azure-security"></a>Seznámení se zabezpečením Azure
## <a name="overview"></a>Přehled
Víme, že zabezpečení je úloha jedna v cloudu a jak je důležité, abyste našli přesné a včasné informace o zabezpečení Azure. Jedním z nejlepších důvodů, proč používat Azure pro vaše aplikace a služby, je využít výhod jeho široké škály nástrojů a možností zabezpečení. Tyto nástroje a funkce umožňují vytvářet zabezpečená řešení na zabezpečené platformě Azure. Microsoft Azure poskytuje důvěrnost, integritu a dostupnost zákaznických dat a zároveň umožňuje transparentní odpovědnost.

Tento článek poskytuje komplexní přehled o zabezpečení dostupném v Azure.

### <a name="azure-platform"></a>Platforma Azure
Azure je platforma veřejných cloudových služeb, která podporuje široký výběr operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení. Může spouštět linuxové kontejnery s integrací Dockeru; vytvářet aplikace s JavaScriptem, Pythonem, .NET, PHP, Javou a node.js; pro zařízení se systémy iOS, Android a Windows.

Služby veřejného cloudu Azure podporují stejné technologie, na které se miliony vývojářů a IT profesionálů už spoléhají a kterým důvěřujeme. Když budete stavět na poskytovateli veřejných cloudových služeb nebo na něm migrovat, spoléháte se na schopnosti této organizace chránit vaše aplikace a data pomocí služeb a ovládacích prvků, které poskytují pro správu zabezpečení vašich cloudových prostředků.

Infrastruktura Azure je navržena od zařízení až po aplikace pro současné hostování milionů zákazníků a poskytuje důvěryhodný základ, na kterém mohou firmy splnit své požadavky na zabezpečení.

Kromě toho vám Azure poskytuje širokou škálu konfigurovatelných možností zabezpečení a možnost jejich řízení, takže si můžete přizpůsobit zabezpečení tak, aby splňovalo jedinečné požadavky nasazení vaší organizace. Tento dokument vám pomůže pochopit, jak vám možnosti zabezpečení Azure můžou pomoci splnit tyto požadavky.

> [!Note]
> Primární zaměření tohoto dokumentu je na ovládací prvky orientované na zákazníka, které můžete použít k přizpůsobení a zvýšení zabezpečení aplikací a služeb.
>
> Informace o tom, jak Microsoft zabezpečuje samotnou platformu Azure, najdete v [tématu Zabezpečení infrastruktury Azure](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Souhrn možností zabezpečení Azure

### <a name="features-to-secure-the-azure-platform"></a>Funkce pro zabezpečení platformy Azure
Následující funkce jsou funkce, které můžete zkontrolovat a poskytnout jistotu, že platforma Azure je spravovaná zabezpečeným způsobem. Odkazy byly poskytnuty pro další podrobné informace o tom, jak společnost Microsoft řeší otázky týkající se důvěryhodnosti zákazníků ve čtyřech oblastech: zabezpečená platforma, ochrana osobních údajů & ovládací prvky, dodržování předpisů a transparentnost.

| [Bezpečná platforma](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [Ovládací prvky & ochrany osobních údajů](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[Dodržování předpisů](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [Průhlednost](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Cyklus vývoje bezpečnosti](https://www.microsoft.com/sdl/), interní audity | [Správa dat po celou dobu](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [Centrum zabezpečení](https://www.microsoft.com/trustcenter/default.aspx) |[Jak Microsoft zabezpečuje zákaznická data ve službách Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [Povinné bezpečnostní školení, ověření spolehlivosti](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Řízení umístění dat](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [Společné centrum ovládacích prvků](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Jak Microsoft spravuje umístění dat ve službách Azure](https://azuredatacentermap.azurewebsites.net/)|
| [Penetrační testování](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [detekce vniknutí, DDoS](https://www.microsoft.com/trustcenter/Security/ThreatManagement), [Audity & protokolování](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [Poskytněte přístup k datům podle svých podmínek](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Kontrolní seznam hloubkové kontroly cloudových služeb](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Kdo v Microsoftu má přístup k vašim datům za jakých podmínek](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Nejmodernější datové centrum](https://www.microsoft.com/cloud-platform/global-datacenters), fyzické zabezpečení, [bezpečná síť](network-overview.md) | [Reakce na vymáhání práva](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Dodržování předpisů podle služeb, umístění & průmysl](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Jak Microsoft zabezpečuje zákaznická data ve službách Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [Reakce na bezpečnostní incidenty](https://aka.ms/SecurityResponsepaper), [sdílená odpovědnost](https://aka.ms/sharedresponsibility) |[Přísné standardy ochrany osobních údajů](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Kontrola certifikace pro služby Azure, centrum transparentnosti](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>Funkce pro zabezpečení dat a aplikací
V závislosti na modelu cloudové služby je variabilní odpovědnost za to, kdo je zodpovědný za správu zabezpečení aplikace nebo služby. V platformě Azure jsou k dispozici funkce, které vám pomohou při plnění těchto povinností prostřednictvím integrovaných funkcí a prostřednictvím partnerských řešení, která lze nasadit do předplatného Azure.

Integrované funkce jsou uspořádány do šesti funkčních oblastí: Operace, Aplikace, Úložiště, Sítě, Výpočetní prostředky a Identita. Další podrobnosti o funkcích a možnostech dostupných v platformě Azure v těchto šesti oblastech jsou k dispozici prostřednictvím souhrnných informací.

## <a name="operations"></a>Provoz
Tato část obsahuje další informace týkající se klíčových funkcí operací zabezpečení a souhrnné informace o těchto funkcích.

### <a name="security-and-audit-dashboard"></a>Řídicí panel zabezpečení a auditu
[Řešení zabezpečení a auditposkytuje](../../security-center/security-center-intro.md) komplexní přehled o stavu zabezpečení IT vaší organizace pomocí [integrovaných vyhledávacích dotazů](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) na významné problémy, které vyžadují vaši pozornost. Řídicí panel [zabezpečení a auditování](https://technet.microsoft.com/library/mt484091.aspx) je domovskou obrazovkou pro vše, co souvisí se zabezpečením v protokolech Azure Monitoru. Poskytuje podrobné informace o stavu zabezpečení počítačů. Umožňuje také zobrazit všechny události za posledních 24 hodin, 7 dní nebo za jakýkoli zadaný interval.

Kromě toho můžete nakonfigurovat dodržování předpisů & zabezpečení tak, aby [automaticky provádělo určité akce](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) při zjištění určité události.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../../azure-resource-manager/management/deployment-models.md) umožňuje pracovat s prostředky ve vašem řešení jako skupina. Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Pro nasazení používáte [šablonu Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) a tato šablona může fungovat pro různá prostředí, jako je testování, příprava a produkční prostředí. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

Nasazení založená na šablonách Azure Resource Manageru pomáhají zlepšit zabezpečení řešení nasazených v Azure, protože standardní nastavení řízení zabezpečení a dá se integrovat do standardizovaných nasazení založených na šablonách. To snižuje riziko chyb konfigurace zabezpečení, ke kterým může dojít během ručního nasazení.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) je rozšiřitelná služba Správy výkonu aplikací (APM) pro webové vývojáře. Pomocí Application Insights můžete sledovat živé webové aplikace a automaticky detekovat anomálie výkonu. Obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé skutečně dělají s vašimi aplikacemi. Monitoruje vaši aplikaci po celou dobu, po kterou je spuštěna, a to jak během testování, tak po publikování nebo nasazení.

Application Insights vytváří grafy a tabulky, které vám například ukazují, jaké denní doby získáte většinu uživatelů, jak je aplikace responzivní a jak dobře ji obsluhují všechny externí služby, na kterých závisí.

Pokud dojde k selhání, selhání nebo problémy s výkonem, můžete prohledávat telemetrická data podrobně diagnostikovat příčinu. A služba vám pošle e-maily, pokud dojde ke změnám v dostupnosti a výkonu vaší aplikace. Aplikace Insight se tak stává cenným nástrojem zabezpečení, protože pomáhá s dostupností v triádě o důvěrnosti, integritě a dostupnosti zabezpečení.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) nabízí vizualizaci, dotaz, směrování, výstrahy, automatické škálování a automatizaci na datech jak z infrastruktury Azure ([protokol aktivit),](../../azure-monitor/platform/platform-logs-overview.md)tak z každého jednotlivého prostředku Azure ([diagnostické protokoly).](../../azure-monitor/platform/platform-logs-overview.md) Azure Monitor můžete použít k upozornění na události související se zabezpečením, které jsou generovány v protokolech Azure.

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
[Protokoly Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics/) – poskytuje řešení pro správu IT pro místní i třetí strany cloudové infrastruktury (například AWS) kromě prostředků Azure. Data z Azure Monitoru lze směrovat přímo do protokolů Azure Monitoru, takže můžete zobrazit metriky a protokoly pro celé prostředí na jednom místě.

Protokoly Azure Monitor může být užitečný nástroj v forenzní a jiné analýzy zabezpečení, protože nástroj umožňuje rychle prohledávat velké množství položek souvisejících se zabezpečením s flexibilní přístup dotazu. Kromě toho místní [brána firewall a protokoly proxy lze exportovat do Azure a zpřístupnit pro analýzu pomocí protokolů Azure Monitor.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../../advisor/index.yml) je personalizovaný cloudový konzultant, který vám pomůže optimalizovat nasazení Azure. Analyzuje telemetrii využití a konfiguraci prostředků. Doporučuje pak řešení, která pomohou zlepšit [výkon](../../advisor/advisor-performance-recommendations.md), [zabezpečení](../../advisor/advisor-security-recommendations.md)a [vysokou dostupnost](../../advisor/advisor-high-availability-recommendations.md) vašich prostředků při hledání příležitostí ke [snížení celkových výdajů Azure](../../advisor/advisor-cost-recommendations.md). Azure Advisor poskytuje doporučení zabezpečení, která můžou výrazně zlepšit vaše celkové držení těla zabezpečení pro řešení, která nasadíte v Azure. Tato doporučení jsou čerpána z analýzy zabezpečení provedené [Azure Security Center.](../../security-center/security-center-intro.md)

### <a name="azure-security-center"></a>Azure Security Center
[Security Center](../../security-center/security-center-intro.md) vám pomůže předcházet, zjišťovat a reagovat na hrozby se zvýšeným přehledem a kontrolou nad zabezpečením vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Centrum zabezpečení navíc pomáhá s operacemi zabezpečení tím, že poskytuje jeden řídicí panel, který zobrazuje výstrahy a doporučení, na které lze okamžitě reagovat. Často můžete napravit problémy jediným kliknutím v konzole Centra zabezpečení.
## <a name="applications"></a>Aplikace
Část obsahuje další informace týkající se klíčových funkcí zabezpečení aplikací a souhrnné informace o těchto funkcích.

### <a name="web-application-vulnerability-scanning"></a>Prohledávání chyb zabezpečení webové aplikace
Jedním z nejjednodušších způsobů, jak začít s testováním chyb zabezpečení v [aplikaci App Service,](../../app-service/overview.md) je použití [integrace se zabezpečením Tinfoil](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) k provedení kontroly zranitelnosti jedním kliknutím ve vaší aplikaci. Výsledky testů můžete zobrazit v přehledu srozumitelných a zjistit, jak opravit každou chybu zabezpečení pomocí podrobných pokynů.

### <a name="penetration-testing"></a>Penetrační testování
Pokud dáváte přednost provádění vlastních penetračních testů nebo chcete použít jinou sadu skeneru nebo poskytovatele, musíte postupovat podle [procesu schválení penetračního testování Azure](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) a získat předchozí souhlas k provedení požadovaných penetračních testů.

### <a name="web-application-firewall"></a>Brána firewall webové aplikace
Brána firewall webových aplikací (WAF) v [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) pomáhá chránit webové aplikace před běžnými webovými útoky, jako je vkládání SQL, útoky skriptování mezi weby a zneužití relace. Dodává se s předkonfigurovanou ochranou před hrozbami identifikovanými [projektem OWASP (Open Web Application Security Project) jako 10 nejčastějších chyb zabezpečení](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Ověřování a autorizace v prostředí Azure App Service
[Ověřování / autorizace služby App Service](../../app-service/overview-authentication-authorization.md) je funkce, která umožňuje aplikaci přihlašovat uživatele, abyste nemuseli měnit kód v back-endu aplikace. Poskytuje snadný způsob ochrany aplikace a práci s daty pro jednotlivé uživatele.

### <a name="layered-security-architecture"></a>Architektura zabezpečení s vrstvami
Vzhledem k [tomu, že prostředí služby App Service](../../app-service/environment/app-service-app-service-environment-intro.md) poskytují izolované runtime prostředí nasazené do [virtuální sítě Azure](../../virtual-network/virtual-networks-overview.md), mohou vývojáři vytvořit architekturu zabezpečení s vrstvami, která poskytuje různé úrovně přístupu k síti pro každou aplikační vrstvu. Běžnou touhou je skrýt back-endy rozhraní API před obecným přístupem k Internetu a povolit volání rozhraní API pouze prostřednictvím webových aplikací pro upstream. [Skupiny zabezpečení sítě (NSGs)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) lze použít v podsítích virtuální sítě Azure obsahující prostředí služby App Service k omezení veřejného přístupu k aplikacím rozhraní API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostika webového serveru a diagnostika aplikací
Webové aplikace Služby App Service poskytují diagnostické funkce pro protokolování informací z webového serveru i webové aplikace. Ty jsou logicky rozděleny do [diagnostiky webového serveru](../../app-service/troubleshoot-diagnostic-logs.md) a [diagnostiky aplikací](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Webový server obsahuje dva hlavní pokroky v diagnostice a odstraňování problémů s weby a aplikacemi.

První novou funkcí jsou informace o stavu aplikací v reálném čase, o fondech aplikací, pracovních procesech, webech, doménách aplikací a spuštěných požadavcích. Druhou novou výhodou jsou podrobné trasovací události, které sledují požadavek v celém procesu požadavku a odpovědi.

Chcete-li povolit shromažďování těchto událostí trasování, lze službu IIS 7 nakonfigurovat tak, aby automaticky zaznamenávala úplné protokoly trasování ve formátu XML pro jakýkoli konkrétní požadavek na základě uplynulých časových kódů nebo kódů chybových odpovědí.

#### <a name="web-server-diagnostics"></a>Diagnostika webového serveru
Můžete povolit nebo zakázat následující druhy protokolů:

-   Podrobné protokolování chyb – podrobné informace o chybě pro stavové kódy HTTP, které označují selhání (stavový kód 400 nebo vyšší). To může obsahovat informace, které mohou pomoci určit, proč server vrátil kód chyby.

-   Trasování neúspěšných požadavků – podrobné informace o neúspěšných požadavcích, včetně trasování součástí služby IIS použitých ke zpracování požadavku a času v jednotlivých součástech. To může být užitečné, pokud se pokoušíte zvýšit výkon webu nebo izolovat, co je příčinou konkrétní chyby PROTOKOLU HTTP, které mají být vráceny.

-   Protokolování webového serveru – informace o transakcích PROTOKOLU HTTP pomocí rozšířeného formátu souboru protokolu W3C. To je užitečné při určování celkových metrik webu, jako je počet zpracovaných požadavků nebo počet požadavků z konkrétní ip adresy.

#### <a name="application-diagnostics"></a>Diagnostika aplikací
[Diagnostika aplikací](../../app-service/troubleshoot-diagnostic-logs.md) umožňuje zachytit informace vytvořené webovou aplikací. ASP.NET aplikace mohou použít třídu [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) k protokolování informací do protokolu diagnostiky aplikací. V konzoli Application Diagnostics se zobrazují dva hlavní typy událostí. Jedná se o události související s výkonem aplikací a události související se selháním a chybami aplikací. Selhání a chyby lze dále rozdělit na problémy s připojením, zabezpečením a selháním. Problémy se selháním obvykle souvisejí s kódem aplikace.

V konzoli Application Diagnostics lze zobrazit události seskupené následujícími způsoby:

-   Vše (Zobrazí se všechny události.)
-   Chyby aplikace (Zobrazí se události související s výjimkami.)
-   Výkon (Zobrazí se události související s výkonem.)

## <a name="storage"></a>Úložiště
Část obsahuje další informace týkající se klíčových funkcí v zabezpečení úložiště Azure a souhrnné informace o těchto funkcích.

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Účet úložiště můžete zabezpečit pomocí řízení přístupu na základě rolí (RBAC). Omezení přístupu na základě [potřeby znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) zásadzabezpečení je nezbytné pro organizace, které chtějí vynutit zásady zabezpečení pro přístup k datům. Tato přístupová práva jsou udělena přiřazením příslušné role RBAC skupinám a aplikacím v určitém oboru. K přiřazení oprávnění uživatelům můžete použít [předdefinované role RBAC](../../role-based-access-control/built-in-roles.md), například přispěvatel účtu úložiště. Přístup ke klíčům úložiště pro účet úložiště pomocí modelu [Azure Resource Manager](../../storage/blobs/security-recommendations.md) lze řídit pomocí řízení přístupu na základě rolí (RBAC).

### <a name="shared-access-signature"></a>Sdílený přístupový podpis
[Sdílený přístupový podpis (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. SAS znamená, že můžete udělit klientovi omezená oprávnění k objektům v účtu úložiště po určitou dobu a se zadanou sadou oprávnění. Tato omezená oprávnění můžete udělit, aniž byste museli sdílet přístupové klíče k účtu.

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Šifrování při přenosu je mechanismus ochrany dat při přenosu v sítích. S Azure Storage můžete zabezpečit data pomocí:
-   [Šifrování na úrovni přenosu](../../storage/blobs/security-recommendations.md), jako je například HTTPS při přenosu dat do nebo z Azure Storage.

-   [Šifrování vodičů](../../storage/blobs/security-recommendations.md), například [šifrování SMB 3.0](../../storage/blobs/security-recommendations.md) pro [sdílené složky Azure](../../storage/files/storage-dotnet-how-to-use-files.md).

-   Šifrování na straně klienta, šifrování dat před jejich přenosem do úložiště a dešifrování dat po jejich přenosu z úložiště.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Pro mnoho organizací je šifrování dat v klidovém stavu povinným krokem k ochraně osobních údajů, dodržování předpisů a suverenitě dat. Existují tři funkce zabezpečení úložiště Azure, které poskytují šifrování dat, která je "v klidovém stavu":

-   [Šifrování služby storage service](../../storage/common/storage-service-encryption.md) umožňuje požadovat, aby služba úložiště automaticky šifrovala data při zápisu do úložiště Azure.

-   [Šifrování na straně klienta](../../storage/common/storage-client-side-encryption.md) také poskytuje funkci šifrování v klidovém stavu.

-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) umožňuje šifrovat disky operačního systému a datové disky používané virtuálním počítačem IaaS.

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) provádí protokolování a poskytuje data metrik pro účet úložiště. Tato data můžete použít ke sledování požadavků, analýze trendů využití a diagnostice problémů s účtem úložiště. Analýza úložiště protokoluje podrobné informace o úspěšných a neúspěšných požadavcích na službu úložiště. Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí. Protokolují se následující typy ověřovaných požadavků:
-   Úspěšné požadavky.

-   Neúspěšné požadavky, včetně časového omezení, omezení, sítě, autorizace a dalších chyb.

-   Požadavky pomocí sdíleného přístupového podpisu (SAS), včetně neúspěšných a úspěšných požadavků.

-   Požadavky na analytická data.

### <a name="enabling-browser-based-clients-using-cors"></a>Povolení klientů založených na prohlížeči pomocí CORS
[Sdílení prostředků mezi zdroji (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) je mechanismus, který umožňuje domény udělit navzájem oprávnění pro přístup k navzájem prostředky. Uživatelský agent odesílá další záhlaví, aby zajistil, že kód JavaScript načtený z určité domény bude mít přístup k prostředkům umístěným v jiné doméně. Druhá doména pak odpoví s dalšími záhlavími, která povolují nebo odepírají původní doménu přístupu k jejím prostředkům.

Služby úložiště Azure nyní podporují CORS tak, že jakmile nastavíte pravidla CORS pro službu, vyhodnotí se správně ověřený požadavek na službu z jiné domény, aby se zjistilo, zda je povoleno podle pravidel, která jste zadali.

## <a name="networking"></a>Síťové služby
Část obsahuje další informace týkající se klíčových funkcí v zabezpečení sítě Azure a souhrnné informace o těchto funkcích.

### <a name="network-layer-controls"></a>Ovládací prvky síťové vrstvy
Řízení přístupu k síti je akt omezení připojení a z konkrétních zařízení nebo podsítí a představuje jádro zabezpečení sítě. Cílem řízení přístupu k síti je zajistit, aby vaše virtuální počítače a služby byly přístupné jenom uživatelům a zařízením, kterým je chcete zpřístupnit.

#### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
[Skupina zabezpečení sítě (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) je základní stavová brána firewall pro filtrování paketů a umožňuje řídit přístup na základě [5-n-tice](https://www.techopedia.com/definition/28190/5-tuple). Skupiny nsg neposkytují kontrolu aplikační vrstvy nebo ověřené ovládací prvky přístupu. Slouží k řízení provozu mezi podsítěmi v rámci virtuální sítě Azure a provozu mezi virtuální sítí Azure a Internetem.

#### <a name="route-control-and-forced-tunneling"></a>Řízení trasy a vynucené tunelování
Schopnost řídit chování směrování ve virtuálních sítích Azure je důležité zabezpečení sítě a možnost řízení přístupu. Například pokud chcete ujistěte se, že veškerý provoz do a z vaší virtuální sítě Azure prochází tímto virtuálním zařízením zabezpečení, musíte být schopni řídit a přizpůsobit chování směrování. To lze provést konfigurací uživatelem definovaných tras v Azure.

[Uživatelem definované trasy](../../virtual-network/virtual-networks-udr-overview.md) umožňují přizpůsobit příchozí a odchozí trasy pro přenosy, které se přesouvají do a z jednotlivých virtuálních počítačů nebo podsítí, a zajistit tak co nejbezpečnější trasu. [Vynucené tunelové propojení](https://www.petri.com/azure-forced-tunneling) je mechanismus, který můžete použít k zajištění, že vaše služby nejsou povoleny k zahájení připojení k zařízením v Internetu.

To se liší od možnosti přijímat příchozí připojení a pak na ně reagovat. Front-endové webové servery musí reagovat na požadavky od hostitelů v Internetu, a proto je povolen příchozí přenos z Internetu na tyto webové servery a webové servery mohou reagovat.

Vynucené tunelové propojení se běžně používá k vynucení odchozího provozu na Internetu, aby procházelmístní servery zabezpečení a brány firewall.

#### <a name="virtual-network-security-appliances"></a>Zařízení pro zabezpečení virtuální sítě
Zatímco skupiny zabezpečení sítě, uživatelem definované trasy a vynucené tunelové propojení poskytují úroveň zabezpečení v síťových a transportních vrstvách [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), může nastat doba, kdy chcete povolit zabezpečení na vyšších úrovních zásobníku. K těmto rozšířeným funkcím zabezpečení sítě můžete přistupovat pomocí řešení Azure partner network security appliance. Nejnovější řešení zabezpečení sítě partnerů Azure najdete tak, že navštívíte [Azure Marketplace](https://azure.microsoft.com/marketplace/) a vyhledáte "zabezpečení" a "zabezpečení sítě".

### <a name="azure-virtual-network"></a>Azure Virtual Network
Virtuální síť Azure je reprezentace vaší vlastní sítě v cloudu. Jedná se o logickou izolaci síťové struktury Azure vyhrazené pro vaše předplatné. V rámci této sítě máte plnou kontrolu nad bloky IP adres, nastavením DNS, zásadami zabezpečení a směrovacími tabulkami. Virtuální síť můžete rozdělit do podsítí a umístit virtuální počítače Azure IaaS (VM) nebo [cloudové služby (instance role PaaS)](../../cloud-services/cloud-services-choose-me.md) ve virtuálních sítích Azure.

Virtuální síť můžete navíc připojit k místní síti pomocí jedné z [možností připojení](../../vpn-gateway/index.yml) dostupných v Azure. V podstatě můžete svoji síť rozšířit do Azure s úplnou kontrolou nad bloky IP adres a s výhodou poskytovatelů Azure celopodnikového rozsahu.

Azure networking podporuje různé scénáře zabezpečeného vzdáleného přístupu. Zde jsou některá z vylepšení:

-   [Připojení jednotlivých pracovních stanic k virtuální síti Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Připojení místní sítě k virtuální síti Azure pomocí sítě VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [Připojení místní sítě k virtuální síti Azure pomocí vyhrazeného propojení WAN](../../expressroute/expressroute-introduction.md)

-   [Vzájemné propojení virtuálních sítí Azure](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN Gateway
Pokud chcete odeslat síťový provoz mezi virtuální sítí Azure a místním webem, musíte pro virtuální síť Azure vytvořit bránu VPN. [Brána VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) je typ brány virtuální sítě, která odesílá šifrovaný provoz přes veřejné připojení. Brány VPN můžete taky použít k odesílání provozu mezi virtuálními sítěmi Azure přes síťovou strukturu Azure.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) je vyhrazené propojení WAN, které umožňuje rozšířit místní sítě do cloudu Microsoftu prostřednictvím vyhrazeného privátního připojení, které usnadňuje poskytovatel připojení.

![ExpressRoute](./media/overview/azure-security-fig1.png)

Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 a CRM Online. Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), síť Ethernet typu point-to-point nebo virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění.

ExpressRoute připojení nejdou přes veřejný Internet, a proto lze považovat za bezpečnější než řešení založená na VPN. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](../../application-gateway/overview.md) poskytuje řadič pro doručování aplikací [(ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) jako službu, která nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaši aplikaci.

![Application Gateway](./media/overview/azure-security-fig2.png)

Umožňuje optimalizovat produktivitu webové farmy tím, že přeloží ukončení SSL náročné na procesor na aplikační bránu (známé také jako "SSL offload" nebo "SSL bridging"). Poskytuje také další možnosti směrování vrstvy 7, včetně distribuce příchozího provozu s každým robin, spřažení relací založených na souborech cookie, směrování na základě cesty URL a možnost i hostování více webů za jednou aplikační bránou. Služba Azure Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7.

Poskytuje převzetí služeb při selhání, směrování výkonu požadavků HTTP mezi různými servery, ať už jsou místní nebo v cloudu.

Aplikace poskytuje mnoho funkcí řadiče pro doručování aplikací (ADC), včetně vyrovnávání zatížení HTTP, spřažení relací na základě souborů cookie, [offloadu ssl (Secure Sockets Layer),](../../application-gateway/tutorial-restrict-web-traffic-powershell.md) sond y vlastního stavu, podpory pro více webů a mnoha dalších.

### <a name="web-application-firewall"></a>Web Application Firewall (Brána firewall webových aplikací)
Brána firewall webových aplikací je funkce [Brány aplikací Azure,](../../application-gateway/overview.md) která poskytuje ochranu webovým aplikacím, které používají aplikační bránu pro standardní funkce Řízení doručování aplikací (ADC). Firewall webových aplikací chrání webové aplikace před většinou z 10 nejčastějších webových hrozeb podle OWASP.

![Web Application Firewall (Brána firewall webových aplikací)](./media/overview/azure-security-fig1.png)

-   Ochrana před útoky prostřednictvím injektáže SQL.

-   Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

-   Ochrana před narušením protokolu HTTP.

-   Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

-   Ochrana před roboty, prohledávacími moduly a skenery.

-   Detekce běžných chybných konfigurací aplikací (tj. Apache, IIS atd.)

Centralizovaný firewall webových aplikací, který chrání před webovými útoky, značně zjednodušuje správu zabezpečení a nabízí lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Firewall webových aplikací umožňuje jednoduše převést stávající aplikační brány do služby Application Gateway.

### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) umožňuje řídit distribuci uživatelských přenosů pro koncové body služby v různých datových centrech. Mezi koncové body služby podporované Traffic Managerem patří virtuální počítače Azure, webové aplikace a cloudové služby. Službu Traffic Manager můžete použít také s externími koncovými body mimo Azure. Traffic Manager používá dns systém DNS k přesměrování požadavků klientů na nejvhodnější koncový bod na základě [metody směrování provozu](../../traffic-manager/traffic-manager-routing-methods.md) a stavu koncových bodů.

Traffic Manager poskytuje řadu metod směrování provozu, které vyhovují různým potřebám aplikací, [sledování](../../traffic-manager/traffic-manager-monitoring.md)stavu koncového bodu a automatické převzetí služeb při selhání. Služba Traffic Manager je odolná vůči selhání, a to i selhání celé oblasti Azure.

### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) poskytuje vašim aplikacím vysokou dostupnost a síťový výkon. Jedná se o vyrovnávání zatížení vrstvy 4 (TCP, UDP), která distribuuje příchozí provoz mezi instancemi služeb v pořádku definovanými v sadě s vyrovnáváním zatížení. Azure Load Balancer lze nakonfigurovat takto:

-   Vyrovnávání zatížení příchozí internetový provoz do virtuálních počítačů. Tato konfigurace se označuje jako [vyrovnávání zatížení směřující k Internetu](../../load-balancer/concepts-limitations.md#publicloadbalancer).

-   Provoz vyvažuje zatížení mezi virtuálními počítači ve virtuální síti, mezi virtuálními počítači v cloudových službách nebo mezi místními počítači a virtuálními počítači ve virtuální síti mezi místními. Tato konfigurace se označuje jako [vnitřní vyrovnávání zatížení](../../load-balancer/concepts-limitations.md#internalloadbalancer).

- Přeposílání externího provozu do konkrétního virtuálního počítače

### <a name="internal-dns"></a>Interní DNS
Můžete spravovat seznam serverů DNS používaných ve virtuální síti na portálu pro správu nebo v konfiguračním souboru sítě. Zákazník může pro každou virtuální síť přidat až 12 serverů DNS. Při zadávání serverů DNS je důležité ověřit, zda jsou seznam serverů DNS zákazníka ve správném pořadí pro prostředí zákazníka. Seznamy serverů DNS nefungují round-robin. Používají se v pořadí, v jakém jsou určeny. Pokud je možné dosáhnout prvního serveru DNS v seznamu, klient tento server DNS použije bez ohledu na to, zda server DNS funguje správně nebo ne. Chcete-li změnit pořadí serverů DNS pro virtuální síť zákazníka, odeberte servery DNS ze seznamu a přidejte je zpět v pořadí, které zákazník chce. Služba DNS podporuje aspekt dostupnosti bezpečnostní triády "CIA".

### <a name="azure-dns"></a>Azure DNS
[Dns je](https://technet.microsoft.com/library/bb629410.aspx)zodpovědný za překlad (nebo překlad) názvu webu nebo služby na jeho IP adresu. [Azure DNS](../../dns/dns-overview.md) je hostitelská služba určená pro domény DNS a zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. Služba DNS podporuje aspekt dostupnosti bezpečnostní triády "CIA".

### <a name="azure-monitor-logs-nsgs"></a>Azure Monitor protokoly nsgs
Pro skupiny sítě nsg můžete povolit následující kategorie diagnostických protokolů:

-   Událost: Obsahuje položky, pro které jsou pravidla skupiny zabezpečení zabezpečení použita pro virtuální počítače a role instancí na základě adresy MAC. Stav těchto pravidel se shromažďuje každých 60 sekund.

-   Čítač pravidel: Obsahuje položky, kolikrát je každé pravidlo skupiny nSG použito k odepření nebo povolení provozu.

### <a name="security-center"></a>Security Center
[Azure Security Center](../../security-center/security-center-intro.md) průběžně analyzuje stav zabezpečení vašich prostředků Azure pro osvědčené postupy zabezpečení sítě. Když Security Center identifikuje potenciální slabá místa zabezpečení, vytvoří [doporučení,](../../security-center/security-center-recommendations.md) která vás provedou procesem konfigurace potřebných ovládacích prvků pro posílení a ochranu vašich prostředků.

## <a name="compute"></a>Služba Compute
Část obsahuje další informace týkající se klíčových funkcí v této oblasti a souhrnné informace o těchto funkcích.

### <a name="antimalware--antivirus"></a>Antimalwarový & Antivirus
S Azure IaaS můžete používat antimalwarový software od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro, McAfee a Kaspersky, k ochraně vašich virtuálních počítačů před škodlivými soubory, adwarem a dalšími hrozbami. [Microsoft Antimalware](antimalware.md) pro Cloud Services azure a virtuální počítače je funkce ochrany, která pomáhá identifikovat a odstranit viry, spyware a další škodlivý software. Microsoft Antimalware poskytuje konfigurovatelné výstrahy, když se známý škodlivý nebo nežádoucí software pokusí nainstalovat sám nebo spustit ve vašich systémech Azure. Microsoft Antimalware lze také nasadit pomocí Azure Security Center

### <a name="hardware-security-module"></a>Modul hardwarového zabezpečení
Šifrování a ověřování nezlepšují zabezpečení, pokud nejsou chráněny samotné klíče. Můžete zjednodušit správu a zabezpečení důležitých tajných klíčů a klíčů jejich uložením v [azure key vault](../../key-vault/key-vault-overview.md). Trezor klíčů nabízí možnost ukládat klíče do hardwarových modulů zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 Level 2. Šifrovací klíče serveru SQL Server pro zálohování nebo [transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx) mohou být uloženy v trezoru klíčů s libovolnými klíči nebo tajnými klíči z vašich aplikací. Oprávnění a přístup k těmto chráněným položkám se spravují prostřednictvím [služby Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Zálohování virtuálního počítače
[Azure Backup](../../backup/backup-overview.md) je řešení, které chrání data aplikací s nulovými kapitálovými investicemi a minimálními provozními náklady. Chyby aplikací mohou poškodit vaše data a lidské chyby mohou zavádět chyby do vašich aplikací, které mohou vést k problémům se zabezpečením. S Azure Backup jsou chráněné vaše virtuální počítače se systémem Windows a Linux.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Důležitou součástí strategie [kontinuity a zotavení po havárii (BCDR)](../../best-practices-availability-paired-regions.md) vaší organizace je zjistit, jak udržet podnikové úlohy a aplikace v provozu, když dojde k plánovaným a neplánovaným výpadkům. [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) pomáhá organizovat replikaci, převzetí služeb při selhání a obnovení úloh a aplikací tak, aby byly dostupné ze sekundárního umístění, pokud dojde k primárnímu umístění.

### <a name="sql-vm-tde"></a>TDE virtuálního virtuálního provozu SQL
[Transparentní šifrování dat (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) a šifrování na úrovni sloupců (CLE) jsou funkce šifrování serveru SQL. Tato forma šifrování vyžaduje, aby zákazníci mohli spravovat a ukládat kryptografické klíče, které používáte pro šifrování.

Služba Azure Key Vault (AKV) je navržena tak, aby zlepšila zabezpečení a správu těchto klíčů v zabezpečeném a vysoce dostupném umístění. Sql Server Connector umožňuje SQL Server používat tyto klíče z Azure Key Vault.

Pokud používáte SQL Server s místními počítači, existují kroky, které můžete postupovat pro přístup k Azure Key Vault z místního počítače SQL Server. Ale pro SQL Server ve virtuálních počítačích Azure můžete ušetřit čas pomocí funkce Integrace úložiště klíčů Azure. Pomocí několika rutin Azure PowerShell, které tuto funkci povolí, můžete automatizovat konfiguraci potřebnou pro přístup k trezoru klíčů pro virtuální počítač SQL.

### <a name="vm-disk-encryption"></a>Šifrování disku virtuálního počítače
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) je nová funkce, která vám pomůže šifrovat vaše disky virtuálních počítačů Windows a Linux IaaS. Používá standardní funkci BitLocker systému Windows a funkci DM-Crypt linuxu, aby poskytoval šifrování svazku pro operační systém a datové disky. Řešení je integrované s Azure Key Vault, které vám pomohou řídit a spravovat klíče a tajné kódy šifrování disku v předplatném trezoru klíčů. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů jsou šifrována v klidovém stavu ve vašem úložišti Azure.

### <a name="virtual-networking"></a>Virtuální síť
Virtuální počítače potřebují připojení k síti. Pro podporu tohoto požadavku Azure vyžaduje, aby virtuální počítače byly připojené k virtuální síti Azure. Virtuální síť Azure je logická konstrukce postavená na fyzické síťové struktuře Azure. Každá logická [virtuální síť Azure](../../virtual-network/virtual-networks-overview.md) je izolovaná od všech ostatních virtuálních sítí Azure. Tato izolace pomáhá zajistit, že síťový provoz ve vašich nasazeních není přístupný ostatním zákazníkům Microsoft Azure.

### <a name="patch-updates"></a>Aktualizace oprav
Aktualizace oprav jsou základem pro hledání a řešení potenciálních problémů a zjednodušení procesu správy aktualizací softwaru, a to snížením počtu aktualizací softwaru, které je nutné nasadit ve vašem podniku, a zvýšením schopnosti sledovat dodržování předpisů.

### <a name="security-policy-management-and-reporting"></a>Správa a vykazování zásad zabezpečení
[Security Center](../../security-center/security-center-intro.md) vám pomůže předcházet, zjišťovat a reagovat na hrozby a poskytuje lepší přehled o zabezpečení vašich prostředků Azure a kontrolu nad tím. Poskytuje integrované monitorování zabezpečení a správu zásad v rámci vašich předplatných Azure, pomáhá odhalovat hrozby, které by jinak mohly zůstat bez povšimnutí, a spolupracuje s širokým ekosystémem řešení zabezpečení.

## <a name="identity-and-access-management"></a>Správa identit a přístupu
Zabezpečení systémů, aplikací a dat začíná ovládacími prvky přístupu založenými na identitě. Funkce správy identit a přístupu, které jsou integrovány do obchodních produktů a služeb společnosti Microsoft, pomáhají chránit vaše organizační a osobní údaje před neoprávněným přístupem a zároveň je zpřístupní oprávněným uživatelům kdykoli a kdekoli, kde se nacházejí potřebovat.

### <a name="secure-identity"></a>Zabezpečená identita
Společnost Microsoft používá ke správě identity a přístupu několik postupů a technologií zabezpečení ve svých produktech a službách.

-   [Vícefaktorové ověřování vyžaduje,](https://azure.microsoft.com/services/multi-factor-authentication/) aby uživatelé používali více metod pro přístup, místně i v cloudu. Poskytuje silné ověřování s řadou snadných možností ověření a zároveň poskytuje uživatelům jednoduchý proces přihlášení.

-   [Microsoft Authenticator](https://aka.ms/authenticator) poskytuje uživatelsky přívětivé multifaktorové ověřování, které funguje jak s účty Microsoft Azure Active Directory, tak s účty Microsoft Azure a Microsoft a zahrnuje podporu nositelných zařízení a schválení založených na otiscích prstů.

-   [Vynucení zásad hesel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) zvyšuje zabezpečení tradičních hesel tím, že ukládá požadavky na délku a složitost, vynucené pravidelné střídání a uzamčení účtu po neúspěšných pokusech o ověření.

-   [Ověřování založené na tokenech](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) umožňuje ověřování prostřednictvím služby Azure Active Directory.

-   [Řízení přístupu na základě rolí (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) umožňuje udělit přístup na základě přiřazené role uživatele, což usnadňuje uživatelům pouze množství přístupu, které potřebují k plnění svých pracovních povinností. RBAC můžete přizpůsobit podle obchodního modelu vaší organizace a tolerance rizika.

-   [Integrovaná správa identit (hybridní identita)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) umožňuje udržovat kontrolu nad přístupem uživatelů napříč interními datovými centry a cloudovými platformami a vytvářet identitu jednoho uživatele pro ověřování a autorizaci pro všechny prostředky.

### <a name="secure-apps-and-data"></a>Zabezpečené aplikace a data
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), komplexní cloudové řešení správy identit a přístupu, pomáhá zabezpečit přístup k datům v aplikacích na webu i v cloudu a zjednodušuje správu uživatelů a skupin. Kombinuje základní adresářové služby, rozšířené správy identit, zabezpečení a správy přístupu k aplikacím a usnadňuje vývojářům vytváření správy identit založených na zásadách do svých aplikací. Pokud chcete zvýšit úroveň Azure Active Directory, můžete přidat placené funkce prostřednictvím edic Azure Active Directory Basic, Premium P1 a Premium P2.

| Free / Společné funkce     | Základní funkce    |Prémiové funkce P1 |Prémiové funkce P2 | Spojení služby Azure AD – jenom související funkce windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Objekty adresáře](../../active-directory/active-directory-whatis.md), [Správa uživatelů/skupin (přidání/aktualizace/odstranění)/ Zřizování na základě uživatele, Registrace zařízení](../../active-directory/active-directory-whatis.md), Jednotné [přihlašování (SSO),](../../active-directory/active-directory-whatis.md) [Samoobslužná změna hesla pro uživatele v cloudu](../../active-directory/active-directory-whatis.md), [Připojení (Synchronizační modul, který rozšiřuje místní adresáře do služby Azure Active Directory),](../../active-directory/active-directory-whatis.md) [Zprávy o zabezpečení a používání](../../active-directory/active-directory-whatis.md)       |   [Správa / zřizování přístupu na základě skupiny](../../active-directory/active-directory-whatis.md), [Samoobslužné resetování hesla pro uživatele cloudu](../../active-directory/active-directory-whatis.md), [Firemní branding (přihlašovací stránky / přizpůsobení přístupového panelu),](../../active-directory/active-directory-whatis.md) [Proxy aplikace](../../active-directory/active-directory-whatis.md), [SLA 99.9%](../../active-directory/active-directory-whatis.md) |  [Samoobslužná skupina a aplikace Správa/Samoobslužné aplikace dodatky/ dynamické skupiny](../../active-directory/active-directory-whatis.md), [Samoobslužné resetování/změna/odemknutí pomocí místního odpisu](../../active-directory/active-directory-whatis.md), [vícefaktorové ověřování (cloud a místní (Server MFA)),](../../active-directory/active-directory-whatis.md) [MIM CAL + SERVER MIM](../../active-directory/active-directory-whatis.md), [Zjišťování cloudových aplikací](../../active-directory/active-directory-whatis.md), [connect health](../../active-directory/active-directory-whatis.md), [automatické převrácení hesla pro skupinové účty](../../active-directory/active-directory-whatis.md)|    [Ochrana identity](../../active-directory/identity-protection/overview.md), [Správa privilegovaných identit](../../active-directory/privileged-identity-management/pim-configure.md)|   [Připojení zařízení k Azure AD, desktopové muška, Microsoft Passport pro Azure AD, obnovení nástroje Správce bitlockeru správce](../../active-directory/active-directory-whatis.md), [automatické registrace MDM, samoobslužné obnovení nástroje BitLocker, další místní správci zařízení s Windows 10 prostřednictvím připojení Azure AD](../../active-directory/active-directory-whatis.md)|


- [Cloud App Discovery](../../active-directory/cloudappdiscovery-get-started.md) je prémiová funkce služby Azure Active Directory, která umožňuje identifikovat cloudové aplikace, které používají zaměstnanci ve vaší organizaci.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) je služba zabezpečení, která využívá funkce detekce anomálií služby Azure Active Directory k zajištění konsolidovaného zobrazení detekce rizik a potenciálních chyb zabezpečení, které by mohly ovlivnit identity vaší organizace.

- [Služba Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně bez nutnosti nasazovat řadiče domény. Uživatelé se k těmto virtuálním počítačům přihlašují pomocí svých podnikových přihlašovacích údajů služby Active Directory a mají bezproblémový přístup k prostředkům.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) je vysoce dostupná globální služba správy identit pro aplikace orientované na spotřebitele, kterou lze škálovat na stovky milionů identit a integrovat je na mobilních a webových platformách. Vaši zákazníci se mohou přihlásit ke všem vašim aplikacím prostřednictvím přizpůsobitelných prostředí, která používají stávající účty sociálních médií, nebo můžete vytvořit nová samostatná pověření.

- [Azure Active Directory B2B Collaboration](https://aka.ms/aad-b2b-collaboration) je zabezpečené řešení integrace partnerů, které podporuje vaše vztahy mezi společnostmi tím, že umožňuje partnerům přístup k podnikovým aplikacím a datům selektivně pomocí jejich vlastních identit.

- [Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) umožňuje rozšířit možnosti cloudu na zařízení s Windows 10 pro centralizovanou správu. Umožňuje uživatelům připojit se k podnikovému nebo organizačnímu cloudu prostřednictvím služby Azure Active Directory a zjednodušuje přístup k aplikacím a prostředkům.

- [Azure Active Directory Aplikační proxy poskytuje](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) přihlašování a zabezpečený vzdálený přístup pro webové aplikace hostované místně.

## <a name="next-steps"></a>Další kroky

- Seznamte se se svou [sdílenou odpovědností v cloudu](shared-responsibility.md).

- Zjistěte, jak vám [Azure Security Center](https://azure.microsoft.com/services/security-center/) pomůže předcházet hrozbám, zjišťovat je a reagovat na ně díky zvýšené viditelnosti a kontrole zabezpečení vašich prostředků Azure.
