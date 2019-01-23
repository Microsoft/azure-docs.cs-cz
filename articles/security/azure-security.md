---
title: Úvod do zabezpečení Azure | Dokumentace Microsoftu
description: Další informace o zabezpečení Azure, služeb a jak to funguje.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 90c4ca4a0ac083909214f3d9a1b3b8987b9dc2ad
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473482"
---
# <a name="introduction-to-azure-security"></a>Úvod do zabezpečení Azure
## <a name="overview"></a>Přehled
Víme, že je zabezpečení stavbách v cloudu a jak důležité je, že zjistíte přesné a aktuální informace o zabezpečení Azure. Jedním z nejlepších důvody pro použití Azure pro vaše aplikace a služby je využít jeho široké škály nástroje zabezpečení a možnosti. Tyto nástroje a možnosti nápovědy umožňují vytvářet bezpečná řešení na zabezpečené platformě Azure. Microsoft Azure poskytuje důvěrnost, integritu a dostupnost zákaznických dat, a zároveň umožnit transparentní zodpovědnost.

Vám pomůžou lépe pochopit kolekci zabezpečení je zapsán ovládací prvky, které jsou implementovány v rámci Microsoft Azure z perspektivy odběratele a Microsoft operations, tento dokument white paper, "Úvod do zabezpečení Azure", k poskytování a získejte ucelený na zabezpečení, které jsou k dispozici s Microsoft Azure.

### <a name="azure-platform"></a>Azure Platform
Azure je platforma služby veřejného cloudu, který podporuje širokou škálu operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení. Může probíhat kontejnery Linuxu s integrací Dockeru; Vytvářejte aplikace pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js, Tvořte back EndY pro iOS, Android a Windows zařízení.

Veřejnými cloudovými službami Azure podporují stejné technologie miliony vývojářů a IT profesionály už spoléhají a kterým důvěřují. Při sestavení nebo migraci prostředků IT k, poskytovatele služeb veřejného cloudu jste závislí na schopnosti této organizace chránit vaše aplikace a data se službami a ovládací prvky poskytují ke správě zabezpečení vašich cloudových prostředků.

Infrastruktura Azure je navržená ze zařízení do aplikace pro hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro na kterém můžou podniky vyhovuje jejich požadavkům na zabezpečení.

Azure navíc poskytuje širokou škálu možností konfigurovatelné zabezpečení a umožňuje jim tak, aby zabezpečení jedinečným požadavkům vaší organizace nasazení můžete přizpůsobit. Tento dokument vám pomůže pochopit Azure zabezpečení možnosti můžete splnit tyto požadavky.

> [!Note]
> Hlavním cílem tohoto dokumentu je v ovládacích prvcích určených pro zákazníky, které můžete použít k přizpůsobení a zvýšení zabezpečení vašich aplikací a služeb.
>
> Jsme poskytují některé základní informace, ale podrobné informace o tom, jak společnost Microsoft zabezpečila samotné platformy Azure najdete v tématu informace uvedené v [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Abstraktní
Migrace do veřejného cloudu se zpočátku využitím úspory nákladů a flexibility na inovace. Zabezpečení byla považována za hlavním zájmem zajistit u nějakou dobu a dokonce i závažný, pro migraci do veřejného cloudu. Veřejné cloudové zabezpečení má však přešla ze závažný problém do jedné z ovladače pro migraci do cloudu. Odůvodnění to je vynikající schopnost poskytovatele služeb velkých veřejného cloudu k ochraně aplikací a dat z cloudových prostředků.

Infrastruktura Azure je od zařízení až po aplikace navržena tak, aby umožňovala hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro splnění potřeb zabezpečení jednotlivých podniků. Kromě toho Azure poskytuje širokou škálu možností konfigurovatelné zabezpečení a umožňuje jim tak, aby si přizpůsobit zabezpečení jedinečným požadavkům vašich nasazení pro splnění vašich IT zásad řízení a dodržovat externí předpisy.

Tento dokument popisuje přístup Microsoftu k zabezpečení v rámci cloudové platformě Microsoft Azure:
* Funkce zabezpečení implementované Microsoftu pro zabezpečení infrastruktury Azure, zákaznická data a aplikace.
* Služby Azure a funkce zabezpečení, které jsou k dispozici ke správě zabezpečení služeb a dat v rámci vašich předplatných Azure.

## <a name="summary-azure-security-capabilities"></a>Možnosti souhrnu zabezpečení Azure
Následující tabulka poskytuje stručný popis funkcí zabezpečení, které jsou implementované Microsoftu pro zabezpečení infrastruktury Azure, zákaznická data a zabezpečených aplikací.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Zabezpečení funkce implementována pro zabezpečení platformy Azure:
Funkce uvedené následující jsou funkce, které můžete zkontrolovat na poskytuje záruku, že je na platformě Azure spravovaná bezpečným způsobem. Odkazy byly zadány pro další procházení podrobností podle jak Microsoft řeší otázky důvěryhodnosti zákazníka v čtyři oblasti: Zabezpečení platformy, ochrany osobních údajů a ovládací prvky, dodržování předpisů a transparentnost.


| [Zabezpečená platforma](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Ochrana osobních údajů a ovládací prvky](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Dodržování předpisů](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Transparentnost](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Zabezpečení vývojový cyklus](https://www.microsoft.com/en-us/sdl/)interní auditování | [Umožňuje spravovat vaše data neustále](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Centrum zabezpečení](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Jak společnost Microsoft chrání vaše zákaznická data ve službách Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Povinné školení o zabezpečení, kontroly na pozadí](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Ovládací prvek na umístění dat](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Běžné ovládací prvky centra](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Jak Microsoft spravuje umístění dat ve službách Azure](http://azuredatacentermap.azurewebsites.net/)|
| [Testování průniku](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [zjišťování neoprávněných vniknutí, před útoky DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [auditování a protokolování](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Poskytuje přístup k datům podle vašich představ](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Cloud Services z důvodu co Nejopatrněji kontrolní seznam](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Kdo v Microsoftu přístup k vašim datům na jaké podmínky](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Nejmodernější optimalizaci pspo datového centra](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), fyzické zabezpečení, [zabezpečení sítě](https://docs.microsoft.com/azure/security/security-network-overview) | [Reagovat orgánům zajišťujícím vymáhání zákona](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Dodržování předpisů u služeb, umístění a průmysl](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Jak společnost Microsoft chrání vaše zákaznická data ve službách Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Reakce na incidenty zabezpečení](https://aka.ms/SecurityResponsepaper), [sdílených odpovědností](https://aka.ms/sharedresponsibility) |[Standardy přísné zásady ochrany osobních údajů](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Projděte si certifikaci pro služby Azure, Centrum transparentnosti](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Funkce zabezpečení, které nabízí Azure k zabezpečení dat a aplikací
V závislosti na modelu cloudové služby je proměnná odpovědnost za kdo zodpovídá za správu zabezpečení aplikace nebo služby. Možnosti nejsou k dispozici na platformě Azure, abychom vám v souladu s těmito úkoly prostřednictvím integrované funkce a prostřednictvím partnerských řešení, které je možné nasadit do předplatného Azure.

Integrované funkce jsou uspořádány do šest (6) funkčních oblastí: Operace, aplikací, úložiště, sítě, výpočetní prostředky a Identity. Další podrobnosti o funkcích a možnostem v platformě Azure v těchto oblastech šest (6), jsou poskytované prostřednictvím souhrnné informace.

## <a name="operations"></a>Operace
Tato část obsahuje další informace o klíčových funkcí v oddělení zabezpečení a souhrnné informace o těchto funkcích.

### <a name="security-and-audit-dashboard"></a>Zabezpečení a Audit řídicí panel
[Řešení zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) poskytuje komplexní pohled na vaše organizace IT stavu zabezpečení s využitím [předdefinovaných vyhledávacích dotazů](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) na důležité problémy, které vyžadují vaši pozornost. [Zabezpečení a Audit](https://technet.microsoft.com/library/mt484091.aspx) řídicí panel je domovskou obrazovkou pro všechno, co související se zabezpečením v Log Analytics. Poskytuje celkový přehled o stavu zabezpečení vašich počítačů. Umožňuje také zobrazit všechny události za posledních 24 hodin, 7 dní nebo za jakýkoli zadaný interval.

Kromě toho můžete nakonfigurovat zabezpečení a dodržování předpisů pro [automaticky provádět konkrétní akce](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) při zjištění určité události.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manageru ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) umožňuje pracovat s prostředky ve vašem řešení jako se skupinou. Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Můžete použít [šablony Azure Resource Manageru](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) pro nasazení a tato šablona může fungovat v různých prostředích, jako je například testovacím, přípravném nebo produkčním prostředí. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

Nasazení pomocí šablony Azure Resource Manageru vylepšení zabezpečení řešení nasazené v Azure, protože standard zabezpečení řídit nastavení a můžete integrovat do standardizované nasazení založené na šabloně. To snižuje riziko chyby konfigurace zabezpečení, které může probíhat během ruční nasazení.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) je rozšiřitelná služba správu výkonu aplikací (APM) pro webové vývojáře. Pomocí nástroje Application Insights můžete sledovat živé webové aplikace a automaticky zjišťuje anomálie ve výkonu. Obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé dělají s vašimi aplikacemi. Monitoruje vaše aplikace neustále, na kterých je spuštěný při testování a po publikování nebo ho nasadil.

Application Insights vytvoří grafů a tabulek, které ukazují, například, kterou denní dobu můžete získat většina uživatelů, jak responzivní aplikace je a jak dobře je poskytovaný všech externích služeb, na kterých závisí.

Pokud existují, chyb, chyby nebo problémy s výkonem, můžete prohledat data telemetrie podrobně určování příčin problémů. A služba vám pošle e-mailů Pokud existují změny v dostupnosti a výkonu vaší aplikace. Application Insights tak stane nástroj cenné zabezpečení, protože pomáhá s dostupnost v důvěrnost, integritu a dostupnost chaloupka zabezpečení.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) nabízí vizualizace, dotazování, směrování, výstrahy, automatické škálování a automatizace na obou data z infrastruktury Azure ([protokolu aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) a jednotlivé prostředky Azure ([diagnostiky Protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Azure Monitor můžete výstrahy na události související se zabezpečením, které jsou generovány v protokolů Azure.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) – poskytuje řešení pro správu IT pro místní a třetích stran cloudové infrastruktury (třeba AWS) kromě prostředků Azure. Data ze služby Azure Monitor je možné směrovat přímo ke službě Log Analytics, abyste si mohli zobrazit metriky a protokoly pro celé prostředí na jednom místě.

Log Analytics může být užitečný nástroj v analýze forenzní a další bezpečnostní nástroj umožňuje rychle prohledávat velké množství souvisejících se zabezpečením položky s přístupem, flexibilní dotazování. Kromě toho místní [protokoly brány firewall a proxy serveru můžete exportovat do Azure a k dispozici pro analýzu pomocí Log Analytics.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](https://docs.microsoft.com/azure/advisor/) je individuální cloudový konzultant, který vám umožní optimalizovat nasazení Azure. Analyzuje telemetrii využití a konfiguraci prostředků. Doporučuje řešení, které jí pomohou zlepšit [výkonu](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [zabezpečení](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), a [vysoké dostupnosti](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) prostředků při hledání příležitostí k [snížit celkový Azure věnovat](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Azure Advisor poskytuje doporučení zabezpečení. to může výrazně zlepšit vaše celkové postavení zabezpečení pro řešení, která můžete nasadit v Azure. Tato doporučení jsou vykreslovány pomocí provádí analýzy zabezpečení [Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Kromě toho Azure Security Center pomáhá s operací zabezpečení tím, že poskytuje jeden řídicí panel, které zařízení Surface upozornění a doporučení, která může má akce provést okamžitě. Často můžete opravit problémy s jedním kliknutím v konzole Azure Security Center.
## <a name="applications"></a>Aplikace
V části poskytuje další informace o klíčových funkcí v aplikaci zabezpečení a souhrnné informace o těchto funkcích.

### <a name="web-application-vulnerability-scanning"></a>Webové kontroly ohrožení zabezpečení aplikace
Jeden z nejjednodušších způsobů, jak začít pracovat s testováním ohrožení zabezpečení na vašich [aplikace služby App Service](https://docs.microsoft.com/azure/app-service/overview) , je použít [integrace s Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) k provedení kontroly ve vaší aplikaci jedním kliknutím ohrožení zabezpečení. Můžete zobrazit výsledky testů ve zprávě o snadno pochopit a zjistěte, jak opravit každý ohrožení zabezpečení s využitím podrobných pokynů.

### <a name="penetration-testing"></a>Testování průniku
Pokud budete chtít provádět testování průniku nebo chcete použít jiný skener suite nebo poskytovatele, je třeba dodržet [proces schválení testování průniku s Azure](https://docs.microsoft.com/azure/security/azure-security-pen-testing ) a získat předchozí schválení k provedení požadovaného průniku testy.

### <a name="web-application-firewall"></a>Firewall webových aplikací
Firewall webových aplikací (WAF) ve [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) pomáhá chránit webové aplikace z common webovými útoky, jako jsou Injektáž SQL, útoky skriptování napříč weby a napadení relace. Obsahuje předem nakonfigurovanou ochranu před hrozbami identifikován [Open Web Application zabezpečení projektu (OWASP) jako top 10 nejběžnějších ohrožení zabezpečení](https://msdn.microsoft.com/library/).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Ověřování a autorizace v prostředí Azure App Service
[App Service ověřování / autorizace](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) je funkce, která poskytuje způsob, jak vaše aplikace k přihlášení uživatelů, takže není nutné měnit kód back-endové aplikace. Poskytuje snadný způsob, jak chránit vaše aplikace a pracovat s daty jednotlivých uživatelů.

### <a name="layered-security-architecture"></a>Vrstvené architektury zabezpečení
Protože [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) poskytuje izolovaném prostředí, nasadí do [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), umožňuje vývojářům vytvářet vrstvené architektury zabezpečení poskytují různé úrovně přístup k síti pro jednotlivé vrstvy aplikace. Běžné tím pádem si je skrýt rozhraní API back EndY z obecné přístup k Internetu a povolit pouze rozhraní API, které jsou volány aplikací nadřazený web apps. [Skupiny zabezpečení sítě (Nsg)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) je možné omezit veřejný přístup k aplikacím API v Azure Virtual Network podsítěmi, které obsahují služby App Service Environment.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostika webového serveru a application diagnostics
Aplikace služby App Service web apps poskytují diagnostické funkce pro protokolování informací z webového serveru a webové aplikace. Tyto jsou logicky rozdělena do [webového serveru diagnostiky](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) a [konzole application diagnostics](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Webový server zahrnuje dva hlavní záloh v diagnostice a řešení potíží s weby a aplikace.

První novou funkcí je informace o fondy aplikací, pracovních procesů, weby, domény aplikace a spuštěné požadavky vyvolané programem stavu v reálném čase. Druhé nové výhody jsou podrobné trasování událostí, které sledují žádost v celém procesu dokončení požadavků a odpovědí.

Povolte shromažďování těchto trasování událostí, služby IIS 7 se dají konfigurovat na automaticky zachytávat protokoly úplné trasování ve formátu XML pro jakékoli konkrétní požadavek na základě uplynulý čas nebo chybové kódy odpovědí.

#### <a name="web-server-diagnostics"></a>Diagnostika webového serveru
Můžete povolit nebo zakázat následující typy protokolů:

-   Podrobné protokolování chyb – podrobné informace o chybě pro stavové kódy HTTP, které indikují chybu (stavový kód 400 nebo vyšší). To může obsahovat informace, které vám pomůže určit, proč server vrátil kód chyby.

-   Trasování neúspěšných žádostí – podrobné informace o neúspěšných požadavcích, včetně trasování součásti služby IIS používá ke zpracování požadavku a doba trvání v jednotlivých komponentách. To může být užitečné, pokud se pokoušíte zvýšit výkon webu a izolovat co způsobuje konkrétní chyba protokolu HTTP, který se má vrátit.

-   Webový Server protokolování – informace o transakce HTTP pomocí rozšířeném formátu protokolu W3C souboru. To je užitečné při určování celkové lokality metriky, jako je počet požadavků zpracovaných nebo kolik žádostí se z konkrétní IP adresu.

#### <a name="application-diagnostics"></a>Diagnostika aplikace
[Konzole Application diagnostics](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) umožňuje zaznamenat informace vytvořené webové aplikace. Můžete použít aplikace ASP.NET [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) třídy k protokolování informací do protokolu diagnostiky aplikace. V konzoli Application Diagnostics existují dva hlavní typy událostí, ta, která souvisí s výkonem aplikací a související se selháním a chybami aplikací. Je možné rozdělit selháním a chybami dalšího problémy s připojením, zabezpečením a selháním. Selhání problémy se většinou vztahují k problému s kódem aplikace.

V konzoli Application Diagnostics lze zobrazit události seskupené následujícími způsoby:

-   Vše (zobrazí se všechny události)
-   Chyby aplikace (zobrazí výjimka události)
-   Výkon (zobrazí se události související s výkonem)

## <a name="storage"></a>Storage
V části poskytuje další informace o klíčových funkcí v Azure storage zabezpečení a souhrnné informace o těchto funkcích.

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Můžete zabezpečit svůj účet úložiště pomocí řízení přístupu na základě Role (RBAC). Omezení přístupu na základě [znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší možná oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) principů zabezpečení je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Tato přístupová práva jsou uděluje přiřazením příslušné role RBAC pro skupiny nebo aplikace v určitém rozsahu. Můžete použít [předdefinované role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles), jako je Přispěvatel účtů úložiště, přiřadit oprávnění pro uživatele. Přístup k úložišti klíčů pro účet úložiště pomocí [Azure Resource Manageru](https://docs.microsoft.com/azure/storage/storage-security-guide) model lze ovládat pomocí řízení přístupu na základě Role (RBAC).

### <a name="shared-access-signature"></a>Sdílený přístupový podpis
[Sdílený přístupový podpis (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. SAS znamená, že můžete udělit že klienta omezená oprávnění k objektům v účtu úložiště během zadaného období a s konkrétní sadou oprávnění. Omezená oprávnění může udělit aniž byste museli sdílet přístupové klíče vašeho účtu.

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Šifrování během přenosu sítí je mechanismus ochrany dat při přenosu napříč sítěmi. S Azure Storage můžete svázat data s využitím:
-   [Šifrování na úrovni přenosu](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), jako je například HTTPS při přenosu dat do nebo z úložiště Azure.

-   [Propojí šifrování](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), jako například [šifrování SMB 3.0](https://docs.microsoft.com/azure/storage/storage-security-guide) pro [sdílených složek Azure](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Šifrování na straně klienta, k šifrování dat před přenosu do služby storage a k dešifrování dat po převedení mimo úložiště.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Šifrování dat v klidovém stavu pro mnoho společností, je povinný krok k zabezpečení, ochrany osobních údajů, dodržování předpisů a suverenitu dat. Existují tři funkce zabezpečení služby Azure storage, které poskytují šifrování dat, která jsou "neaktivní":

-   [Šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption) umožňuje požadovat, že služba úložiště automaticky šifrování dat při zápisu do služby Azure Storage.

-   [Šifrování na straně klienta](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) také poskytuje funkci šifrování v klidovém stavu.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) umožňuje šifrovat disky s operačním systémem a datové disky používané virtuálním počítači s IaaS.

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště. Analýza úložiště protokoluje podrobné informace o úspěšné i neúspěšné požadavky do služby úložiště. Tyto informace slouží ke sledování jednotlivých požadavků a Diagnostikujte problémy pomocí služby storage. Žádosti o přihlášení snažíme maximálně vyhovět. Jsou zaznamenány následující typy ověřených požadavků:
-   Úspěšné požadavky.

-   Neúspěšné požadavky, včetně vypršení časového limitu, omezování, sítě, autorizace a dalších chybách.

-   Požadavky na použití sdílený přístupový podpis (SAS), včetně úspěšné a neúspěšné požadavky.

-   Požadavky na analytics data.

### <a name="enabling-browser-based-clients-using-cors"></a>Povolení založené na prohlížeči klientů pomocí CORS
[Sdílení prostředků mezi zdroji (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) virtuálních sítí je mechanismus, který umožňuje u domén na sebe navzájem udělit oprávnění pro přístup k prostředkům druhé strany. Uživatelský Agent odešle dodatečné hlavičky, aby zajistila, že kód jazyka JavaScript načtené z některé domény může získat přístup k prostředkům v jiné doméně. Druhá možnost domény pak odpovědi s dodatečné hlavičky, povolení nebo odepření přístupu k původní doméně na prostředky.

Služby Azure storage teď podporují CORS, takže Jakmile jednou nastavíte pravidla CORS pro službu, správně ověřený požadavek směřovaný na službu z jiné domény je vyhodnocován pro určení, zda je povoleno podle pravidel, které jste zadali.
## <a name="networking"></a>Sítě
V části poskytuje další informace o klíčových funkcí v Azure network security a souhrnné informace, informace o těchto možnostech.

### <a name="network-layer-controls"></a>Ovládací prvky síťové vrstvy
Řízení přístupu k síti je v rámci omezení připojení do a z konkrétních zařízení nebo podsítí a představuje základní zabezpečení sítě. Abyste měli jistotu, že je přístupný jenom uživatelům a zařízením, u kterých chcete je přístupné virtuální počítače a služby je cílem řízení přístupu k síti.

#### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
A [skupina zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) je základní stavových paketů filtrování a brány firewall umožňuje řídit přístup na základě [5 řazené kolekce členů](https://www.techopedia.com/definition/28190/5-tuple). Skupiny Nsg se neposkytuje kontroly vrstvy aplikace nebo ověřený řízení přístupu. Jejich slouží k řízení provozu přesouvání mezi podsítěmi v rámci virtuální sítě Azure a provoz mezi virtuální sítí Azure a Internetem.

#### <a name="route-control-and-forced-tunneling"></a>Ovládací prvek směrování a vynucené tunelování
Možnost řízení chování směrování ve vašich virtuálních sítích Azure je nejdůležitější síťové funkce ovládací prvek zabezpečení a přístup. Například pokud chcete, aby se zajistilo, že veškerý provoz do a z Azure Virtual Network prochází skrz virtuální zabezpečení zařízení, musíte být schopni řízení a přizpůsobení chování směrování. Můžete to provést pomocí konfigurace trasy definované uživatelem v Azure.

[Trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) umožňují přizpůsobit příchozí a odchozí cesty pro provoz přesunu do a z jednotlivých virtuálních počítačů nebo podsítě – největší pomáhat zajistit zabezpečení možné trasy. [Vynucené tunelování](https://www.petri.com/azure-forced-tunneling) virtuálních sítí je mechanismus, který můžete použít k zajištění, že vaše služby nejsou povoleny k navázání připojení k zařízení na Internetu.

Tím se liší od nebudou moct přijímat příchozí připojení a pak reagovat na ně. Front-endové webové servery musí reagovat na žádosti z Internetu hostitelů a proto síti Internet opensourcového vizualizačního provoz je povolený příchozí pro tyto webové servery a webové servery může reagovat.

Vynucené tunelování se běžně používá k vynucení odchozí provoz do Internetu a absolvovat bran firewall a proxy serverů s místními zabezpečení.

#### <a name="virtual-network-security-appliances"></a>Virtuální síťová zabezpečovací zařízení
Skupiny zabezpečení sítě, trasy definované uživatelem a vynuceném tunelování nabízejí úroveň zabezpečení na síť a přenosové vrstvy [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), může nastat situace, kdy chcete povolit zabezpečení na vyšší úrovně zásobník. Tyto funkce zabezpečení rozšířeného sítě můžete přistupovat pomocí zařízení řešení zabezpečení sítě partnerů Azure. Najdete nejaktuálnější sítě partnerů Azure řešení zabezpečení přechodem [Azure Marketplace](https://azure.microsoft.com/marketplace/) a vyhledáním "zabezpečení" a "zabezpečení sítě."

### <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuální síť Azure (VNet) je reprezentace vaší vlastní sítě v cloudu. Jde o logickou izolaci prostředků infrastruktury sítě Azure vyhrazenou pro vaše předplatné. V rámci této sítě máte plnou kontrolu nad bloky IP adres, nastavením DNS, zásadami zabezpečení a směrovacími tabulkami. Můžete segmentovat do podsítí virtuální sítě a umístit virtuální počítače Azure IaaS (virtuální počítače) a/nebo [cloudové služby (instance rolí PaaS)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) ve virtuálních sítích Azure.

Virtuální síť můžete navíc připojit k místní síti pomocí jedné z [možností připojení](https://docs.microsoft.com/azure/vpn-gateway/) dostupných v Azure. V podstatě můžete svoji síť rozšířit do Azure s úplnou kontrolou nad bloky IP adres a s výhodou poskytovatelů Azure celopodnikového rozsahu.

Sítě Azure podporuje různé scénáře zabezpečený vzdálený přístup. Některé z nich patří:

-   [Jednotlivé pracovní stanice se připojit ke službě Azure Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Připojení místní sítě k virtuální síti Azure pomocí sítě VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Připojení místní sítě k virtuální síti Azure pomocí vyhrazeného propojení WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Připojit k sobě navzájem virtuální sítě Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN Gateway
Pokud chcete posílat síťový provoz mezi vaší virtuální sítí Azure a vaší místní lokalitě, musíte vytvořit bránu VPN vaší virtuální sítě Azure. A [VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) je typem brány virtuální sítě, která odesílá šifrovaný síťový provoz přes veřejné připojení. Brány VPN můžete použít také k posílání provozu mezi virtuálními sítěmi Azure po prostředcích infrastruktury sítě Azure.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vyhrazené sítě WAN odkaz, který umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes vyhrazené soukromé připojení zajišťované poskytovatelem připojení.

![ExpressRoute](./media/azure-security/azure-security-fig1.png)

Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 a CRM Online. Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), síť Ethernet typu point-to-point nebo virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění.

Spojení ExpressRoute nevede veřejným Internetem a proto lze považovat za bezpečnější než u řešení založených na VPN. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) poskytuje [Application Delivery Controller (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) jako službu, nabízí různé možnosti pro vaši aplikaci Vyrovnávání zatížení vrstvy 7.

![Application Gateway](./media/azure-security/azure-security-fig2.png)

To umožňuje optimalizovat produktivitu webové farmy tím, že se ukončování náročné na CPU SSL ke službě Application Gateway (označované také jako "Přesměrování zpracování SSL" nebo "Přemostění SSL"). Poskytuje také další možnosti přesměrování vrstvy 7 jako je kruhové dotazování na distribuci příchozích přenosů, spřažení relace na základě souborů cookie, směrování na základě cesty URL a možnost hostování několika webů za jedinou službou Application Gateway. Služba Azure Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7.

Poskytuje převzetí služeb při selhání, směrování výkonu požadavků HTTP mezi různými servery, ať už jsou místní nebo v cloudu.

Aplikace poskytuje mnoho funkcí Application Delivery Controller (ADC), včetně protokolu HTTP Vyrovnávání zatížení, na základě souboru cookie relace spřažení, [vrstvy SSL (Secure Sockets)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) testy snižování zátěže, vlastní stavu, podpory více webů a řada dalších.

### <a name="web-application-firewall"></a>Web Application Firewall (Brána firewall webových aplikací)
Firewall webových aplikací je funkce [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) , která poskytuje ochranu webovým aplikacím, které používají pro standardní funkce řízení doručování aplikací (ADC) application gateway. Firewall webových aplikací chrání webové aplikace před většinou z 10 nejčastějších webových hrozeb podle OWASP.

![Web Application Firewall (Brána firewall webových aplikací)](./media/azure-security/azure-security-fig1.png)

-   Ochrana před útoky prostřednictvím injektáže SQL.

-   Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

-   Ochrana před narušením protokolu HTTP.

-   Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

-   Ochrana před roboty, prohledávacími moduly a skenery.

-   Detekce běžných chyb v konfiguraci aplikací (tj. Apache, IIS atd.)


Centralizovaný firewall webových aplikací, který chrání před webovými útoky, značně zjednodušuje správu zabezpečení a nabízí lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Firewall webových aplikací umožňuje jednoduše převést stávající aplikační brány do služby Application Gateway.
### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) umožňuje řídit distribuci uživatelského provozu do koncových bodů služby v různých datových střediscích. Koncové body služby, které Traffic Manager podporuje zahrnují virtuální počítače Azure, webové aplikace a cloudové služby. Službu Traffic Manager můžete používat také s externími koncovými body mimo Azure. Traffic Manager používá v systému DNS (Domain Name) přesměrovává klientské žádosti na nejvhodnější koncové body na základě [metody směrování provozu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) a stavu koncových bodů.

Traffic Manager poskytuje řadu metod směrování provozu tak, aby vyhovoval potřebám různé aplikace, koncový bod stavu [monitorování](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)a automatické převzetí služeb při selhání. Služba Traffic Manager je odolná vůči selhání, a to i selhání celé oblasti Azure.
### <a name="azure-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure
[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) zajišťuje vysokou dostupnost a výkon sítě pro vaše aplikace. Je Vyrovnávání zatížení vrstvy 4 (TCP, UDP), který distribuuje příchozí provoz mezi v pořádku instance služby definované v sadě s vyrovnáváním zatížení. Nástroj Azure Load Balancer můžete nakonfigurovat na:

-   Načtěte vyrovnávat příchozí přenosy z Internetu k virtuálním počítačům. Tato konfigurace se označuje jako [přístupem k Internetu pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Vyrovnávání zatížení provozu mezi virtuálními počítači ve virtuální síti, mezi virtuálními počítači v cloudových službách nebo mezi místními a virtuálními počítači ve virtuální síti mezi různými místy. Tato konfigurace se označuje jako [interní Vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Předávání externích přenosů konkrétní virtuální počítač

### <a name="internal-dns"></a>Interního serveru DNS
Můžete spravovat seznam serverů DNS používaných virtuální sítě na portálu Management Portal, nebo v konfiguračním souboru sítě. Odběratele můžete přidat až 12 servery DNS pro každou virtuální síť. Při zadávání serverů DNS, je důležité ověřit, že můžete seznam serverů DNS zákazníka ve správném pořadí pro prostředí zákazníka. Seznamy serveru DNS, nebudou fungovat kruhové dotazování. Používají se v pořadí, v jakém jsou uvedeny. Pokud je první server DNS v seznamu konfigurovanému, klient použije tento server DNS bez ohledu na to, zda DNS server funguje správně. Chcete-li změnit pořadí serveru DNS pro virtuální síti zákazníka, odeberte ze seznamu serverů DNS a jejich přidání zpět v pořadí tohoto zákazníka chce. DNS podporuje aspekty dostupnosti chaloupka zabezpečení "CIA".

### <a name="azure-dns"></a>Azure DNS
[Domain Name System](https://technet.microsoft.com/library/bb629410.aspx), nebo DNS, je zodpovědný za převod (nebo řešení) názvu webu nebo služby k její IP adrese. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostitelská služba určená pro domény DNS a zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. DNS podporuje aspekty dostupnosti chaloupka zabezpečení "CIA".
### <a name="log-analytics-nsgs"></a>Log Analytics skupin zabezpečení sítě
Můžete povolit následující kategorie diagnostických protokolů pro skupiny zabezpečení sítě:
-   událost: Obsahuje položky, pro které skupina NSG pravidla se použijí u virtuálních počítačů a instancí rolí na základě adresy MAC. Stav pro tato pravidla shromažďovaných každých 60 sekund.

-   Čítač pravidla: Obsahuje položky pro kolikrát se každé pravidlo NSG se použije pro odepření nebo povolení provozu.

### <a name="azure-security-center"></a>Azure Security Center
Security Center pomáhá zabránit, detekci a reakce na hrozby a poskytuje zvýšenou viditelnost a kontrolu nad zabezpečením vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které jinak nevšimli a spolupracuje s řadou řešení zabezpečení. Sítě System center doporučení týkající se brány firewall, skupiny zabezpečení sítě, konfiguraci pravidla pro příchozí provoz a další.

Doporučení pro síť k dispozici jsou následující:

-   [Přidání brány Firewall příští generace](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) doporučuje přidání brány Firewall pro další generace (NGFW) od partnera Microsoftu o navýšení vaší ochranu zabezpečení

-   [Směrování provozu jenom přes NGFW](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) doporučuje konfiguraci sítě pravidla skupiny zabezpečení (NSG), které vynucují příchozí provoz k vašemu virtuálnímu počítači přes vaše NGFW.

-   [Povolit skupiny zabezpečení sítě u podsítí nebo virtuálních počítačů](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) doporučuje povolit skupiny Nsg na podsítě nebo virtuálních počítačů.

-   [Omezit přístup přes internetový koncový bod](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) doporučuje nakonfigurovat pravidla pro příchozí provoz pro skupiny zabezpečení sítě.


## <a name="compute"></a>Compute

V části poskytuje další informace týkající se klíčové funkce v této oblasti a souhrnné informace, informace o těchto možnostech.

### <a name="antimalware--antivirus"></a>Antimalware & antivirové ochrany v programu
S Azure IaaS můžete použít antimalwarový software od dodavatelů zabezpečení, jako je Microsoft, Symantec, Trend Micro, McAfee a Kaspersky k ochraně virtuálních počítačů před škodlivými soubory, adwarem a dalšími hrozbami. [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Azure Cloud Services a Virtual Machines je funkce ochrany, která pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software. Antimalware od Microsoftu nabízí konfigurovatelných upozornění, když označuje, že škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v Azure systémech. Microsoft Antimalware je také možné nasadit pomocí Azure Security Center

### <a name="hardware-security-module"></a>Modul hardwarového zabezpečení
Šifrování a ověřování nevedou k lepšímu zabezpečení zamknutí samotných kláves. Vám může správy a zabezpečení důležitých tajných kódů a klíčů můžete zjednodušit jejich ukládáním do [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault umožňuje ukládat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2 standardů. SQL Server šifrování klíče pro zálohování nebo [transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx) můžete všechny uloženy ve službě Key Vault všechny klíče nebo tajné kódy z vašich aplikací. Oprávnění a přístup k těmto chráněným položkám se spravují přes [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Záloha virtuálního počítače
[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) je řešení, které chrání aplikační data s nulovou kapitálovou investicí a minimálními provozní náklady. Chyby aplikací můžou poškodit vaše data a lidské můžou způsobit chyby do svých aplikací, které může způsobit problémy se zabezpečením. Pomocí služby Azure Backup jsou chráněné virtuální počítače s Windows a Linux.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Důležitou součástí vaší organizace [obchodní kontinuity podnikových procesů/zotavení po havárii (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) strategie je tím, jak udržovat firemní úlohy a aplikace rychle zprovoznit plánovaných a neplánovaných výpadků. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) pomáhá Orchestrace replikace, převzetí služeb při selhání a obnovení úloh a aplikací tak, aby byly k dispozici ze sekundární lokality v případě, že primární lokalita ocitne mimo provoz.

### <a name="sql-vm-tde"></a>SQL VM TDE
[Transparentní šifrování dat (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) a šifrování na úrovni sloupce (Vymazat) jsou funkce šifrování SQL serveru. Tato forma šifrování vyžaduje, aby zákazníci ke správě a ukládání kryptografických klíčů, který používáte pro šifrování.

Službu Azure Key Vaultu (AKV) je navržené pro zlepšení zabezpečení a správu těchto klíčů v zabezpečených a vysoce dostupných umístění. Konektor SQL serveru umožňuje SQL serveru na použití těchto klíčů ze služby Azure Key Vault.

Pokud používáte systém SQL Server pomocí místních počítačů, existují kroky, pomocí kterých můžete pro přístup k Azure Key Vault z vašeho místního počítače systému SQL Server. Ale pro SQL Server na virtuálních počítačích Azure, můžete ušetřit čas pomocí funkce integrace Azure Key Vault. S několika rutinami Azure Powershellu pro tuto funkci povolit můžete automatizovat konfiguraci pro virtuální počítač SQL pro přístup k trezoru klíčů.

### <a name="vm-disk-encryption"></a>Šifrování disku virtuálního počítače
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) je nová funkce, která umožňuje šifrovat disky virtuálního počítače Windows a Linuxem v režimu IaaS. Bude se vztahovat oborový standard BitLocker funkce Windows a funkci DM-Crypt systému Linux zajišťuje šifrování pro operační systém a datové disky. Toto řešení je integrovaná s Azure Key Vault a pomáhá řídit a spravovat šifrování disku klíče a tajné kódy ve vašem předplatném služby Key Vault. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů šifrování v klidovém stavu ve službě Azure storage.

### <a name="virtual-networking"></a>Virtuální síť
Virtuální počítače vyžadují připojení k síti. Splnění tohoto požadavku Azure vyžaduje virtuální počítače připojit ke službě Azure Virtual Network. Virtuální síť Azure je logická konstrukce postavené na Azure síťových prostředcích infrastruktury. Každý logický [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) je izolovaná od všech jiných virtuálních sítí Azure. Tato izolace pomáhá zajistit, že síťový provoz v nasazeních není dostupný ostatním zákazníkům Microsoft Azure.

### <a name="patch-updates"></a>Oprava aktualizací
Aktualizace poskytují základ pro vyhledání a opravě potenciální problémy a zjednodušit proces správy aktualizací softwaru, jak snížit počet aktualizací softwaru, je nutné nasadit ve vašem podniku a zvýšit vaši schopnost monitorování dodržování předpisů.

### <a name="security-policy-management-and-reporting"></a>Správa zásad zabezpečení a generování sestav
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá zabránit, zjišťovat a reagovat na hrozby a poskytuje vyšší přehled a kontrolu nad zabezpečením vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které jinak nevšimli a spolupracuje s řadou řešení zabezpečení.

### <a name="azure-security-center"></a>Azure Security Center
Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Zabezpečení systémů, aplikací a dat začíná řízení přístupu založené na identitě. Funkce správy identit a přístupu, které jsou součástí Microsoft podnikové produkty a služby pomáhají chránit vaše organizace a osobní informace před neoprávněným přístupem při nastavování je dostupná pro oprávněné uživatele, kdykoli a kdekoli, potřebujete.

### <a name="secure-identity"></a>Zabezpečení identit
Společnost Microsoft používá více postupy zabezpečení a technologie ve svých produktů a služeb pro správu identit a přístupů.
-   [Ověřování službou Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) vyžaduje, aby uživatelé použít několik metod pro přístup, v místním prostředí i v cloudu. Nabízí silné ověřování s řadou jednoduchých možností při narážely uživatelům jednoduchý proces přihlašování.

-   [Microsoft Authenticator](https://aka.ms/authenticator) poskytuje uživatelsky přívětivé prostředí ověřování službou Multi-Factor Authentication, který spolupracuje s Microsoft Azure Active Directory a účty Microsoft a zahrnuje podporu pro přenosná zařízení a jejich schvalování, na základě otisku prstu.

-   [Vynucení zásad hesel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) zvyšuje úroveň zabezpečení tradiční hesla ukládat požadavky na délku a složitost, nuceně pravidelná obměna a uzamčení účtu po neúspěšných pokusů o přihlášení.

-   [Ověřování pomocí tokenu](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) umožňuje ověřování prostřednictvím Azure Active Directory.

-   [Řízení přístupu na základě role (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) umožňuje udělit přístup na základě role přiřazené uživatele, což usnadňuje uživatelům poskytnout pouze takovou úroveň přístupu, které potřebují k vykonávání těchto úkolů úlohy. RBAC můžete přizpůsobit podle obchodního modelu vaší organizace a odolnost vůči rizikům.

-   [Integrovaná správa identity (hybridní identita)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) umožňuje udržet kontrolu nad přístup uživatelů napříč interní datacentra a cloudové platformy, vytváření identity jednoho uživatele pro ověřování a autorizaci ke všem prostředkům.

### <a name="secure-apps-and-data"></a>Zabezpečení aplikací a dat
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), komplexní identit a přístupu cloudové řešení pro správu, pomáhá zabezpečit přístup k datům v aplikacích na webu a v cloudu a zjednodušuje správu uživatelů a skupin. Kombinuje základních adresářových služeb, rozšířené správy identit, zabezpečení a správy přístupu k aplikacím a usnadňuje vývojářům umožní tvořit správu identit podle zásad do svých aplikací. Pokud chcete zvýšit úroveň Azure Active Directory, můžete přidat placené funkce prostřednictvím edic Azure Active Directory Basic, Premium P1 a Premium P2.

| Free / společné funkce     | Základní funkce    |Funkce úrovně Premium P1 |Funkce úrovně Premium P2 | Azure Active Directory Join – pouze související funkce Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Adresářových objektů](https://docs.microsoft.com/azure/active-directory/active-directory-editions#directory-objects), [Správa uživatelů/skupin (přidání, aktualizace nebo odstranění) / založené na uživatelích pro zřizování a registraci zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration), [jednotné přihlašování (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#single-sign-on-sso), [samoobslužné heslo Změny pro cloudové uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users), [Connect (synchronizační modul, který rozšiřuje místní adresáře do Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory), [zabezpečení / sestavy využití](https://docs.microsoft.com/azure/active-directory/active-directory-editions#securityusage-reports)       |   [Správa přístupu na základě skupin / zřizování](https://docs.microsoft.com/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning), [samoobslužné resetování hesla pro cloudové uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users), [značky (přizpůsobení přihlašovacích stránek/přístupového panelu) společnosti](https://docs.microsoft.com/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization), [ Proxy aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-editions#application-proxy), [SLA 99,9 %](https://docs.microsoft.com/azure/active-directory/active-directory-editions#sla-999) |  [Samoobslužné skupiny a aplikace Management/samoobslužné přidávání aplikací/dynamické skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-group), [heslo samoobslužné resetování/změna/odemknutí přes místní zpětný zápis](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back), [službou Multi-Factor Authentication Authentication (cloudové a místní (MFA Server))](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [MIM CAL + MIM Server](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mim-cal-mim-server), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions#cloud-app-discovery), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-health), [Automatické předávání hesel u skupinových účtů](https://docs.microsoft.com/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|   [Ochrana identity](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Připojení zařízení k Azure AD, Desktop SSO, Microsoft Passport for Azure AD, obnovení nástroje Bitlocker správce](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [MDM automatický zápis, samoobslužné obnovení nástroje Bitlocker, další místní správci pro zařízení s Windows 10 prostřednictvím služby Azure AD Připojte se k](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) je Prémiová funkce služby Azure Active Directory, který vám pomůže identifikovat cloudových aplikací, které používají zaměstnanci ve vaší organizaci.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) je služba zabezpečení, která využívá funkce detekce anomálií ve službě Azure Active Directory k poskytování umožní získat přehled o rizikových událostech a potenciálních chybách zabezpečení, které by mohly ovlivnit vaši identity ve vaší organizaci.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně, aniž byste museli nasazovat řadiče domény. Uživatelé přihlásit k těmto virtuálním počítačům pomocí svých podnikových přihlašovacích údajů služby Active Directory a můžou bezproblémově přistupovat k prostředkům.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) je služba pro správu s vysokou dostupností, globální identit pro zákaznické aplikace, které můžete škálovat na stovky milionů identit a integrovat do mobilních i webových platforem. Vaši zákazníci se můžete přihlásit ke všem aplikacím přes přizpůsobitelné prostředí, které používají existující účty sociálních médií, nebo můžete vytvořit nové samostatné přihlašovací údaje.

- [Azure Active Directory B2B spolupráce](https://aka.ms/aad-b2b-collaboration) je zabezpečené partnerské řešení integrace, který podporuje vaše vztahy s ostatními společnostmi tím, že umožňuje partnerům přístup k podnikovým aplikacím a datům selektivně pomocí jejich samosprávné identity.

- [Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) umožňuje rozšíření možností cloudu u zařízení s Windows 10 pro centralizovanou správu. Umožňuje uživatelům připojit se k podnikové nebo organizační cloudu prostřednictvím Azure Active Directory a zjednodušuje přístup k aplikacím a prostředkům.

- [Azure Active Directory Application Proxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) poskytuje jednotné přihlašování a zabezpečený vzdálený přístup pro místně hostované webové aplikace.

## <a name="next-steps"></a>Další kroky
- [Začínáme se zabezpečením Microsoft Azure](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Služby a funkce Azure, které můžete použít ke zvýšení zabezpečení služeb a dat v rámci Azure

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Ochrana před hrozbami, rozpoznání hrozeb a reakce na hrozby – zvýšená viditelnost a kontrola nad zabezpečením vašich prostředků Azure

- [Monitorování stavu zabezpečení pomocí Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Možnosti monitorování v Azure Security Center k monitorování dodržování zásad.
