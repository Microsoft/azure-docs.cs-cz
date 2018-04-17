---
title: Úvod do Azure zabezpečení | Microsoft Docs
description: Další informace o zabezpečení Azure, jejích služeb a jak to funguje.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 51d0f1f6d2689e37c1c39bf4a5e8078a2482c76d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-azure-security"></a>Úvod do Azure zabezpečení
## <a name="overview"></a>Přehled
Víme, že je zabezpečení úlohy, jeden v cloudu a jak důležité je, že zjistíte přesné a aktuální informace o zabezpečení Azure. Jedním z nejlepší důvodů, proč používat Azure pro vaše aplikace a služby je využívat jeho širokou škálu zabezpečení nástroje a možnosti. Tyto nástroje a možnosti pomáhat ji možné vytvořit zabezpečený řešení na zabezpečené platformy Azure. Microsoft Azure poskytuje utajení, integrita a dostupnost dat zákazníka, a zároveň umožnit transparentní odpovědnosti za.

Vám pomůžou lépe pochopit kolekce zabezpečení je zapsán ovládací prvky implementované v rámci Microsoft Azure z odběratele a Microsoft operations perspektivy, tento dokument white paper, "Úvod do Azure Security", k poskytování komplexní vzhledu na zabezpečení, které jsou dostupné v Microsoft Azure.

### <a name="azure-platform"></a>Platformy Azure
Azure je platforma služby veřejného cloudu, která podporuje široký výběr operačních systémů, programovací jazyky, rozhraní, nástroje, databází a zařízení. Může probíhat Linux kontejnery s integrace Dockeru; vývoj aplikací pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js; sestavení back EndY pro iOS, Android a Windows zařízení.

Služby veřejného cloudu Azure podporují stejné technologie miliony vývojářů a IT profesionály již spoléhají na a vztah důvěryhodnosti. Sestavení nebo migrovat aktiva IT, poskytovatele služeb veřejného cloudu se spoléhat na dané organizace dalo k ochraně vašich aplikací a dat se službami a ovládací prvky slouží ke správě zabezpečení vaše cloudové prostředky.

Infrastruktury Azure a k aplikacím pro hostování miliony zákazníků současně slouží ze zařízení a poskytuje trustworthy foundation, na kterém může podnikům splňovat požadavky jejich zabezpečení.

Azure navíc poskytuje širokou škálu možností konfigurovat zabezpečení a umožňuje řídit tak, aby zabezpečení ke splnění jedinečným požadavkům vaší organizace nasazení můžete přizpůsobit. Tento dokument vám pomůže pochopit zabezpečení jak Azure funkce vám může pomoct splnění těchto požadavků.

> [!Note]
> Hlavním cílem tohoto dokumentu je na přístupném zákazníkovi ovládacích prvků, které můžete přizpůsobit a zvýšit zabezpečení pro aplikace a služby.
>
> Jsme poskytují několik informací přehled, ale podrobné informace o tom, jak Microsoft zabezpečuje samotné platformě Azure, najdete informace, které jsou součástí [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Abstraktní
Veřejný cloud migrace byly na začátku doprovází úsporu nákladů a flexibility mohou. Zabezpečení byla považována za hlavní problém pro delší dobu a i zobrazit zátkou, pro migraci veřejného cloudu. Veřejný cloud zabezpečení má však přešla ze závažný problém na jednu z ovladače pro migrace do cloudu. Důvody, to je vynikající možnost poskytovatelů služeb velké veřejného cloudu chránit aplikace a data cloudové prostředky.

Infrastruktura Azure je od zařízení až po aplikace navržena tak, aby umožňovala hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro splnění potřeb zabezpečení jednotlivých podniků. Kromě toho Azure poskytuje širokou škálu možností konfigurovat zabezpečení a umožňuje řídit tak, aby můžete přizpůsobit zabezpečení splnění jedinečný vašeho nasazení plnit IT zásad řízení a dodržovat externí předpisy.

Tento dokument popisuje přístup společnosti Microsoft k zabezpečení v rámci Cloudová platforma Microsoft Azure:
* Funkce zabezpečení implementované Microsoft k zabezpečení infrastrukturu Azure, zákaznických dat a aplikací.
* Funkce zabezpečení, které jsou k dispozici ke správě zabezpečení služeb a vaše data v rámci vašich předplatných Azure a službami Azure.

## <a name="summary-azure-security-capabilities"></a>Možnosti shrnutí zabezpečení Azure
Následující tabulka zadejte stručný popis funkcí zabezpečení, které jsou implementované Microsoft k zabezpečení infrastrukturu Azure, data zákazníků a zabezpečených aplikací.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Funkce zabezpečení, implementovaný zabezpečit platformy Azure:
Funkce uvedené následující jsou možnosti, které můžete zkontrolovat na poskytuje záruku, že platformou Azure spravovat zabezpečeným způsobem. Byly zadány odkazy pro další podrobnostem na tom, jak Microsoft adresy dotazy zákazníků důvěryhodnosti čtyři oblasti: zabezpečení platformy, ochrany osobních údajů a ovládací prvky, dodržování předpisů a průhlednost.


| [Zabezpečené platformy](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Ochrana osobních údajů a ovládací prvky](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Dodržování předpisů](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Průhlednost](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Cyklus zaměřený na zabezpečení vývoj](https://www.microsoft.com/en-us/sdl/)Interní audity | [Spravovat vaše data vždy](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Centrum zabezpečení](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Jak Microsoft zabezpečuje dat zákazníka ve službách Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Povinné bezpečnostního školení, pozadí kontroly](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Řízení na umístění dat](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Běžné ovládací prvky rozbočovače](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Jak spravovat Microsoft umístění dat do služby Azure](http://azuredatacentermap.azurewebsites.net/)|
| [Testování průnikům](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [zjišťování neoprávněných vniknutí DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [audity & protokolování](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Poskytovat přístup k datům na vaše podmínky](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Cloudové služby kvůli opatrností kontrolní seznam](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Kdo v Microsoft má přístup k datům na jaké podmínky](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Stav systému datové centrum obrázky](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), fyzického zabezpečení [zabezpečení sítě](https://docs.microsoft.com/azure/security/security-network-overview) | [Neodpovídá na požadavky policie a bezpečnostních složek](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Dodržování předpisů podle umístění & odvětví služeb](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Jak Microsoft zabezpečuje dat zákazníka ve službách Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Reakcí na incidenty zabezpečení](http://aka.ms/SecurityResponsepaper), [sdílené odpovědnosti](http://aka.ms/sharedresponsibility) |[Standardy přísné ochrany osobních údajů](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Zkontrolujte certifikační službách Azure průhlednost rozbočovače](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Funkce zabezpečení, které nabízí Azure k zabezpečení dat a aplikací
V závislosti na modelu cloudové služby je proměnné odpovědnost, pro který je zodpovědný za správu zabezpečení aplikace nebo služby. Možnosti nejsou k dispozici v platformě Azure, které vám pomohou při splnění těchto odpovědnosti prostřednictvím integrovaných funkcí a prostřednictvím partnerských řešení, které lze nasadit do předplatného Azure.

Integrované funkce jsou uspořádány do šesti (6) funkčním oblastem: operace, aplikace, úložiště, sítě, výpočetních a Identity. Další podrobnosti o funkce a možnosti, které jsou k dispozici v platformě Azure v těchto oblastech šest (6) jsou k dispozici prostřednictvím souhrnné informace.

## <a name="operations"></a>Operace
Tato část obsahuje další informace o klíčových funkcích v operacích zabezpečení a souhrnné informace o těchto možnostech.

### <a name="operations-management-suite-security-and-audit-dashboard"></a>Operations Management Suite zabezpečení a Audit řídicí panel
[OMS zabezpečení a Audit řešení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) poskytuje komplexní pohled na vaše organizace IT postavení zabezpečení s [integrovaný vyhledávací dotazy](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) pro významné problémy, které vyžadují vaši pozornost. Řídicí panel [Zabezpečení a audit](https://technet.microsoft.com/library/mt484091.aspx) je domovskou obrazovkou pro všechno, co v OMS souvisí se zabezpečením. Poskytuje podrobný přehled o stavu zabezpečení vašeho počítače. Umožňuje také zobrazit všechny události za posledních 24 hodin, 7 dní nebo za jakýkoli zadaný interval.

Kromě toho můžete nakonfigurovat OMS zabezpečení a dodržování předpisů pro [automaticky provádět určité akce](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) při zjištění určité události.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) umožňuje pracovat s prostředky ve vašem řešení jako se skupinou. Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Můžete použít [šablony Azure Resource Manageru](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) pro nasazení a tato šablona může fungovat v různých prostředích, jako je testování, pracovní a provozní. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

Nasazení založené na šablonách Azure Resource Manager pomáhají zvýšit zabezpečení řešení nasazené v Azure, protože standardní zabezpečení řídit nastavení a můžete integrovat do standardizované nasazení na základě šablon. Tím se snižuje riziko chyby konfigurace zabezpečení, které může probíhat během ruční nasazení.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) je rozšiřitelný služba Správa výkonu aplikace (APM) pro vývojářům webů. S nástrojem Application Insights můžete monitorovat za chodu webových aplikací a automaticky zjišťovat anomálie výkonu. Obsahuje nástroje výkonné analytics při diagnostice problémů a zjistit, co uživatelé ve skutečnosti dělají s vaší aplikací. Sleduje aplikace vždy, které běží, během testování a po publikována nebo nasazená.

Application Insights vytvoří grafů a tabulek, které vás, například zobrazit, jaké denní dobu získat většina uživatelů, jak reaguje aplikace je a jak dobře, jsou-li obsloužených žádné externí služby, které závisí na.

Pokud dojde k chybě, chyby nebo problémy s výkonem, můžete hledat data telemetrie podrobně při určování příčin. A služba vám pošle e-mailů Pokud existují změny v dostupnosti a výkonu vaší aplikace. Přehled aplikace proto stane nástroj cenné zabezpečení, protože pomáhá s dostupnost v utajení, integrita a dostupnost chaloupka zabezpečení.

### <a name="azure-monitor"></a>Azure Monitor
[Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) nabízí vizualizace, dotaz, směrování, výstrahy, automatické škálování a automatizace dat i z infrastrukturu Azure ([protokol aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) a každý jednotlivých prostředků Azure ([diagnostiky Protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Monitorování Azure můžete výstrahy na události související se zabezpečením, které se generují protokolů Azure.

### <a name="log-analytics"></a>Log Analytics
[Analýza protokolu](https://azure.microsoft.com/documentation/services/log-analytics/) součástí [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) – poskytuje do řešení pro správu IT pro místní i jiných výrobců cloudové infrastruktury (například AWS) kromě prostředků Azure. Data z Azure monitorování mohou být směrovány přímo k Log Analytics, abyste viděli metriky a protokoly pro celé prostředí na jednom místě.

Užitečné nástroje při analýze forenzní a dalších zabezpečení, může se analýza protokolů, jako tento nástroj umožňuje rychle prohledat prostřednictvím velkých objemů záznamy související se zabezpečením s přístupem flexibilní dotazování. Kromě toho místní [protokoly brány firewall a proxy serveru můžete exportovat do Azure a k dispozici pro analýzu pomocí analýzy protokolů.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](https://docs.microsoft.com/azure/advisor/) je konzultantem přizpůsobené cloudu, který umožňuje optimalizovat Azure nasazení. Analyzuje telemetrii využití a konfiguraci prostředků. Potom doporučí řešení k vylepšení [výkonu](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [zabezpečení](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), a [vysokou dostupnost](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) vašich prostředků při hledání budou moci [snížit vaše celkové Azure tráví](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Azure Advisor poskytuje doporučení zabezpečení, které může výrazné zlepšení vaší celkové postavení zabezpečení pro řešení, které můžete nasadit v Azure. Tato doporučení jsou vykreslovány z provádí analýzu zabezpečení [Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Kromě toho Azure Security Center pomáhá s operace zabezpečení tím, že poskytuje jednoho řídicího panelu této výstrahy ploch a doporučení, které můžete reagovali na ni okamžitě. Často můžete opravit problémy s jedním kliknutím v konzole Azure Security Center.
## <a name="applications"></a>Aplikace
Tato část poskytuje další informace o klíčových funkcích v aplikaci zabezpečení a souhrnné informace o těchto možnostech.

### <a name="web-application-vulnerability-scanning"></a>Webové aplikace ohrožení zabezpečení kontrolu
Jedním z nejjednodušších způsobů, jak začít pracovat s testování pro chyby zabezpečení v vaše [aplikaci služby App Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview) , je použít [integrace s Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) k ohrožení zabezpečení jedním kliknutím kontrolu ve vaší aplikaci. Můžete zobrazit výsledky testů ve zprávě o snadno pochopit a zjistěte, jak opravit jednotlivé chyby zabezpečení s podrobnými pokyny.

### <a name="penetration-testing"></a>Testování průniku
Pokud dáváte přednost proveďte vlastní testy průnikům nebo chcete použít jiný skener suite nebo poskytovatele, postupujte podle [Azure průnikům testování procesu schvalování](https://security-forms.azure.com/penetration-testing/terms) a získat předchozí schválení k provedení požadované průnikům testů.

### <a name="web-application-firewall"></a>Brány firewall webových aplikací
Brány firewall webových aplikací (firewall webových aplikací) v [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) pomáhá chránit před běžné útoky založenými na web jako Injektáž SQL, útoky skriptování mezi weby a zneužití relace webové aplikace. Vybavená předem nakonfigurovaným rozhraním ochrany před hrozbami identifikovaný [otevřete webovou aplikaci zabezpečení projektu (OWASP) jako známých chyb zabezpečení prvních 10](https://msdn.microsoft.com/library/).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Ověřování a autorizace v prostředí Azure App Service
[Aplikace služby ověřování / autorizace](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) je funkce, která poskytuje způsob, jak vaše aplikace k přihlášení uživatele, takže není nutné změnit kód na back-end aplikace. Poskytuje snadný způsob, jak chránit vaše aplikace a pracovat s daty na uživatele.

### <a name="layered-security-architecture"></a>Architektura vrstveného zabezpečení
Vzhledem k tomu [prostředí App Service](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) poskytuje izolovaném prostředí nasadit do [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), vývojáři můžou vytvářet architektura vrstveného zabezpečení poskytuje různé úrovně přístup k síti pro každou vrstvu aplikace. Společné přání je skrýt rozhraní API back EndY z obecné přístup k Internetu a Povolit jenom rozhraní API, která se má volat nadřazeného webové aplikace. [Skupin zabezpečení (Nsg) sítě](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) lze použít na podsítě virtuální sítě Azure obsahující prostředí App Service omezit veřejný přístup k aplikacím API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Webový server diagnostics a application diagnostics
Webové aplikace služby App Service poskytují diagnostické funkce pro protokolování informací z webového serveru a webové aplikace. Tyto jsou logicky rozdělené do [webový server diagnostiky](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) a [rozhraní application diagnostics](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Webový server zahrnuje dva hlavní pokroky v diagnostice a řešení potíží s weby a aplikace.

První novou funkci se informace o fondech aplikací, pracovních procesů, lokality, aplikační domény a spuštěné žádosti v reálném čase stavu. Druhé nové výhody jsou podrobné trasovat události, které trasování požadavku v celém procesu dokončení požadavku a odpovědi.

Chcete-li povolit shromažďování tyto události trasování, můžete být IIS 7 nakonfigurován tak, aby automaticky úplné trasování protokoly ve formátu XML pro všechny konkrétní požadavek na základě uplynulý čas nebo kódy chyb odpovědi.

#### <a name="web-server-diagnostics"></a>Diagnostika webového serveru
Můžete povolit nebo zakázat následující typy protokolů:

-   Podrobné protokolování chyb - podrobné informace o chybě pro stavové kódy HTTP, které indikují chybu (kód stavu 400 nebo vyšší). To může obsahovat informace, které vám mohou pomoci určit, proč server vrátil kód chyby.

-   Trasování neúspěšných žádostí - podrobné informace o chybných žádostech, včetně trasování pro součásti služby IIS používá ke zpracování žádostí a doba trvání v jednotlivých součástí. To může být užitečné, pokud se pokoušíte zvýšit výkon webu nebo izolovat, co ho způsobuje. konkrétní chyba protokolu HTTP, který se má vrátit.

-   Webový Server protokolování - informace o použití rozšířeném formátu protokolu W3C souboru transakce HTTP. To je užitečné, když chcete určit celkový metriky lokality, jako je počet požadavků zpracovaných nebo je počet požadavků z konkrétní IP adresu.

#### <a name="application-diagnostics"></a>Diagnostika aplikace
[Rozhraní Application diagnostics](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) umožňuje zaznamenat informace o vytvořil webovou aplikací. Aplikace ASP.NET můžete použít [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) třída do protokolu informace o protokolu diagnostiky aplikace. V konzoli Application Diagnostics existují dva hlavní typy událostí, s ohledem na výkon aplikace a události související se selháním a chybami aplikací. Selhání a chyby lze rozdělit dále do problémy s připojením, zabezpečení a selhání. Selhání jsou obvykle vztahujících se k problému s kódem aplikace.

V konzoli Application Diagnostics lze zobrazit události seskupené následujícími způsoby:

-   Vše (zobrazí se všechny události)
-   Chyby aplikace (události výjimky zobrazí)
-   Výkon (zobrazí se události výkonu)

## <a name="storage"></a>Úložiště
Tato část poskytuje další informace o klíčových funkcích v zabezpečení a souhrnné informace o těchto možnostech úložiště Azure.

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Můžete zabezpečit váš účet úložiště pomocí řízení přístupu na základě rolí (RBAC). Omezení přístupu na základě [potřebovat znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) Principy zabezpečení je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Tato oprávnění jsou udělena přiřazením příslušné role RBAC skupinám a aplikacím v určitých oboru. Můžete použít [předdefinované role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles), jako je Přispěvatel účet úložiště, přiřadit oprávnění pro uživatele. Přístup k úložišti klíče pro účet úložiště pomocí [Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) modelu se dá řídit přes řízení přístupu na základě Role (RBAC).

### <a name="shared-access-signature"></a>Sdílený přístupový podpis
[Sdílený přístupový podpis (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. SAS znamená udělit, že klient v zadaném období a se zadanou sadou oprávnění omezená oprávnění k objektům v účtu úložiště. Tato omezená oprávnění můžete udělit bez nutnosti sdílet klíče pro přístup k účtu.

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Šifrování během přenosu je mechanismus ochrany dat při přenosu v sítích. S Azure Storage můžete zabezpečit pomocí dat:
-   [Šifrování transportní vrstvy](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), jako je například HTTPS při přenosu dat do nebo z Azure Storage.

-   [Propojit šifrování](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), jako například [šifrování SMB 3.0](https://docs.microsoft.com/azure/storage/storage-security-guide) pro [Azure sdílené složky](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Šifrování na straně klienta, k šifrování dat, než se přenese do úložiště a k dešifrování dat po se přenáší z úložiště.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Pro mnoho společností je šifrování dat v klidovém stavu povinný krok k ochrany osobních údajů, dodržování předpisů a suverenity data. Existují tři funkcí zabezpečení úložiště Azure, které poskytují šifrování dat, která je "v klidovém stavu":

-   [Šifrování služby úložiště](https://docs.microsoft.com/azure/storage/storage-service-encryption) umožňuje požadovat, aby službu úložiště automaticky šifrování dat při zápisu do služby Azure Storage.

-   [Šifrování na straně klienta](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) také poskytuje funkci šifrování v klidovém stavu.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) umožňuje šifrování disků operačního systému a datové disky používané virtuálním počítačem IaaS.

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště. Analytika úložiště protokoly podrobné informace o úspěšných a nezdařených požadavků a služba úložiště. Tyto informace slouží k monitorování jednotlivých požadavků a diagnostikovat problémy s služby úložiště. Na základě typu best effort protokolované požadavky. Jsou zaznamenány následující typy ověřených požadavků:
-   Úspěšné požadavky.

-   Neúspěšné požadavky, včetně vypršení časového limitu, omezení šířky pásma, sítě, autorizace a dalších chyb.

-   Požadavky na použití sdíleného přístupového podpisu (SAS), včetně úspěšné a neúspěšné požadavky.

-   Požadavky na analytická data.

### <a name="enabling-browser-based-clients-using-cors"></a>Povolení založené na prohlížeči klientů pomocí CORS
[Sdílení prostředků různých původů (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) mechanismus, který umožňuje domény k sobě navzájem udělit oprávnění pro přístup k prostředkům druhé strany. Uživatelský Agent odešle další hlavičky zajistit, že kód jazyka JavaScript načíst z určité domény je povolen přístup k prostředkům v jiné doméně. Druhé domény a odpovědi s další hlavičky povolit nebo odmítnout původní domény přístup k jeho prostředky.

Služby Azure storage teď podporují CORS tak, aby po nastavení pravidla CORS pro službu správně ověřené požadavek na službu z jiné domény vyhodnotí k určení, zda je povoleno podle pravidel, který jste zadali.
## <a name="networking"></a>Sítě
Tato část poskytuje další informace o klíčových funkcích v síti Azure informace o zabezpečení a souhrnné informace o těchto možnostech.

### <a name="network-layer-controls"></a>Ovládací prvky sítě vrstev
Řízení přístupu k síti je v rámci omezení připojení do a z konkrétní zařízení nebo podsítě a představuje základní zabezpečení sítě. Cílem řízení přístupu k síti je zajistit, že virtuální počítače a služby jsou dostupné pouze uživatelé a zařízení, u kterých chcete je přístupný.

#### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
A [skupina zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) je základní stavových paketů filtrování brány firewall a umožňuje řízení přístupu na základě [5 řazené kolekce členů](https://www.techopedia.com/definition/28190/5-tuple). Skupiny Nsg neposkytují kontroly vrstvy aplikací nebo ověřený řízení přístupu. Může se použít k řízení provozu přesun mezi podsítěmi v rámci virtuální síť Azure a provoz mezi virtuální síť Azure a Internet.

#### <a name="route-control-and-forced-tunneling"></a>Řízení směrování a vynucené tunelování
Možnost řídit chování směrování na virtuálních sítí Azure je kritických síťových funkce řízení zabezpečení a přístup. Například pokud chcete zajistit, aby veškerý provoz do a z Azure Virtual Network prochází této virtuální zabezpečení zařízení, musíte být schopni řídit a přizpůsobit chování směrování. To provedete tak, že nakonfigurujete trasy definované uživatelem v Azure.

[Trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) slouží k úpravě příchozí a odchozí cesty pro přesun do provozu a mimo jednotlivé virtuální počítače nebo podsítě nejvíce zajistit zabezpečení možné trasy. [Vynuceného tunelování](https://www.petri.com/azure-forced-tunneling) mechanismus, můžete zajistit, že vaše služby nejsou povolené k navázání připojení k zařízení na Internetu.

To se liší od je schopen přijmout příchozí spojení a poté reagovat na ně. Webových serverů front-end potřebovat reagovat na požadavky z hostitelů v Internetu a tak Internet Source provoz je povolený příchozí na tyto webové servery a webové servery může reagovat.

Vynucené tunelování se často používá ke vynutit odchozí přenosy na Internetu, abychom mohli absolvovat zabezpečení místní proxy servery a brány firewall.

#### <a name="virtual-network-security-appliances"></a>Virtuální síť zabezpečovací zařízení
Zatímco skupin zabezpečení sítě, trasy definované uživatelem a vynucené tunelování poskytují úroveň zabezpečení na síť a transportní vrstvy [OSI model](https://en.wikipedia.org/wiki/OSI_model), může nastat situace, když chcete povolit s vyšší úrovní zabezpečení zásobníku. Tyto funkce Rozšířené sítě zabezpečení dostanete pomocí zařízení řešení zabezpečení sítě partnera Azure. Můžete najít nejaktuálnější sítě Azure partnera řešení zabezpečení navštivte stránky [Azure Marketplace](https://azure.microsoft.com/marketplace/) a při vyhledání "zabezpečení" a "zabezpečení sítě."

### <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuální síť Azure (VNet) je reprezentace vaší vlastní sítě v cloudu. Je to logická izolace vyhrazeného pro vaše předplatné Azure síťové infrastruktury. V rámci této sítě máte plnou kontrolu nad bloky IP adres, nastavením DNS, zásadami zabezpečení a směrovacími tabulkami. Můžete segmentovat do podsítí virtuální sítě a umísťovat virtuální počítače Azure IaaS (VM) nebo [cloudové služby (instance rolí PaaS)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) na virtuálních sítí Azure.

Virtuální síť můžete navíc připojit k místní síti pomocí jedné z [možností připojení](https://docs.microsoft.com/azure/vpn-gateway/) dostupných v Azure. V podstatě můžete svoji síť rozšířit do Azure s úplnou kontrolou nad bloky IP adres a s výhodou poskytovatelů Azure celopodnikového rozsahu.

Azure sítě podporuje různé scénáře zabezpečený vzdálený přístup. Mezi ty patří:

-   [Připojit k virtuální síti Azure jednotlivé pracovní stanice](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Připojit místní síť k virtuální síti Azure prostřednictvím sítě VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Připojit k virtuální síti Azure s vyhrazenou propojení WAN do místní sítě](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Připojit k sobě navzájem virtuální sítě Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN Gateway
K posílání síťového provozu mezi vaší virtuální sítě Azure a místními servery, musíte vytvořit bránu VPN pro virtuální síť Azure. A [brány VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) je typ brány virtuální sítě, které odesílá šifrovaný provoz přes veřejný připojení. Brány sítě VPN můžete taky k posílání provozu mezi virtuálními sítěmi Azure přes Azure síťových prostředcích infrastruktury.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) je vyhrazené propojení WAN, který umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes vyhrazené soukromé připojení zajišťované poskytovatelem připojení.

![ExpressRoute](./media/azure-security/azure-security-fig1.png)

Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 a CRM Online. Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), síť Ethernet typu point-to-point nebo virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění.

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného Internetu a proto lze považovat za bezpečnější než řešení založených na VPN. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) poskytuje [aplikace doručení řadiče (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) jako služba nabízí různé vrstvy 7 možnosti vyrovnávání zatížení pro vaši aplikaci.

![Application Gateway](./media/azure-security/azure-security-fig2.png)

Umožňuje optimalizovat webové farmy produktivitu přesměrováním zátěže procesoru náročné ukončení protokolu SSL pro službu Application Gateway (také označované jako "Přesměrování zpracování SSL" nebo "Přemostění SSL"). Poskytuje také další možnosti směrování vrstvy 7 včetně kruhového dotazování distribuce příchozí provoz, spřažení na základě souboru cookie relace, na základě cestu směrování adres URL a umožňuje hostování více webů za jeden Application Gateway. Služba Azure Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7.

Poskytuje převzetí služeb při selhání, směrování výkonu požadavků HTTP mezi různými servery, ať už jsou místní nebo v cloudu.

Aplikace nabízí řadu funkcí aplikace doručení řadiče (ADC), včetně HTTP Vyrovnávání zatížení, na základě souboru cookie relace spřažení, [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) testy snižování zátěže, vlastní stavu, podporu pro více lokalit, a mnohé další.

### <a name="web-application-firewall"></a>Web Application Firewall (Brána firewall webových aplikací)
Brány Firewall webových aplikací je funkce [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) , který poskytuje ochranu k webovým aplikacím, které používají aplikační brány pro standardní funkce doručování aplikací ovládací prvek (ADC). Firewall webových aplikací chrání webové aplikace před většinou z 10 nejčastějších webových hrozeb podle OWASP.

![Web Application Firewall (Brána firewall webových aplikací)](./media/azure-security/azure-security-fig1.png)

-   Ochrana před útoky prostřednictvím injektáže SQL.

-   Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

-   Ochrana před narušením protokolu HTTP.

-   Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

-   Ochrana před roboty, prohledávacími moduly a skenery.

-   Detekce časté nesprávné konfigurace aplikace (tedy Apache, IIS, atd.)


Centralizovaný firewall webových aplikací, který chrání před webovými útoky, značně zjednodušuje správu zabezpečení a nabízí lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Firewall webových aplikací umožňuje jednoduše převést stávající aplikační brány do služby Application Gateway.
### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) umožňuje řízení distribuce provozu generovaného uživateli pro koncové body služby v různých datových střediscích. Koncové body služby, které jsou podporovány nástrojem Traffic Manager zahrnují virtuální počítače Azure, webové aplikace a cloudové služby. Službu Traffic Manager můžete používat také s externími koncovými body mimo Azure. Traffic Manager používá v systému DNS (Domain Name) pro přesměrování požadavků klientů na základě nejvhodnější koncový bod [metodu směrování provozu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) a stav koncových bodů.

Traffic Manager poskytuje celou řadu metod směrování provozu tak, aby vyhovovala potřebám jiné aplikace, koncový bod stavu [monitorování](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)a automatické převzetí služeb při selhání. Správce provozu je odolný proti selhání, včetně selhání celé oblasti Azure.
### <a name="azure-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure
[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) zajišťuje vysokou dostupnost a výkon sítě pro vaše aplikace. Je Vyrovnávání zatížení vrstvy 4 (TCP, UDP), která distribuuje příchozí komunikaci mezi pořádku instancí služby definované v sadě s vyrovnáváním zatížení. Azure nástroj pro vyrovnávání zatížení můžete nakonfigurovat:

-   Načtěte vyrovnávání příchozí přenosy z Internetu k virtuálním počítačům. Tato konfigurace se označuje jako [Vyrovnávání zatížení internetového](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Přenosy Vyrovnávání zatížení mezi virtuálními počítači ve virtuální síti, mezi virtuálními počítači v cloudové služby nebo mezi místními počítači a virtuální počítače ve virtuální síti mezi různými místy. Tato konfigurace se označuje jako [Vyrovnávání zatížení pro vnitřní](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Externí přenos dat pro konkrétní virtuální počítač

### <a name="internal-dns"></a>Interní DNS
Můžete spravovat seznam serverů DNS se používá ve virtuální síti v portálu pro správu nebo v konfiguračním souboru na síti. Zákazníka můžete přidat až 12 serverů DNS pro každý virtuální síť. Při zadávání serverů DNS, je důležité si ověřit, že můžete seznam serverů DNS zákazníka ve správném pořadí pro prostředí zákazníka. Seznamy serveru DNS nefungují kruhového dotazování. Jsou použity v pořadí, ve kterém jsou uvedené. Pokud je první server DNS v seznamu nelze připojit, klient použije tento server DNS, bez ohledu na to, zda je DNS server funguje správně nebo ne. Změnit pořadí serveru DNS pro virtuální síť zákazníka, odeberte ze seznamu serverů DNS a přidat je zpět v pořadí tohoto zákazníka chce. DNS podporuje dostupnosti aspektů chaloupka zabezpečení "c oddílu".

### <a name="azure-dns"></a>Azure DNS
[Domain Name System](https://technet.microsoft.com/library/bb629410.aspx), nebo DNS, je zodpovědný za překladu (nebo vyřešení) název webu nebo služby na jeho IP adresu. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostitelská služba domén DNS, poskytnutí překladu názvů pomocí infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. DNS podporuje dostupnosti aspektů chaloupka zabezpečení "c oddílu".
### <a name="log-analytics-nsgs"></a>Skupiny Nsg analýzy protokolů
Pro skupiny Nsg můžete povolit v těchto kategoriích protokolů diagnostiky:
-   Události: Obsahuje položky, pro které skupina NSG pravidla virtuální počítače a instance rolí na základě adresy MAC. Stav pro tato pravidla se shromažďují každých 60 sekund.

-   Čítač pravidel: obsahuje položky pro počet opakování každé pravidlo NSG se použije pro odepřít nebo povolení provozu.

### <a name="azure-security-center"></a>Azure Security Center
Security Center pomáhá zabránit, zjistit a reagovat na hrozby a poskytuje že vyšší přehled a kontrolu nad zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které byste jinak nevšimli a spolupracuje s řadou řešení zabezpečení. Síťového střediska doporučení ohledně brány firewall, skupiny zabezpečení sítě, konfiguraci pravidla pro příchozí provoz a další.

Doporučení sítě k dispozici jsou následující:

-   [Přidat Brána Firewall příští generace](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) doporučuje přidat brány Firewall pro další generace (NGFW) z partnera společnosti Microsoft pro zvýšení ochrany vaší zabezpečení

-   [Směrování provozu prostřednictvím NGFW pouze](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) doporučuje, které nakonfigurujete sítě pravidla zabezpečení skupiny (NSG), které vynutit příchozí provoz do virtuálního počítače prostřednictvím vaší NGFW.

-   [Povolení skupin zabezpečení sítě pro podsítě nebo virtuální počítače](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) doporučuje, abyste povolili skupiny Nsg na podsítě nebo virtuálních počítačů.

-   [Omezení přístupu prostřednictvím internetové koncový bod](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) doporučuje konfigurace pravidla pro příchozí provoz pro skupiny Nsg.


## <a name="compute"></a>Compute

Tato část poskytuje další informace o klíčových funkcích v této oblasti a souhrnné informace o těchto možnostech.

### <a name="antimalware--antivirus"></a>Antimalwarové & antivirové
S Azure IaaS můžete antimalwarový software od dodavatelů zabezpečení, jako je Microsoft, Symantec, Trend Micro, McAfee a Kaspersky k ochraně vašich virtuálních počítačů ze škodlivých souborů, adwaru a dalšími hrozbami. [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Azure Cloud Services a virtuálních počítačů je ochrana funkci, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software. Antimalware od Microsoftu poskytuje konfigurovat výstrahy, když označuje, že škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v Azure systémech. Antimalware od Microsoftu můžete nasadit také pomocí Azure Security Center

### <a name="hardware-security-module"></a>Modul hardwarového zabezpečení
Šifrování a ověřování zlepšení zabezpečení Pokud samotné klíče jsou chráněny. Můžete zjednodušit správu a zabezpečení vašich důležitých tajných klíčů a klíče v jejich uložením [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault poskytuje možnost pro uložení klíče v modulech hardwarového zabezpečení (HSM) certifikovány pro FIPS 140-2 Level 2 standardů. Klíčů pro šifrování SQL Server pro zálohování nebo [transparentní šifrování dat](https://msdn.microsoft.com/library/bb934049.aspx) všechny se uloží Key Vault se klíčů nebo tajných údajů z vašich aplikací. Oprávnění a přístup k tyto chráněné položky se spravují prostřednictvím [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Záloha virtuálního počítače
[Zálohování Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) je řešení, které chrání data aplikací s nulové kapitálovými investicemi a minimálními provozní náklady. Chyby aplikace může dojít k poškození dat a lidské chyby může způsobit chyby do vaší aplikace, které může způsobit problémy se zabezpečením. S Azure Backup jsou chráněné virtuální počítače s Windows a Linux.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Důležitou součástí vaší organizace [obchodní kontinuity nebo zotavení po havárii (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) strategie se nad tím, jak udržovat firemní úlohy a aplikace fungovaly při plánovaných a neplánovaných výpadků. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) pomáhá orchestraci replikace, převzetí služeb při selhání a obnovení úloh a aplikací, aby byly k dispozici ze sekundární lokality Pokud primární lokalita ocitne mimo provoz.

### <a name="sql-vm-tde"></a>VIRTUÁLNÍ POČÍTAČ TDE SQL
[Transparentní šifrování dat (šifrování TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) a šifrování na úrovni sloupce (Vymazat) jsou funkcí šifrování SQL serveru. Tato forma šifrování vyžaduje zákazníky ke správě a ukládání kryptografických klíčů, které používáte pro šifrování.

Službu službou Azure Key Vault (AZURE) slouží k vylepšení zabezpečení a správu tyto klíče v umístění zabezpečené a vysoce dostupné. SQL Server Connector umožňuje serveru SQL pro použití těchto klíčů z Azure Key Vault.

Pokud používáte systém SQL Server s místním počítačům, existují kroky, pomocí kterých můžete pro přístup k Azure Key Vault z vašeho místního počítače systému SQL Server. Ale pro SQL Server ve virtuálních počítačích Azure, můžete ušetřit čas pomocí funkce Azure Key Vault integrace. Pomocí několik rutin prostředí Azure PowerShell tuto funkci povolíte můžete automatizovat konfiguraci potřebné pro virtuální počítač SQL pro přístup k trezoru klíčů.

### <a name="vm-disk-encryption"></a>Šifrování disku virtuálního počítače
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) je novou funkci, která vám pomůže zašifrovat disky virtuálního počítače s Windows a Linux IaaS. Bude se vztahovat oborový standard BitLocker funkce systému Windows a DM-Crypt funkce systému Linux zajistit šifrování svazku operačního systému a datové disky. Řešení jsou integrované s Azure Key Vault můžete řídit a spravovat šifrování disku klíče a tajné klíče v rámci vašeho předplatného Key Vault. Řešení také zajistí, že všechna data na disky virtuálního počítače jsou zašifrovaná přinejmenším ve službě Azure storage.

### <a name="virtual-networking"></a>Virtuální síť
Třeba virtuální počítače připojení k síti. Pro podporu tohoto požadavku, Azure vyžaduje virtuální počítače k připojení k virtuální síti Azure. Virtuální síť Azure je logická konstrukce postavená na Azure síťových prostředcích infrastruktury. Každý logický [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) je izolovaný od všech ostatních Azure virtuálních sítí. Tato izolace pomáhá zajistit, že síťový provoz v nasazeních není dostupný ostatním zákazníkům Microsoft Azure.

### <a name="patch-updates"></a>Oprava aktualizací
Oprava aktualizací poskytují základ pro hledání a opravě potenciální problémy a zjednodušit proces správy aktualizace softwaru, protože se sníží počet aktualizací softwaru, které je nutné nasadit ve vašem podniku a tím zvýšit vaši schopnost sledování dodržování předpisů.

### <a name="security-policy-management-and-reporting"></a>Správa zásad zabezpečení a vytváření sestav
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá zabránit, zjišťovat a reagovat na hrozby a poskytuje vyšší přehled a kontrolu nad zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které byste jinak nevšimli a spolupracuje s řadou řešení zabezpečení.

### <a name="azure-security-center"></a>Azure Security Center
Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

## <a name="identify-and-access-management"></a>Identifikovat a správy přístupu

Zabezpečení systémů, aplikací a dat začíná řízení přístupu na základě identity. Funkce správy identit a přístupu, které jsou součástí obchodní produktů a služeb Microsoftu pomáhají chránit vaše organizace a osobní informace před neoprávněným přístupem při zajistit dostupná pro oprávněné uživatele kdykoli a kdekoli budou ho potřebovat.

### <a name="secure-identity"></a>Zabezpečené Identity
Společnost Microsoft používá více postupy zabezpečení a technologií napříč svých produktů a služeb pro správu identit a přístupu.
-   [Služba Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) vyžaduje, aby uživatelé použít několik metod pro přístup, místně a v cloudu. Poskytuje silné ověřování s celou řadu možností snadno ověření při požadavků uživatelů s jednoduchý proces přihlášení.

-   [Microsoft Authenticator](https://aka.ms/authenticator) poskytuje uživatelsky přívětivý možnosti vícefaktorového ověřování, které pracuje s Microsoft Azure Active Directory a účty Microsoft a zahrnuje podporu pro wearables a na základě otisku prstu schválení.

-   [Vynucení zásad hesel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) zvyšuje úroveň zabezpečení tradiční hesel nastavení požadavky na délku a složitost, vynutit pravidelné otočení a uzamčení účtu po neúspěšných pokusů o přihlášení.

-   [Ověřování na základě tokenu](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) umožňuje ověřování přes Azure Active Directory.

-   [Řízení přístupu na základě role (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) umožňuje udělit přístup na základě role přiřazené uživatele, což usnadňuje uživatelům poskytnout pouze takovou úroveň přístupu, které potřebují k vykonávání těchto úkolů úlohy. RBAC můžete přizpůsobit podle vaší organizace obchodnímu modelu a odolnost vůči rizikům.

-   [Integrovaná správa identity (hybridní identita)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) umožňuje zachovat kontrolu nad přístup uživatelů napříč interní datovými centry a cloudové platformy, vytváření identitu jednoho uživatele pro ověřování a autorizaci ke všem prostředkům.

### <a name="secure-apps-and-data"></a>Zabezpečení aplikací a dat
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), komplexní identit a přístupu cloudové řešení pro správu, pomáhá zajistit zabezpečený přístup k datům v aplikacích v lokalitě a v cloudu a zjednodušuje správu uživatelů a skupin. Zkombinuje základní adresářové služby, pokročilé zásady správného řízení identity, zabezpečení a správu přístupu aplikace a usnadňuje vývojářům vytvářet na základě zásad identity management do svých aplikací. K vylepšení služby Azure Active Directory, můžete přidat placené možnosti pomocí edice Azure Active Directory Basic, Premium P1 a Premium P2.

| Volné / běžné funkce     | Základní funkce    |Premium P1 funkce |Premium P2 funkce | Azure Active Directory Join – pouze souvisejících funkcí Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Objekty adresáře](https://docs.microsoft.com/azure/active-directory/active-directory-editions#directory-objects), [uživatele nebo skupiny správy (přidání, aktualizace nebo odstranění) / založená na uživateli zřizování, registrace zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration), [jednotné přihlašování (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#single-sign-on-sso), [samoobslužného heslo Změna uživatelům cloudu](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users), [Connect (synchronizační modul, který rozšiřuje místní adresáře do Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory), [zabezpečení / Reports využití](https://docs.microsoft.com/azure/active-directory/active-directory-editions#securityusage-reports)       |   [Správa přístupu na základě skupiny / zřizování](https://docs.microsoft.com/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning), [samoobslužného resetování hesel uživatelům cloudu](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users), [firemní Branding (vlastní nastavení přihlašovací stránky nebo přístupový Panel)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization), [ Proxy aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-editions#application-proxy), [SLA 99,9 %](https://docs.microsoft.com/azure/active-directory/active-directory-editions#sla-999) |  [Skupiny samoobslužné služby a aplikace správy/samoobslužných-služeb aplikace dodatky nebo dynamické skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-group), [heslo samoobslužného resetování nebo změny nebo odemknout s místními zpětný zápis](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back), [Multi-Factor Ověřování (Cloud a místní (MFA Server))](https://docs.microsoft.com/azure/active-directory/active-directory-editions#multi-factor-authentication-cloud-and-on-premises-mfa-server), [MIM CAL + serveru MIM](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mim-cal-mim-server), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions#cloud-app-discovery), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-health), [Výměna automatické heslo pro skupinové účty](https://docs.microsoft.com/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|   [Ochrana identity](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Připojení zařízení k Azure AD, plocha jednotné přihlašování, Microsoft Passport pro Azure AD, obnovení nástroje Bitlocker správce](https://docs.microsoft.com/azure/active-directory/active-directory-editions#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery), [MDM automatický zápis, Bitlocker samoobslužného obnovení další místní správce na zařízení s Windows 10 prostřednictvím služby Azure AD Připojení k](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) je funkce premium, Azure Active Directory, která umožňuje identifikovat cloudové aplikace, které používají zaměstnanci ve vaší organizaci.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) je služba zabezpečení, která využívá Azure Active Directory možností detekce anomálií a poskytuje ucelený přehled o rizikových událostech a potenciální ohrožení zabezpečení, které mohou ovlivnit vaše organizace identity.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně bez nutnosti nasazení řadičů domény. Uživatelé přihlásit do těchto virtuálních počítačů pomocí své podnikové přihlašovací údaje služby Active Directory a bezproblémově přistupovat k prostředkům.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) je služba správy vysokou dostupností, globální identity pro určených aplikace, které můžete škálovat pro stovky milionů identit a integrovat do různých mobilních a webové platformy. Vaši zákazníci se můžete přihlásit a všechny aplikace prostřednictvím přizpůsobitelné používající existujících účtů na sociálních sítích, nebo můžete vytvořit nové samostatné přihlašovací údaje.

- [Azure Active Directory spolupráce B2B ve](https://aka.ms/aad-b2b-collaboration) je zabezpečený partnera integrační řešení, které podporuje vaše vztahy mezi tím, že umožňuje partnerům umožnit přístup k podnikovým aplikacím a datům selektivně pomocí jejich autonomně spravovaná identity.

- [Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) můžete rozšířit možností cloudu k zařízení s Windows 10 pro centralizovanou správu. To umožňuje uživatelům připojit se ke cloudu podnikové nebo organizace prostřednictvím Azure Active Directory a zjednodušuje přístup k aplikacím a prostředkům.

- [Azure Proxy aplikace Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) zajišťuje jednotné přihlašování a zabezpečený vzdálený přístup pro webové aplikace hostované na místě.

## <a name="next-steps"></a>Další kroky
- [Začínáme s Microsoft Azure zabezpečení](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Služby a funkce Azure, které můžete použít ke zvýšení zabezpečení služeb a dat v rámci Azure

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Ochrana před hrozbami, rozpoznání hrozeb a reakce na hrozby – zvýšená viditelnost a kontrola nad zabezpečením vašich prostředků Azure

- [Monitorování stavu zabezpečení pomocí Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Monitorovací funkce v Azure Security Center k monitorování dodržování zásad.
