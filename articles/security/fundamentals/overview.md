---
title: Seznámení se službou Azure Security | Microsoft Docs
description: Přečtěte si tento přehled a zaveďte sami sebe do zabezpečení Azure, jejich různé služby a jak to funguje.
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
ms.openlocfilehash: d00b897f41ab00f0e3c23292eeb5f431bf5c070c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87835931"
---
# <a name="introduction-to-azure-security"></a>Seznámení se zabezpečením Azure
## <a name="overview"></a>Přehled
Víme, že zabezpečení je úloha v cloudu a jak důležité je, že najdete přesné a včasné informace o zabezpečení Azure. Jedním z nejlepších důvodů použití Azure pro vaše aplikace a služby je využití nejrůznějších nástrojů a funkcí zabezpečení. Tyto nástroje a možnosti usnadňují vytváření zabezpečených řešení na zabezpečené platformě Azure. Microsoft Azure poskytuje důvěrnost, integritu a dostupnost zákaznických dat a zároveň umožňuje transparentní zodpovědnost.

Tento článek poskytuje komplexní přehled o zabezpečení, které je k dispozici v Azure.

### <a name="azure-platform"></a>Platforma Azure
Azure je platforma veřejné cloudové služby, která podporuje širokou škálu operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení. Může spouštět kontejnery Linux s integrací Docker. Vytvářejte aplikace pomocí jazyků JavaScript, Python, .NET, PHP, Java a Node.js; Vytvořte back-endy pro zařízení s iOS, Androidem a Windows.

Veřejné cloudové služby Azure podporují stejné technologie jako miliony vývojářů a odborníků na IT, kteří už využívají a důvěřují jim. Když vytváříte nebo migrujete prostředky IT na, poskytovatele veřejné cloudové služby, kterého se spoléháte na schopnosti této organizace chránit vaše aplikace a data službami a ovládacími prvky, které poskytují ke správě zabezpečení cloudových prostředků.

Infrastruktura Azure je navržená z hlediska aplikací pro hostování milionů zákazníků současně a poskytuje důvěryhodný základ, na kterém podniky můžou splňovat požadavky na zabezpečení.

Kromě toho Azure poskytuje řadu konfigurovatelných možností zabezpečení a možnost je ovládat, abyste mohli přizpůsobit zabezpečení, aby splňovaly jedinečné požadavky nasazení vaší organizace. Tento dokument vám pomůže pochopit, jak vám funkce zabezpečení Azure můžou tyto požadavky splnit.

> [!Note]
> Hlavním cílem tohoto dokumentu je ovládací prvky, které jsou na zákaznících, které můžete použít k přizpůsobení a zvýšení zabezpečení aplikací a služeb.
>
> Informace o tom, jak Microsoft zabezpečuje platformu Azure, najdete v tématu [zabezpečení infrastruktury Azure](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Shrnutí možností zabezpečení Azure

### <a name="features-to-secure-the-azure-platform"></a>Funkce pro zabezpečení platformy Azure
Následující funkce jsou možnosti, které můžete zkontrolovat a poskytnout tak jistotu, že je platforma Azure spravovaná zabezpečeným způsobem. Odkazy jsou k dispozici pro další procházení k podrobnostem o tom, jak společnost Microsoft řeší otázky týkající se vztahů důvěryhodnosti zákazníků ve čtyřech oblastech: zabezpečená platforma, ochrana osobních údajů & ovládací prvky, dodržování předpisů a transparentnost.

| [Zabezpečená platforma](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [Ovládací prvky ochrany osobních údajů &](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[Dodržování předpisů](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [Transparentnost](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Cyklus vývoje zabezpečení](https://www.microsoft.com/sdl/), interní audity | [Správa dat ve všech časových intervalech](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [Centrum zabezpečení](https://www.microsoft.com/trustcenter/default.aspx) |[Jak Microsoft zabezpečuje zákaznická data ve službách Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [Povinné školení zabezpečení, kontroly na pozadí](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Řízení na umístění dat](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [Centrum běžných ovládacích prvků](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Jak Microsoft spravuje umístění dat ve službách Azure](https://azuredatacentermap.azurewebsites.net/)|
| [Testování průniku](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [zjišťování neoprávněných vniknutí, DDoS](https://www.microsoft.com/trustcenter/Security/ThreatManagement), [audity & protokolování](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [Poskytnutí přístupu k datům podle vašich podmínek](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Cloud Services kontrolní seznam pro náležité opatrnosti](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Kdo v Microsoftu má přístup k vašim datům za podmínek](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Stav datového centra umění](https://www.microsoft.com/cloud-platform/global-datacenters), fyzické zabezpečení, [zabezpečení sítě](network-overview.md) | [Reakce na vynucování zákonů](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Dodržování předpisů podle služby, umístění & oboru](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Jak Microsoft zabezpečuje zákaznická data ve službách Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [Odpověď na incident zabezpečení](https://aka.ms/SecurityResponsepaper), [sdílená odpovědnost](https://aka.ms/sharedresponsibility) |[Přísné standardy ochrany osobních údajů](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Kontrola certifikace pro služby Azure, centrum průhledností](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>Funkce pro zabezpečení dat a aplikací
V závislosti na modelu cloudové služby je k dispozici proměnlivá odpovědnost za správu zabezpečení aplikace nebo služby. Na platformě Azure jsou dostupné možnosti, které vám pomůžou s plněním těchto odpovědností prostřednictvím integrovaných funkcí a prostřednictvím partnerských řešení, která se dají nasadit do předplatného Azure.

Předdefinované možnosti jsou uspořádány ve šesti funkčních oblastech: operace, aplikace, úložiště, sítě, výpočetní prostředí a identita. Další podrobnosti o funkcích a možnostech, které jsou k dispozici na platformě Azure, jsou v těchto šesti oblastech poskytovány prostřednictvím souhrnných informací.

## <a name="operations"></a>Operace
V této části najdete další informace týkající se klíčových funkcí v bezpečnostních operacích a souhrnné informace o těchto funkcích.

### <a name="security-and-audit-dashboard"></a>Řídicí panel Security and Audit
[Security and Audit řešení](../../security-center/security-center-intro.md) poskytuje ucelený přehled o stav zabezpečení IT vaší organizace pomocí [integrovaných vyhledávacích dotazů](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) pro významné problémy, které vyžadují vaši pozornost. Řídicí panel [Security and Audit](https://technet.microsoft.com/library/mt484091.aspx) je domovská obrazovka pro všechno, co souvisí se zabezpečením v protokolech Azure monitor. Poskytuje podrobný přehled o stavu zabezpečení vašich počítačů. Umožňuje také zobrazit všechny události za posledních 24 hodin, 7 dní nebo za jakýkoli zadaný interval.

Kromě toho můžete nakonfigurovat zabezpečení & dodržování předpisů pro [automatické provádění konkrétních akcí](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) při zjištění konkrétní události.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../../azure-resource-manager/management/deployment-models.md) vám umožní pracovat s prostředky ve vašem řešení jako se skupinou. Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Pro nasazení použijete [šablonu Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) a tato šablona může fungovat v různých prostředích, jako je testování, příprava a produkce. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

Azure Resource Manager nasazení na základě šablon pomáhá vylepšit zabezpečení řešení nasazených v Azure, protože standardní nastavení řízení zabezpečení a je možné je integrovat do standardizovaných nasazení založených na šablonách. Tím se snižuje riziko chyb konfigurace zabezpečení, které mohou probíhat během ručních nasazení.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) je rozšiřitelná služba správy výkonu aplikací (APM) pro webové vývojáře. Pomocí Application Insights můžete monitorovat živé webové aplikace a automaticky zjišťovat anomálie výkonu. Obsahuje výkonné analytické nástroje, které vám pomůžou diagnostikovat problémy a porozumět tomu, co uživatelé skutečně dělají s vašimi aplikacemi. Monitoruje vaši aplikaci pokaždé, když je spuštěná, jak během testování, tak i po jeho publikování nebo nasazení.

Application Insights vytvoří grafy a tabulky, které vám ukáže například dobu, po kterou se dostanete nejvíc uživatelů, jak reagovat na aplikaci a jak dobře je obsluhovaná pomocí všech externích služeb, na kterých závisí.

Pokud dojde k chybám, selháním nebo problémům s výkonem, můžete prohledat data telemetrie podrobněji a diagnostikovat příčinu. A služba vám pošle e-maily, pokud dojde ke změnám v dostupnosti a výkonu vaší aplikace. Vhledem z hlediska aplikace je tak cenný nástroj zabezpečení, protože pomáhá s dostupností v Triad zabezpečení důvěrnosti, integrity a dostupnosti.

### <a name="azure-monitor"></a>Azure Monitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) nabízí vizualizaci, dotaz, směrování, upozorňování, automatické škálování a automatizaci dat jak z infrastruktury Azure ([Protokol aktivit](../../azure-monitor/platform/platform-logs-overview.md)), tak z každého jednotlivého prostředku Azure ([diagnostické protokoly](../../azure-monitor/platform/platform-logs-overview.md)). Pomocí Azure Monitor můžete upozorňovat na události související se zabezpečením, které jsou generovány v protokolech Azure.

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
[Protokoly Azure monitor](https://azure.microsoft.com/documentation/services/log-analytics/) – poskytuje kromě prostředků Azure i řešení pro správu IT pro místní i cloudovou infrastrukturu (například AWS). Data z Azure Monitor můžete směrovat přímo do protokolů Azure Monitor, abyste viděli metriky a protokoly pro celé prostředí na jednom místě.

Protokoly Azure Monitor můžou být užitečným nástrojem v forenzní a jiné analýze zabezpečení, protože nástroj umožňuje rychle prohledávat velké objemy položek souvisejících se zabezpečením s flexibilním přístupem k dotazům. Kromě toho je [možné místní bránu firewall a protokoly proxy exportovat do Azure a zpřístupnit je pro účely analýzy pomocí protokolů Azure monitor.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../../advisor/index.yml) je přizpůsobený cloudový poradce, který vám pomůže optimalizovat nasazení Azure. Analyzuje telemetrii využití a konfiguraci prostředků. Pak doporučí řešení, která pomáhají zlepšit [výkon](../../advisor/advisor-performance-recommendations.md), [zabezpečení](../../advisor/advisor-security-recommendations.md)a [vysokou dostupnost](../../advisor/advisor-high-availability-recommendations.md) vašich prostředků a současně hledají příležitosti ke [snížení celkové útraty Azure](../../advisor/advisor-cost-recommendations.md). Azure Advisor poskytuje doporučení pro zabezpečení, která můžou významně zlepšit celkové stav zabezpečení pro řešení nasazená v Azure. Tato doporučení se vykreslují z analýzy zabezpečení provedené [Azure Security Center.](../../security-center/security-center-intro.md)

### <a name="azure-security-center"></a>Azure Security Center
[Security Center](../../security-center/security-center-intro.md) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně tím, že se zvyšuje přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Kromě toho Security Center pomáhá s operacemi zabezpečení tím, že poskytuje jeden řídicí panel, který obchází výstrahy a doporučení, která se dají okamžitě zpracovávat. Často můžete problémy vyřešit jediným kliknutím v konzole Security Center.
## <a name="applications"></a>Aplikace
V části najdete další informace týkající se klíčových funkcí v zabezpečení aplikací a souhrnné informace o těchto funkcích.

### <a name="web-application-vulnerability-scanning"></a>Kontrola ohrožení zabezpečení webové aplikace
Jedním z nejjednodušších způsobů, jak začít s testováním ohrožení zabezpečení ve vaší [aplikaci App Service](../../app-service/overview.md) , je použití [integrace se zabezpečením TINFOIL](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) k provedení kontroly ohrožení zabezpečení na vaší aplikaci jedním kliknutím. Výsledky testů můžete zobrazit v snadno srozumitelné sestavě a Naučte se, jak opravit jednotlivé chyby zabezpečení pomocí podrobných pokynů.

### <a name="penetration-testing"></a>Testování průniku
Pokud dáváte přednost vlastním testům průniku nebo chcete použít jinou sadu nebo poskytovatele, musíte postupovat podle [procesu schválení testování průniku do Azure](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) a získat předchozí schválení k provedení požadovaných testů průniku.

### <a name="web-application-firewall"></a>Firewall webových aplikací
Firewall webových aplikací (WAF) v [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) pomáhá chránit webové aplikace před běžnými webovými útoky, jako jsou vkládání SQL, útoky skriptování mezi weby a zneužití relace. Obsahuje předem nakonfigurovanou ochranu před hrozbami, které identifikoval [projekt OWASP (Open Web Application Security) jako prvních 10 běžných ohrožení zabezpečení](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Ověřování a autorizace v prostředí Azure App Service
[App Service ověřování/autorizace](../../app-service/overview-authentication-authorization.md) je funkce, která poskytuje způsob, jakým se aplikace přihlásí uživatelům, aby nemuseli měnit kód back-endu aplikace. Poskytuje snadný způsob ochrany vaší aplikace a práci s daty jednotlivých uživatelů.

### <a name="layered-security-architecture"></a>Vícevrstvá Architektura zabezpečení
Vzhledem k tomu, že [prostředí App Service](../../app-service/environment/app-service-app-service-environment-intro.md) poskytují izolované běhové prostředí nasazené do [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md), můžou vývojáři vytvořit vrstvenou architekturu zabezpečení, která poskytuje různé úrovně přístupu k síti pro jednotlivé aplikační vrstvy. Běžným přáním je skrýt back-endy rozhraní API z obecného přístupu k Internetu a jenom v případě, že jsou rozhraní API zavolána pomocí nadřazeného webového aplikace. [Skupiny zabezpečení sítě (skupin zabezpečení sítě)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) se dají použít v podsítích Azure Virtual Network, které obsahují App Service prostředí k omezení veřejného přístupu k aplikacím API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostika webového serveru a Application Diagnostics
App Service webové aplikace poskytují diagnostické funkce pro protokolování informací z webového serveru i z webové aplikace. Tyto jsou logicky rozděleny na [diagnostiku webových serverů](../../app-service/troubleshoot-diagnostic-logs.md) a [Application Diagnostics](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Webový server obsahuje dva hlavní pokrok při diagnostice a řešení potíží s weby a aplikacemi.

První nová funkce je informace o stavu v reálném čase týkající se fondů aplikací, pracovních procesů, webů, domén aplikací a spuštěných požadavků. Druhými novými výhodami jsou podrobné události trasování, které sledují požadavek v rámci kompletního procesu požadavků a odpovědí.

Aby bylo možné povolit shromažďování těchto trasovacích událostí, může být služba IIS 7 nakonfigurována tak, aby automaticky zachytává úplné protokoly trasování ve formátu XML pro všechny konkrétní požadavky na základě uplynulého času nebo kódů chybové odpovědi.

#### <a name="web-server-diagnostics"></a>Diagnostika webového serveru
Můžete povolit nebo zakázat následující typy protokolů:

-   Podrobné protokolování chyb – podrobné informace o chybě pro stavové kódy HTTP indikující selhání (Stavový kód 400 nebo vyšší). Může to obsahovat informace, které vám pomůžou určit, proč Server vrátil kód chyby.

-   Trasování chybných požadavků – podrobné informace o neúspěšných žádostech, včetně trasování komponent služby IIS použitých ke zpracování žádosti a doby trvání jednotlivých komponent. To může být užitečné, pokud se pokoušíte zvýšit výkon webu nebo izolovat, co způsobuje, že se může vrátit konkrétní chyba HTTP.

-   Protokolování webového serveru – informace o transakcích HTTP pomocí formátu souboru protokolu W3C Extended. To je užitečné při určování celkové metriky lokality, jako je počet zpracovaných požadavků nebo kolik požadavků pochází z konkrétní IP adresy.

#### <a name="application-diagnostics"></a>Application Diagnostics
[Application Diagnostics](../../app-service/troubleshoot-diagnostic-logs.md) umožňuje zachytit informace vytvářené webovou aplikací. ASP.NET aplikace mohou použít třídu [System. Diagnostics. Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) k protokolování informací do protokolu nástroje Application Diagnostics. V konzoli Application Diagnostics se zobrazují dva hlavní typy událostí. Jedná se o události související s výkonem aplikací a události související se selháním a chybami aplikací. Selhání a chyby lze dále rozdělit na problémy s připojením, zabezpečením a selháním. Problémy se selháním obvykle souvisejí s kódem aplikace.

V konzoli Application Diagnostics lze zobrazit události seskupené následujícími způsoby:

-   Vše (Zobrazí se všechny události.)
-   Chyby aplikace (Zobrazí se události související s výjimkami.)
-   Výkon (Zobrazí se události související s výkonem.)

## <a name="storage"></a>Storage
V části najdete další informace týkající se klíčových funkcí zabezpečení Azure Storage a souhrnné informace o těchto funkcích.

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Svůj účet úložiště můžete zabezpečit pomocí Role-Based Access Control (RBAC). Omezení přístupu na základě [potřebných znalostí](https://en.wikipedia.org/wiki/Need_to_know) a [minimálních](https://en.wikipedia.org/wiki/Principle_of_least_privilege) principů zabezpečení oprávnění je nezbytné pro organizace, které chtějí vynutilit zásady zabezpečení pro přístup k datům. Tato přístupová práva jsou udělována přiřazením příslušné role Azure do skupin a aplikací v určitém oboru. K přiřazení oprávnění uživatelům můžete použít [předdefinované role Azure](../../role-based-access-control/built-in-roles.md), jako je Přispěvatel účtu úložiště. Přístup k klíčům úložiště pro účet úložiště pomocí modelu [Azure Resource Manager](../../storage/blobs/security-recommendations.md) lze ovládat pomocí Role-Based Access Control (RBAC).

### <a name="shared-access-signature"></a>Sdílený přístupový podpis
[Sdílený přístupový podpis (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. SAS znamená, že můžete klientovi udělit omezená oprávnění k objektům ve vašem účtu úložiště po určitou dobu a se zadanou sadou oprávnění. Tato omezená oprávnění můžete udělit bez nutnosti sdílení přístupových klíčů k účtu.

### <a name="encryption-in-transit"></a>Šifrování při přenosu
Šifrování při přenosu je mechanismus ochrany dat při přenosu mezi sítěmi. Pomocí Azure Storage můžete zabezpečit data pomocí:
-   [Šifrování na úrovni přenosu](../../storage/blobs/security-recommendations.md), jako je například https při přenosu dat do nebo z Azure Storage.

-   [Šifrování kabelů](../../storage/blobs/security-recommendations.md), jako je například [šifrování SMB 3,0](../../storage/blobs/security-recommendations.md) pro [sdílené složky Azure](../../storage/files/storage-dotnet-how-to-use-files.md).

-   Šifrování na straně klienta, aby se data zašifroval před přenosem do úložiště a dešifrují data poté, co se přenesou z úložiště.

### <a name="encryption-at-rest"></a>Šifrování neaktivních uložených dat
V případě mnoha organizací je šifrování dat v klidovém případě povinný krok k ochraně dat, dodržování předpisů a suverenity dat. Existují tři funkce zabezpečení služby Azure Storage, které poskytují šifrování dat v klidovém znění:

-   [Šifrování služby Storage](../../storage/common/storage-service-encryption.md) umožňuje požádat, aby služba úložiště při zápisu do Azure Storage automaticky zašifroval data.

-   [Šifrování na straně klienta](../../storage/common/storage-client-side-encryption.md) také poskytuje funkci šifrování v klidovém umístění.

-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) slouží k šifrování disků s operačním systémem a datových disků, které používá virtuální počítač s IaaS.

### <a name="storage-analytics"></a>Storage Analytics
[Analýza úložiště Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) provádí protokolování a poskytuje data metrik pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů využití a diagnostikování problémů s účtem úložiště. Analýza úložiště protokoluje podrobné informace o úspěšných a neúspěšných požadavcích na službu úložiště. Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí. Protokolují se následující typy ověřovaných požadavků:
-   Úspěšné požadavky.

-   Neúspěšné požadavky, včetně časového limitu, omezování, sítě, autorizace a dalších chyb.

-   Požadavky využívající sdílený přístupový podpis (SAS), včetně neúspěšných a úspěšných požadavků.

-   Požadavky na analytické údaje.

### <a name="enabling-browser-based-clients-using-cors"></a>Povolení Browser-Based klientů pomocí CORS
[Sdílení prostředků mezi zdroji (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) je mechanismus, který umožňuje doménám udělit každé další oprávnění pro přístup k prostředkům ostatních zdrojů. Uživatelský agent odesílá další hlavičky, aby bylo zajištěno, že kód jazyka JavaScript načtený z konkrétní domény má povolen přístup k prostředkům umístěným v jiné doméně. Druhá doména pak odpoví s dalšími hlavičkami, které umožňují nebo odmítají původní doménu přístup k jejím prostředkům.

Služba Azure Storage teď podporuje CORS, takže až nakonfigurujete pravidla CORS pro službu, vyhodnotí se správně ověřený požadavek na službu z jiné domény, aby se zjistilo, jestli je povolená podle pravidel, která jste zadali.

## <a name="networking"></a>Sítě
V části najdete další informace týkající se klíčových funkcí v zabezpečení sítě Azure a souhrnné informace o těchto funkcích.

### <a name="network-layer-controls"></a>Ovládací prvky síťové vrstvy
Řízení přístupu k síti je ta, která omezuje připojení k určitým zařízením nebo podsítím a představuje základní zabezpečení sítě. Cílem řízení přístupu k síti je zajistit, aby vaše virtuální počítače a služby byly dostupné jenom pro uživatele a zařízení, ke kterým chcete získat přístup.

#### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
[Skupina zabezpečení sítě (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) je základní bránou firewall pro filtrování paketů a umožňuje řídit přístup na základě [5 řazených kolekcí členů](https://www.techopedia.com/definition/28190/5-tuple). Skupin zabezpečení sítě neposkytuje kontrolu aplikační vrstvy ani ověřování řízení přístupu. Dají se použít k řízení přesunu provozu mezi podsítěmi v rámci Azure Virtual Network a přenosem mezi Virtual Network Azure a internetem.

#### <a name="route-control-and-forced-tunneling"></a>Řízení směrování a vynucené tunelování
Možnost řídit chování směrování v Azure Virtual Networks je kritická funkce zabezpečení sítě a řízení přístupu. Pokud třeba chcete zajistit, aby veškerý provoz do a ze služby Azure Virtual Network procházel prostřednictvím tohoto zařízení pro virtuální zabezpečení, musíte mít přístup k řízení a přizpůsobení chování směrování. To můžete provést konfigurací User-Defined tras v Azure.

[Trasy definované uživatelem](../../virtual-network/virtual-networks-udr-overview.md) umožňují přizpůsobit příchozí a odchozí cesty pro provoz, který se přesouvá do a z jednotlivých virtuálních počítačů nebo podsítí, aby se zajistila nejvyšší možná úroveň zabezpečení trasy. [Vynucené tunelování](https://www.petri.com/azure-forced-tunneling) je mechanismus, který můžete použít k zajištění toho, aby vaše služby neumožňovaly iniciovat připojení k zařízením v Internetu.

To se liší od možnosti přijímat příchozí připojení a reagovat na ně. Webové servery front-end musí odpovídat na požadavky od hostitelů v Internetu, takže internetový provoz se povoluje na tyto webové servery příchozí a webové servery můžou reagovat.

Vynucené tunelování se běžně používá k vynucení odchozího provozu na internetu za účelem přechodu na místní proxy servery zabezpečení a brány firewall.

#### <a name="virtual-network-security-appliances"></a>Zařízení Virtual Network zabezpečení
Skupiny zabezpečení sítě, User-Defined trasy a vynucené tunelování poskytují úroveň zabezpečení v síťové a transportní vrstvě [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), můžou nastat situace, kdy budete chtít povolit zabezpečení na vyšších úrovních zásobníku. K těmto rozšířeným funkcím zabezpečení sítě máte přístup pomocí řešení zařízení zabezpečení partnerské sítě Azure. Nejaktuálnější řešení zabezpečení pro Azure Partner Network najdete na [Azure Marketplace](https://azure.microsoft.com/marketplace/) a vyhledáte "zabezpečení" a "zabezpečení sítě".

### <a name="azure-virtual-network"></a>Azure Virtual Network
Virtuální síť Azure je reprezentace vaší vlastní sítě v cloudu. Jedná se o logickou izolaci prostředků infrastruktury sítě Azure vyhrazených pro vaše předplatné. V rámci této sítě máte plnou kontrolu nad bloky IP adres, nastavením DNS, zásadami zabezpečení a směrovacími tabulkami. Virtuální síť můžete segmentovat do podsítí a umísťovat virtuální počítače Azure IaaS a/nebo [cloudové služby (instance rolí PaaS)](../../cloud-services/cloud-services-choose-me.md) na virtuální sítě Azure.

Virtuální síť můžete navíc připojit k místní síti pomocí jedné z [možností připojení](../../vpn-gateway/index.yml) dostupných v Azure. V podstatě můžete svoji síť rozšířit do Azure s úplnou kontrolou nad bloky IP adres a s výhodou poskytovatelů Azure celopodnikového rozsahu.

Síť Azure podporuje různé scénáře zabezpečeného vzdáleného přístupu. Zde jsou některá z vylepšení:

-   [Připojení jednotlivých pracovních stanic k Azure Virtual Network](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Připojení místní sítě k Virtual Network Azure pomocí sítě VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [Připojení místní sítě k Virtual Network Azure pomocí vyhrazeného propojení WAN](../../expressroute/expressroute-introduction.md)

-   [Vzájemná propojení virtuálních sítí Azure](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN Gateway
Pokud chcete odesílat síťový provoz mezi Virtual Network Azure a vaší místní lokalitou, musíte pro Azure Virtual Network vytvořit bránu VPN. [Brána sítě VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) je typem brány virtuální sítě, která odesílá šifrovaný provoz přes veřejné připojení. Brány VPN můžete použít také k posílání provozu mezi virtuálními sítěmi Azure přes síťové prostředky infrastruktury Azure.

### <a name="express-route"></a>Express Route
Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) je vyhrazené propojení WAN, které umožňuje rozšiřování místních sítí do cloudu Microsoftu přes vyhrazené soukromé připojení, které usnadňuje poskytovatel připojení.

![Express Route](./media/overview/azure-security-fig1.png)

Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako jsou Microsoft Azure, Microsoft 365 a CRM Online. Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), síť Ethernet typu point-to-point nebo virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění.

Připojení ExpressRoute nevyužívají veřejný Internet, a proto je lze považovat za bezpečnější než řešení založená na síti VPN. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](../../application-gateway/overview.md) poskytuje jako službu [kontroler doručování aplikací (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) a nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaši aplikaci.

![Application Gateway](./media/overview/azure-security-fig2.png)

Umožňuje optimalizovat produktivitu webové farmy tím, že se v Application Gateway ukončí ukončení protokolu TLS náročné na procesor (označované také jako "přesměrování zpracování TLS" nebo "přemostění TLS"). Poskytuje taky další možnosti směrování vrstvy 7, jako je kruhové dotazování příchozích přenosů, spřažení relací na základě souborů cookie, směrování založené na cestách URL a možnost hostování několika webů za jedním Application Gateway. Služba Azure Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7.

Poskytuje převzetí služeb při selhání, směrování výkonu požadavků HTTP mezi různými servery, ať už jsou místní nebo v cloudu.

Aplikace poskytuje mnoho funkcí kontroleru doručování aplikací (ADC), včetně vyrovnávání zatížení protokolu HTTP, spřažení relace na základě souborů cookie, [přesměrování zpracování TLS](../../application-gateway/tutorial-restrict-web-traffic-powershell.md), vlastních sond stavu, podpory více webů a mnoha dalších.

### <a name="web-application-firewall"></a>Firewall webových aplikací
Firewall webových aplikací je funkce služby [Azure Application Gateway](../../application-gateway/overview.md) , která poskytuje ochranu webových aplikací, které používají Aplikační bránu pro standardní funkce řízení doručování aplikací (ADC). Firewall webových aplikací chrání webové aplikace před většinou z 10 nejčastějších webových hrozeb podle OWASP.

![Firewall webových aplikací](./media/overview/azure-security-fig1.png)

-   Ochrana před útoky prostřednictvím injektáže SQL.

-   Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

-   Ochrana před narušením protokolu HTTP.

-   Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

-   Ochrana před roboty, prohledávacími moduly a skenery.

-   Detekce běžných neobvyklých konfigurací aplikací (tj. Apache, IIS atd.)

Centralizovaný firewall webových aplikací, který chrání před webovými útoky, značně zjednodušuje správu zabezpečení a nabízí lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Firewall webových aplikací umožňuje jednoduše převést stávající aplikační brány do služby Application Gateway.

### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) umožňuje řídit distribuci provozu uživatelů pro koncové body služby v různých datových centrech. K koncovým bodům služby podporovaným Traffic Manager patří virtuální počítače Azure, Web Apps a cloudové služby. Službu Traffic Manager můžete použít také s externími koncovými body mimo Azure. Traffic Manager používá službu DNS (Domain Name System) k přímému směrování požadavků klientů na nejvhodnější koncový bod na základě [metody směrování provozu](../../traffic-manager/traffic-manager-routing-methods.md) a stavu koncových bodů.

Traffic Manager poskytuje řadu metod směrování provozu, které vyhovují různým potřebám aplikací, [monitorování](../../traffic-manager/traffic-manager-monitoring.md)stavu koncových bodů a automatickému převzetí služeb při selhání. Služba Traffic Manager je odolná vůči selhání, a to i selhání celé oblasti Azure.

### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) zajišťuje vysokou dostupnost a výkon sítě pro vaše aplikace. Jedná se o nástroj pro vyrovnávání zatížení vrstvy 4 (TCP, UDP), který distribuuje příchozí provoz mezi zdravými instancemi služeb definovaných v sadě s vyrovnáváním zatížení. Azure Load Balancer lze nakonfigurovat na:

-   Vyrovnávání zatížení příchozího internetového provozu do virtuálních počítačů. Tato konfigurace se označuje jako [veřejné vyrovnávání zatížení](../../load-balancer/components.md#frontend-ip-configurations).

-   Vyrovnávání zatížení mezi virtuálními počítači ve virtuální síti, mezi virtuálními počítači v cloudových službách nebo mezi místními počítači a virtuálními počítači ve virtuální síti mezi pracovišti. Tato konfigurace se označuje jako [interní vyrovnávání zatížení](../../load-balancer/components.md#frontend-ip-configurations).

- Přeposlání externího provozu na konkrétní virtuální počítač

### <a name="internal-dns"></a>Interní DNS
Seznam serverů DNS používaných ve virtuální síti můžete spravovat v Portál pro správu nebo v konfiguračním souboru sítě. Zákazník může pro každou virtuální síť přidat až 12 serverů DNS. Při zadávání serverů DNS je důležité ověřit, že seznam serverů DNS zákazníka je ve správném pořadí pro prostředí zákazníka. Seznamy serverů DNS nefungují v kruhovém dotazování. Používají se v pořadí, v jakém jsou specifikovány. Pokud je možné dosáhnout prvního serveru DNS v seznamu, klient použije tento server DNS bez ohledu na to, jestli server DNS funguje správně nebo ne. Pokud chcete změnit pořadí serverů DNS pro virtuální síť zákazníka, odeberte ze seznamu servery DNS a přidejte je zpátky v pořadí, v jakém zákazník požaduje. Služba DNS podporuje aspekty dostupnosti Triad zabezpečení CIA.

### <a name="azure-dns"></a>Azure DNS
[Název domény systému](https://technet.microsoft.com/library/bb629410.aspx)nebo DNS zodpovídá za překlad (nebo překladu) názvu webu nebo služby na jeho IP adresu. [Azure DNS](../../dns/dns-overview.md) je hostitelská služba určená pro domény DNS a zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. Služba DNS podporuje aspekty dostupnosti Triad zabezpečení CIA.

### <a name="azure-monitor-logs-nsgs"></a>Protokoly Azure Monitor skupin zabezpečení sítě
Můžete povolit následující kategorie diagnostického protokolu pro skupin zabezpečení sítě:

-   Událost: obsahuje položky, pro které se pravidla NSG aplikují na virtuální počítače a role instancí založené na adrese MAC. Stav těchto pravidel se shromáždí každých 60 sekund.

-   Čítač pravidel: obsahuje záznamy, kolikrát se každé pravidlo NSG použije pro odepření nebo povolení provozu.

### <a name="security-center"></a>Security Center
[Azure Security Center](../../security-center/security-center-intro.md) průběžně analyzuje stav zabezpečení vašich prostředků Azure pro osvědčené postupy zabezpečení sítě. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří [doporučení](../../security-center/security-center-recommendations.md) , která vás provedou procesem konfigurace potřebných ovládacích prvků k posílení a ochraně vašich prostředků.

## <a name="compute"></a>Compute
V této části najdete další informace týkající se klíčových funkcí v této oblasti a souhrnné informace o těchto funkcích.

### <a name="antimalware--antivirus"></a>Antimalwarový & Antivirus
S Azure IaaS můžete použít antimalwarový software od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro, McAfee a Kaspersky, a chránit tak své virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami. [Microsoft Antimalware](antimalware.md) pro Azure Cloud Services a Virtual Machines je schopnost ochrany, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software. Microsoft Antimalware poskytuje konfigurovatelné výstrahy, když se známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v systémech Azure. Microsoft Antimalware se dá nasadit taky pomocí Azure Security Center

### <a name="hardware-security-module"></a>Modul hardwarového zabezpečení
Šifrování a ověřování nevylepšuje zabezpečení, pokud klíče samotné nejsou chráněné. Můžete zjednodušit správu a zabezpečení důležitých tajných kódů a klíčů jejich uložením do [Azure Key Vault](../../key-vault/general/overview.md). Key Vault poskytuje možnost ukládat klíče v modulech hardwarového zabezpečení (HSM) s certifikací podle standardů FIPS 140-2 úrovně 2. Šifrovací klíče SQL Server pro zálohování nebo [transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx) je možné ukládat do Key Vault pomocí jakýchkoli klíčů a tajných kódů z vašich aplikací. Oprávnění a přístup k těmto chráněným položkám se spravují prostřednictvím [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Záloha virtuálního počítače
[Azure Backup](../../backup/backup-overview.md) je řešení, které chrání data vaší aplikace pomocí nulové kapitálové investice a minimální provozní náklady. Chyby aplikací můžou poškodit vaše data a lidské chyby můžou do vašich aplikací zavádět chyby, které mohou vést k problémům se zabezpečením. Díky Azure Backup jsou virtuální počítače se systémem Windows a Linux chráněny.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Důležitou součástí strategie [pro kontinuitu podnikových procesů a zotavení po havárii (BCDR)](../../best-practices-availability-paired-regions.md) vaší organizace se dozvíte, jak udržovat podnikové úlohy a aplikace v provozu, když dojde k plánovanému a neplánovanému výpadku. [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) pomáhá orchestrovat replikaci, převzetí služeb při selhání a obnovení úloh a aplikací tak, aby byly k dispozici ze sekundárního umístění, pokud dojde k výpadku vašeho primárního umístění.

### <a name="sql-vm-tde"></a>TDE VIRTUÁLNÍHO POČÍTAČE SQL
[Transparentní šifrování dat (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) a šifrování na úrovni sloupce (CLE) jsou funkce šifrování SQL serveru. Tato forma šifrování vyžaduje, aby zákazníci spravovali a ukládali kryptografické klíče, které používáte pro šifrování.

Služba Azure Key Vault (integrace) je navržená tak, aby vylepšila zabezpečení a správu těchto klíčů v zabezpečeném a vysoce dostupném umístění. Konektor SQL Serveru umožňuje SQL Server používat tyto klíče z Azure Key Vault.

Pokud používáte SQL Server s místními počítači, můžete postupovat podle pokynů pro přístup k Azure Key Vault z vaší místní instance SQL Server. Ale pro SQL Server ve virtuálních počítačích Azure můžete ušetřit čas pomocí funkce Integrace Azure Key Vault. Pomocí několika rutin Azure PowerShell pro povolení této funkce můžete automatizovat konfiguraci nutnou k tomu, aby virtuální počítač SQL měl přístup k trezoru klíčů.

### <a name="vm-disk-encryption"></a>Šifrování disku virtuálního počítače
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) je nová funkce, která vám pomůže s šifrováním disků virtuálních počítačů s Windows a Linux IaaS. Používá standardní funkci nástroje BitLocker systému Windows a DM-Crypt funkce systému Linux k poskytování šifrování svazku pro operační systém a datové disky. Řešení je integrované s Azure Key Vault, které vám pomůžou s řízením a správou klíčů a tajných kódů na disku v předplatném Key Vault. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů jsou v klidovém stavu zašifrovaná v úložišti Azure.

### <a name="virtual-networking"></a>Virtuální síť
Virtuální počítače potřebují síťové připojení. Pro podporu tohoto požadavku Azure vyžaduje, aby se virtuální počítače připojovaly k Virtual Network Azure. Azure Virtual Network je logická konstrukce, která je postavená na fyzických prostředcích infrastruktury sítě Azure. Každý logický [Virtual Network Azure](../../virtual-network/virtual-networks-overview.md) je izolovaný od všech ostatních virtuálních sítí Azure. Tato izolace vám pomůže zajistit, že síťový provoz ve vašich nasazeních není přístupný ostatním Microsoft Azurem zákazníkům.

### <a name="patch-updates"></a>Aktualizace oprav
Aktualizace oprav poskytují základ pro vyhledání a opravu potenciálních problémů a zjednodušení procesu správy aktualizací softwaru tím, že omezují počet aktualizací softwaru, které musíte nasadit v podniku, a zvýšíte možnost monitorování dodržování předpisů.

### <a name="security-policy-management-and-reporting"></a>Správa zásad zabezpečení a vytváření sestav
[Security Center](../../security-center/security-center-intro.md) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně a poskytuje lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované monitorování zabezpečení a správu zásad napříč předplatnými Azure, pomáhá detekovat hrozby, které by jinak neinformovaly, a spolupracuje s širokou ekosystémem řešení zabezpečení.

## <a name="identity-and-access-management"></a>Správa identit a přístupu
Zabezpečení systémů, aplikací a dat začíná pomocí řízení přístupu na základě identity. Funkce pro správu identit a přístupu, které jsou integrované v obchodních produktech a službách Microsoftu, můžou chránit vaše organizační a osobní údaje před neoprávněným přístupem a zároveň je zpřístupnit oprávněným uživatelům, kdykoli a kdekoli ho potřebují.

### <a name="secure-identity"></a>Zabezpečená identita
Microsoft používá pro správu identit a přístupu více postupů a technologií zabezpečení napříč svými produkty a službami.

-   [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) vyžaduje, aby uživatelé používali více metod pro přístup, místně i v cloudu. Poskytuje silné ověřování s využitím široké škály možností snadného ověření a současně přihlašuje uživatele k jednoduchému procesu přihlašování.

-   [Microsoft Authenticator](https://aka.ms/authenticator) poskytuje uživatelsky přívětivé Multi-Factor Authentication prostředí, které spolupracuje s účty Microsoft Azure Active Directory i Microsoft a zahrnuje podporu pro schvalování wearables a na základě otisků prstů.

-   [Vynucování zásad hesel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) zvyšuje zabezpečení tradičních hesel tím, že ukládá požadavky na délku a složitost, vynucené pravidelné otočení a uzamčení účtu po neúspěšných pokusech o ověření.

-   [Ověřování na základě tokenu](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) umožňuje ověřování prostřednictvím Azure Active Directory.

-   [Řízení přístupu na základě role v Azure (Azure RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) umožňuje udělit přístup na základě přiřazené role uživatele, což usnadňuje uživatelům poskytnout jenom množství přístupu, které potřebují k provedení svých pracovních povinností. RBAC můžete přizpůsobit podle obchodního modelu a tolerance rizik vaší organizace.

-   [Integration Management (hybridní identita)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) umožňuje udržovat řízení přístupu uživatelů mezi interní datacentry a cloudové platformy a vytvořit jednu identitu uživatele pro ověřování a autorizaci pro všechny prostředky.

### <a name="secure-apps-and-data"></a>Zabezpečení aplikací a dat
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)komplexní cloudové řešení pro správu identit a přístupu pomáhá zabezpečit přístup k datům v aplikacích v lokalitě a v cloudu a zjednodušuje správu uživatelů a skupin. Kombinuje základní adresářové služby, pokročilé řízení identit, zabezpečení a správu přístupu k aplikacím a usnadňuje vývojářům vytvářet správu identit založenou na zásadách ve svých aplikacích. Pokud chcete zvýšit úroveň Azure Active Directory, můžete přidat placené funkce prostřednictvím edic Azure Active Directory Basic, Premium P1 a Premium P2.

| Bezplatné a běžné funkce     | Základní funkce    |Funkce Premium P1 |Funkce Premium P2 | Připojení Azure Active Directory – jenom Windows 10 – související funkce|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Adresářové objekty](../../active-directory/active-directory-whatis.md), [Správa uživatelů a skupin (přidání/aktualizace/odstranění)/zřizování na základě uživatelů, registrace zařízení](../../active-directory/active-directory-whatis.md),   [jednotné Sign-On (SSO)](../../active-directory/active-directory-whatis.md),  [samoobslužná Změna hesla pro cloudové uživatele](../../active-directory/active-directory-whatis.md),  [připojení (synchronizační modul, který rozšiřuje místní adresáře do Azure Active Directory)](../../active-directory/active-directory-whatis.md),  [sestavy zabezpečení/využití](../../active-directory/active-directory-whatis.md)       |   [Správa/zřizování přístupu na základě skupin](../../active-directory/active-directory-whatis.md),  [Samoobslužné resetování hesla pro uživatele v cloudu](../../active-directory/active-directory-whatis.md),   [Branding společnosti (přihlašovací stránky/vlastní nastavení přístupového panelu)](../../active-directory/active-directory-whatis.md), [proxy aplikací](../../active-directory/active-directory-whatis.md), [SLA 99,9%](../../active-directory/active-directory-whatis.md) |  [Samoobslužná správa skupin a aplikací/samoobslužné přidávání aplikací/dynamické skupiny](../../active-directory/active-directory-whatis.md), [Samoobslužné resetování hesla, změny/odemknutí místní služby back](../../active-directory/active-directory-whatis.md)-Store, [Multi-Factor Authentication (cloudové a místní (MFA Server))](../../active-directory/active-directory-whatis.md),  [MIM CAL + MIM Server](../../active-directory/active-directory-whatis.md),  [Cloud App Discovery](../../active-directory/active-directory-whatis.md),   [Connect Health](../../active-directory/active-directory-whatis.md),    [Automatická změna hesla pro skupinové účty](../../active-directory/active-directory-whatis.md)|    [Identity Protection](../../active-directory/identity-protection/overview.md),  [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Připojení zařízení ke službě Azure AD, Desktop SSO, Microsoft Passport pro Azure AD, obnovení nástroje BitLocker pro správu](../../active-directory/active-directory-whatis.md)mobilních zařízení, [automatické registraci MDM, Self-Service obnovení BitLockeru, dalším místním správcům na zařízení s Windows 10 prostřednictvím služby Azure AD JOIN](../../active-directory/active-directory-whatis.md)|


- [Cloud App Discovery](../../active-directory/cloudappdiscovery-get-started.md) je funkce Premium Azure Active Directory, která umožňuje identifikovat cloudové aplikace používané zaměstnanci ve vaší organizaci.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) je služba zabezpečení, která využívá Azure Active Directory možnosti detekce anomálií k zajištění konsolidovaného zobrazení pro detekci rizik a potenciální ohrožení zabezpečení, která by mohla ovlivnit identity vaší organizace.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně, aniž by bylo nutné nasazovat řadiče domény. Uživatelé se k těmto virtuálním počítačům přihlásí pomocí svých podnikových přihlašovacích údajů pro Active Directory a můžou bezproblémově přistupovat k prostředkům.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) je vysoce dostupná a globální služba pro správu identit pro aplikace zaměřené na uživatele, která umožňuje škálování na stovky milionů identit a integraci napříč mobilními a webovými platformami. Vaši zákazníci se můžou ke všem vašim aplikacím přihlašovat prostřednictvím přizpůsobitelného prostředí, které používá stávající účty sociálních médií, nebo můžete vytvořit nové samostatné přihlašovací údaje.

- [Azure Active Directory spolupráce B2B](https://aka.ms/aad-b2b-collaboration) je zabezpečené integrační řešení, které podporuje vztahy mezi společnostmi tím, že umožňuje partnerům přístup k podnikovým aplikacím a datům selektivně pomocí svých identit spravovaných svým držitelem.

- [Azure Active Directory JOIN](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) umožňuje rozšiřování cloudových možností do zařízení s Windows 10 pro centralizovanou správu. Umožňuje uživatelům připojit se k podnikovému nebo organizačnímu cloudu prostřednictvím Azure Active Directory a zjednodušit přístup k aplikacím a prostředkům.

- [Proxy aplikací služby Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) poskytuje jednotné přihlašování a zabezpečený vzdálený přístup k webovým aplikacím hostovaným místně.

## <a name="next-steps"></a>Další kroky

- Pochopte svou [sdílenou odpovědnost v cloudu](shared-responsibility.md).

- Přečtěte si, jak [Azure Security Center](https://azure.microsoft.com/services/security-center/) vám pomůžou zabránit hrozbám, detekovat je a reagovat na ně se zvýšenou viditelností a kontrolou zabezpečení vašich prostředků Azure.
