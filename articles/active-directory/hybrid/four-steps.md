---
title: Třeba provést čtyři kroky základ využívá silné identity s Azure Active Directory
description: Toto téma popisuje čtyři kroky, hybridní identity zákazníků a vytváří základ využívá silné identity.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96b5e8ab63c1784ff073c7ba38cd4a6319db43c5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452732"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Třeba provést čtyři kroky základ využívá silné identity s Azure Active Directory

Správa přístupu k aplikacím a datům můžete už nespoléhají na strategie hranic zabezpečení tradiční sítě například hraniční sítě a brány firewall kvůli rychlý přesun aplikací do cloudu. Organizace teď musí důvěřovat jejich řešení identity řídit, kdo a jaké má přístup k aplikacím a datům organizace. Další organizace je umožnit zaměstnancům přinést si vlastní zařízení k práci a používat svoje zařízení z libovolného místa, které můžete připojit k Internetu. Zajištění, že tato zařízení jsou vyhovující a zabezpečené přestal důležitým aspektem, který v řešení identity, které organizace zvolí možnost implementovat. V dnešních digitálních pracoviště [identita je primární ovládací prvek roviny](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) každé společnosti Přesun do cloudu.

Při přijímání hybridní řešení identit Azure Active Directory (Azure AD), organizace získat přístup k prémiové funkce, které odhalují produktivity díky automatizaci, delegování, samoobslužných služeb a jsou jediné možnosti přihlašování. Umožňuje, aby vaši pracovníci přístup k firemním prostředkům z všude, kde potřebují ke své práci zároveň umožní váš IT tým řízení, které přístup tím, že zajišťuje, že lidé mít práva na přístup ke správné prostředky k navázání zabezpečené produktivitu.

Podle poznatky, tento kontrolní seznam osvědčených postupů vám pomůže rychle nasadit doporučené akce k sestavení *silné* identity foundation ve vaší organizaci:

* Snadné připojení k aplikacím
* Automaticky vytvořit jednu identitu pro každého uživatele
* Bezpečně Zlepšete možnosti svých uživatelů
* Zprovoznění přehledy

## <a name="step-1---connect-to-apps-easily"></a>Krok 1 – snadné připojení k aplikacím

Propojení vašich aplikací s Azure AD, můžete zlepšit zabezpečení produktivity koncových uživatelů a povolením jednotného přihlašování (SSO) a proveďte zřizování uživatelů. Tím, že spravuje vaše aplikace na jednom místě, Azure AD, můžete minimalizovat správní režie a jediného ovládacího bodu dosáhnout souladu se zásadami zabezpečení a dodržování předpisů.

Tato část popisuje vaše možnosti pro správu přístupu uživatelů k aplikacím, povolení zabezpečeného vzdáleného přístupu a interních aplikací, o výhodách migrace aplikací do služby Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Zpřístupnění aplikace uživatelům bez problémů

Azure AD umožňuje správcům [přidat aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) do Galerie organizace aplikací v [webu Azure portal](https://portal.azure.com/). Přidání aplikací do Galerie organizace aplikace usnadňuje konfigurace aplikací, které používají Azure AD jako zprostředkovatele identity. Také umožňuje spravovat přístup uživatelů k aplikaci pomocí zásad podmíněného přístupu a nakonfigurovat jednotné přihlašování (SSO) k aplikacím tak, aby uživatelé nemuseli zadávat heslo opakovaně a automaticky přihlášeni v místním a cloudové aplikace.

Jakmile se aplikace přidávají do Galerie Azure AD, uživatelé mohou zobrazit aplikace, které jsou jim přiřazeny a hledání a požádat o další aplikace podle potřeby. Azure AD poskytuje [několik metod](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) uživatelé měli přístup k jejich aplikace:

* Přístupový panel / Moje aplikace
* Spouštěč aplikací Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy přihlašování

Další informace o přístupu uživatelů k aplikacím, najdete v článku **krok 3 – umožněte uživatelům si** v tomto článku.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrace aplikací ze služby Active Directory Federation Services do služby Azure AD

Migrace jednotné přihlašování z Active Directory Federation Services (ADFS) do služby Azure AD umožňuje další možnosti na zabezpečení, spravovatelnosti konzistentnější a spolupráci. Pro dosažení optimálních výsledků doporučujeme migrovat vaše aplikace ze služby AD FS do Azure AD. Přenesení aplikace ověřování a autorizace Azure AD poskytuje následující výhody:

* Správa nákladů
* Řízení rizik
* Zvýšení produktivity
* Adresování dodržování předpisů a zásad správného řízení

Další informace najdete v tématu [migrace aplikací v Azure Active Directory](https://aka.ms/migrateapps/whitepaper) dokument White Paper.

### <a name="enable-secure-remote-access-to-apps"></a>Povolit zabezpečený vzdálený přístup k aplikacím

[Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) poskytuje jednoduché řešení organizacím publikovat místní aplikace do cloudu pro vzdálené uživatele, kteří potřebují přístup k interním aplikacím zabezpečeným způsobem. Po jednotné přihlašování do služby Azure AD mohou uživatelé přistupovat cloudové a místní aplikace prostřednictvím externí adresy URL nebo interní aplikace portálu.

Proxy aplikací Azure AD nabízí následující výhody:

* Rozšíření Azure AD a místní prostředky
  * Zabezpečení cloudových a ochrany
  * Funkce, jako je podmíněný přístup a ověřování službou Multi-Factor Authentication, který se snadno povolit
* Žádné součásti v hraniční síti, jako je například řešení tradiční reverzního proxy serveru a sítě VPN
* Žádná příchozí připojení vyžaduje
* Jednotné přihlašování (SSO) napříč zařízeními, prostředky a aplikace v cloudu i lokálně
* Umožňuje koncovým uživatelům produktivity kdykoli a kdekoli

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Zjistit pomocí Microsoft Cloud App Security stínové IT

V moderních podnicích IT oddělení nemají často informace o všech cloudových aplikací, které jsou používány uživatelé ke své práci. Když správci IT se zobrazí výzva, kolik cloudových aplikací uvažují svým zaměstnancům používat, v průměru, Řekněme 30 nebo 40. Průměr je ve skutečnosti více než 1 000 samostatné aplikace, které se používají zaměstnanci ve vaší organizaci. 80 % zaměstnanců použijte – schválené aplikace, které nikdo zkontroloval a nemusí být kompatibilní se zásadami zabezpečení a dodržování předpisů.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) vám pomůže identifikovat užitečné aplikace, které jsou oblíbené s uživateli, že může schválit a přidat do Galerie organizace aplikace tak, aby uživatelé využít výhod funkcí, jako je jednotné přihlašování a podmíněný přístup.

*"** Cloud App Security** nástroje zase pomáhají zajistit, aby naši lidé jsou správně používali naší cloudové aplikace a aplikace SaaS, způsoby, které podporují zásady základní zabezpečení, které pomáhají chránit Accenture." *--- [John Blasi, správa, ředitel pro bezpečnost informací, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Kromě zjišťování skryté IT činnosti, můžete také MCAS určit úroveň rizika aplikací, zabránit neoprávněnému přístupu k firemním datům, úniku dat a další bezpečnostní rizika spojená s aplikací.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Krok 2: vytvoření jedna identita pro každého uživatele automaticky

Spojuje místního a cloudového adresáře hybridní řešení identit Azure AD vám umožní znovu použít místní služby Active Directory stávajícího prostředí tím, že zajistíte existující identit v cloudu. Řešení synchronizuje místních identit s Azure AD, při IT udržuje v místní službě Active Directory s všechna existující řešení zásad správného řízení jako primární zdroj informací pro identity. Azure AD od Microsoftu, hybridní řešení identit překlenuje místní a cloudové možnosti vytváření společnou uživatelskou identitu pro ověřování a autorizaci pro všechny prostředky bez ohledu na jejich umístění.

Integrace místních adresářů s Azure AD zajišťuje uživatelům vyšší produktivitu a uživatelům bránit v použití více účtů v aplikacích a službách poskytnutím společné identity pro přístup ke cloudovým i a místním prostředkům. Použití více účtů je bolavých míst pro koncové uživatele a IT Příbuzná témata. Z pohledu koncového uživatele s více účty znamená, že si museli pamatovat více hesel. Abyste tomu předešli, mnoho uživatelů znovu použít stejné heslo pro každý účet, který je chybný. z hlediska zabezpečení. Z hlediska IT opakované použití často vede k více resetování hesel a náklady na helpdesk spolu s stížností koncového uživatele.

Azure AD Connect je nástroj, který slouží k synchronizaci místních identit do služby Azure AD, který můžete použít pro přístup ke cloudovým aplikacím. Jakmile identity ve službě Azure AD, můžete zřídit k aplikacím SaaS, jako je Salesforce nebo Concur.

V této části uvádíme doporučení pro zajištění vysoké dostupnosti, moderní ověřování pro cloud a snížení nároků vaší místní.

> [!NOTE]
> Pokud chcete získat další informace o Azure AD Connect, přečtěte si téma [co je Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Nastavit pracovní server Azure AD Connect a stále aktuální

Azure AD Connect hrají klíčovou roli v procesu zřizování. Pokud synchronizační Server přejde do režimu offline z jakéhokoli důvodu, změny v místním nebude aktualizován v cloudu a způsobit potíže s přístupem k uživatelům. Je důležité definovat strategii převzetí služeb při selhání, který umožňuje správcům rychlé obnovení synchronizace po synchronizační server přejde do režimu offline.

Pro zajištění vysoké dostupnosti v události primární Azure AD Connect server přejde do offline režimu, se doporučuje nasadit samostatný [pracovní server](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) pro Azure AD Connect. Nasazení serveru umožňuje správci "podporovat" pracovní server do produkčního prostředí přepínačem jednoduché konfigurace. Pohotovostní server nakonfigurovat v pracovním režimu také umožňuje testovat a nasazovat nové změny konfigurace a zavést nový server, pokud vyřazení staré.

> [!TIP]
> Azure AD Connect je aktualizována v pravidelných intervalech. Proto důrazně doporučujeme, abyste pracovní server aktuální, aby bylo možné využít k vylepšení výkonu, opravy chyb a nové funkce, které obsahuje každá nová verze.

### <a name="enable-cloud-authentication"></a>Ověření cloudu

Organizace pomocí místní služby Active Directory by měl rozšířit svého adresáře do služby Azure AD pomocí služby Azure AD Connect a nakonfigurujte odpovídající metodu ověřování. [Výběr správné metody ověřování](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) organizace je první krok na vaší cestě přesunu aplikace do cloudu. To je zásadní součástí, protože řídí přístup ke všem data v cloudu a prostředků.

Nejjednodušší a doporučená metoda umožňující použití cloudového ověřování ve službě Azure AD pro objekty v místním adresáři je umožnit [synchronizaci hodnot Hash hesel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). Můžete také některé organizace může zvážit povolení [předávací ověřování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Ať už zvolíte PHS nebo PTA, nezapomeňte povolit [bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) umožňující uživatelům přístup ke cloudovým aplikacím bez neustále zadávání uživatelského jména a hesla v aplikaci při použití Windows 7 a 8 zařízení na podnikový síť. Bez jednotného přihlašování uživatelé musí mějte na paměti specifické pro aplikaci hesla a přihlášení do jednotlivých aplikací. Podobně IT pracovníci musí vytvářet a aktualizovat uživatelské účty pro každou aplikaci, jako je Office 365, Box nebo Salesforce. Uživatelé musí pamatovat si hesla a ztrácet čas přihlášení do jednotlivých aplikací. Poskytuje standardizované jednotné přihlašování mechanismus pro celou organizaci je zásadní pro nejlepší uživatelské prostředí, při snížení rizika, schopnost vykazovat, zásad správného řízení.

Pro organizace, které už používají služby AD FS nebo jiného místního zprostředkovatele ověřování Přesun do Azure AD jako zprostředkovatele identity můžete zjednodušení a zlepšení dostupnosti. Pokud nemáte konkrétní případy použití pro použití federace, doporučujeme migrovat z federovaného ověřování PHS a bezproblémového jednotného přihlašování nebo PTA a bezproblémového jednotného přihlašování a využijte výhod snížené místní nároky a flexibilitu, které cloud nabízí s Vylepšené uživatelské prostředí. Další informace najdete v tématu [migrace z federace na synchronizaci hodnot hash hesel pro Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Povolit automatické zrušení zřízení účtů

Povolení automatické zřizování a jeho rušení pro aplikace je nejlepší strategii pro řízení životního cyklu identit mezi různými systémy. Azure AD podporuje [automatizované, na základě zásad zřizování a rušení zřízení](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) uživatelských účtů do celé řady oblíbených aplikací SaaS, jako je například ServiceNow a Salesforce a další, které implementují [SCIM 2.0 protokol](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). Na rozdíl od tradičních řešení zřizování, které vyžadují vlastní kód nebo ruční nahrávání souborů CSV, zřizovací služba je hostovaná v cloudu a funkce předem integrovaných konektorů, které se dá nastavit a spravovat pomocí webu Azure portal. Klíčovou výhodou automatické zrušení zřízení je, že pomáhá zabezpečit svou organizaci odebráním okamžitě identit uživatelů z klíčových aplikací SaaS při opuštění organizace.

Další informace o zřizování automatické uživatelských účtů a jak to funguje, najdete v článku [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Krok 3: bezpečně Zlepšete možnosti svých uživatelů

V dnešních digitálních pracovišti je potřeba vyrovnávat zabezpečení s produktivitu. Ale koncoví uživatelé často zapnutí zpět bezpečnostní opatření, která jejich produktivitu a přístup ke cloudovým aplikacím. Abychom to vyřešit, Azure AD poskytuje samoobslužné funkce, které umožňují uživatelům produktivně pracovat současně minimalizovat správní režie.

Tato část obsahuje seznam doporučení pro odebrání řešit zádrhele spojené s tím, že vaši uživatelé přitom mimořádnou z vaší organizace.

### <a name="enable-self-service-password-reset-for-all-users"></a>Povolení samoobslužného resetování hesla pro všechny uživatele

Azure [samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) nabízí jednoduchý způsob, správci IT mohou umožnit uživatelům resetovat a odemknout svá hesla nebo účty bez zásahu správce. Tento systém zahrnuje generování podrobných sestav, které sledují, kdy uživatelé k systému přistupují, spolu s oznámeními, která upozorňují na zneužití.

Ve výchozím nastavení Azure AD odemyká účty při resetování hesla funguje. Ale při povolení služby Azure AD Connect [integrace v místním](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), máte také možnost oddělit tyto dvě operace, které umožňují uživatelům odemknout svůj účet bez nutnosti k resetování hesla.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Ujistěte se, že všichni uživatelé jsou registrovány pro vícefaktorové ověřování a samoobslužné resetování HESLA

Azure poskytuje sestavy, které je možné podle vás a vaši organizaci k zajištění, že jsou uživatelé zaregistrovaní pro vícefaktorové ověřování a samoobslužné resetování HESLA. Uživatelé, kteří se ještě nezaregistrovali může musí být poučeni o procesu.

MFA [sestavy přihlášení](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) obsahuje informace o použití vícefaktorového ověřování a poskytuje přehled o fungování vícefaktorového ověřování ve vaší organizaci. Přístup k přihlašovací aktivity (a auditů a rizikové události) pro Azure AD je zásadní pro řešení potíží, analýzy využití a napomáhá forenznímu vyšetřování tím.

Podobně [samoobslužné správy hesel sestavy](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) slouží k určení, kdo má (nebo nebyla) registrovaný pro samoobslužné resetování HESLA.

### <a name="self-service-app-management"></a>Správa samoobslužných služeb aplikací

Předtím, než vaši uživatelé samoobslužných můžete objevovat aplikace z jejich přístupovém panelu, je potřeba povolit [samoobslužného přístupu k aplikacím](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) pro všechny aplikace, které chcete povolit uživatelům samoobslužné zjišťování a žádat o přístup k. Samoobslužný přístup k aplikacím je skvělý způsob, jak povolit uživatelům samoobslužné zjišťování aplikací a volitelně povolit obchodní skupiny schvalovat přístup k těmto aplikacím. Můžete povolit obchodní skupiny pro správu přiřazené těmto uživatelům pro přihlašovací údaje [heslo jednotné přihlašování v aplikacích](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-the-application-for-password-single-sign-on) přímo ze svých přístupových panelech.

### <a name="self-service-group-management"></a>Samoobslužná správa skupin

Přiřazování uživatelů k aplikacím je nejlepší namapované při používání skupin, protože umožňují velkou flexibilitu a možnosti správy ve velkém měřítku:

* Založených na atributech pomocí členství v dynamické skupině
* Delegování pro vlastníky aplikace

Azure AD poskytuje schopnost spravovat přístup k prostředkům pomocí skupin zabezpečení a skupiny Office 365. Tyto skupiny můžou spravovat skupiny vlastníka, který můžete schválit nebo odmítnout žádosti o členství a delegovat řízení členství ve skupině. Říká [Samoobslužná správa skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), tato funkce umožňuje vlastníkům, kteří nemají přiřazenou roli správce můžete vytvořit a spravovat skupiny, aniž byste museli spoléhat na správcům zpracování požadavků na jejich šetří čas.

## <a name="step-4---operationalize-your-insights"></a>Krok 4: zprovoznění přehledy

Auditování a protokolování událostí týkajících se zabezpečení a souvisejících výstrah jsou základní součástí efektivní strategie zajistíte, že uživatelé produktivně pracovat a vaše organizace je zabezpečená. Protokoly zabezpečení a sestavy vám můžou pomoct zodpovědět dotazy jako:

* Používáte, budete platit za?
* Je něco podezřelého nebo škodlivý, ve vašem tenantovi dochází?
* Kdo se to týká během bezpečnostní incident?

Protokoly zabezpečení a sestavy poskytují elektronických záznamů podezřelé aktivity a rozpoznat vzory, které může znamenat pokus o nebo úspěšné externí průniku sítě a útoky interních nápovědy. Můžete použít auditování k monitorování aktivity uživatelů, dodržování legislativních předpisů dokumentu, forenzní analýzy a další. Výstrahy poskytují oznámení událostí zabezpečení.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Přiřazení rolí nejnižším oprávněním správce pro operace

Pokud přemýšlíte o váš přístup k operacím, existuje několik úrovní správy, které byste měli zvážit. První úroveň zátěž správy na globální správci. Vždy používat roli globálního správce, může být vhodné pro menší společnosti. Ale pro velké organizace s pracovníky technické podpory a správci za konkrétní úlohy, přiřazení role Globální správce může být ohrožení zabezpečení od poskytuje tyto jednotlivce umožňuje spravovat úkoly, které jsou nad i nad rámec Co jsou by měl být schopný udělat.

V takovém případě byste měli zvážit další úroveň správy. Používání služby Azure AD, můžete určit koncovým uživatelům jako "omezené administrators", kteří můžou spravovat úkoly v méně privilegovaným rolím. Například můžete přiřadit vaši pomoc, pracovníci helpdesku [Čtenář zabezpečení](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) role a umožnit jim možnost spravovat funkce související se zabezpečením s přístupem jen pro čtení. Nebo možná dává smysl přiřadit [Správce ověřování](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) role jednotlivcům jim dát možnost resetovat přihlašovací údaje bez hesla nebo načíst a nakonfigurovat služby Azure Service Health.

Další informace najdete v tématu [oprávnění role správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorování hybridních součásti (Azure AD Connect sync, služby AD FS) pomocí služby Azure AD Connect Health

Azure AD Connect a AD FS jsou důležité součásti, které může potenciálně narušit správy životního cyklu a ověřování a nakonec vést k výpadkům. Proto byste měli nasadit Azure AD Connect Health pro monitorování a generování sestav těchto součástí.

Další informace, získáte další [monitorování AD FS pomocí služby Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Použití Azure monitoru ke shromažďování dat protokolů pro analýzu

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) je jednotné monitorování portál pro všechny protokoly služby Azure AD, která poskytuje podrobný přehled pokročilých analýz a inteligentního strojového učení. Díky nástroji Azure Monitor můžete využívat metrik a protokolů na portálu a prostřednictvím rozhraní API a získat větší přehled o stavu a výkonu svých prostředků. Umožňuje podokně ze skla prostředí v rámci portálu přitom širokou škálu integrace produktů prostřednictvím rozhraní API a datových možností exportu, které podporují tradičních systémů SIEM třetích stran. Azure Monitor také poskytuje možnost konfigurovat pravidla upozornění upozorňování a automatizovaných akcí týkající se dopadu na vaše prostředky.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Vytvořit vlastní řídicí panely pro vaše vedení a pracovního dne na den

Organizace, které nemají řešení SIEM můžete stáhnout [balíček obsahu Power BI](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) pro službu Azure AD. Power BI content pack obsahuje předdefinované sestavy, které vám pomohou pochopit, jakým způsobem uživatelé přijímají a používat funkce služby Azure AD, který umožňuje získat přehled o všech aktivitách v adresáři. Můžete také vytvořit svoje vlastní [vlastní řídicí panel](https://docs.microsoft.com/power-bi/service-dashboards) a sdílet se svým týmem vedení hlásit v každodenní aktivity. Řídicí panely jsou skvělý způsob, jak monitorovat vaše podnikání a vidět všechny nejdůležitější metriky na první pohled. Vizualizace na řídicím panelu můžou pocházet z podkladové datové sadě jeden nebo více a nebo mnoha podkladových sestav jeden. Řídicí panel kombinuje místní a Cloudová data, takže poskytuje ucelený pohled bez ohledu na to, kde se data nacházejí.

![Vlastní řídicí panel Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Pochopení vašeho ovladače volání podpory

Při implementaci řešení s hybridní identitou jak je uvedeno v tomto článku, měli byste nakonec zaznamenat snížení ve volání podpory. Běžné problémy, jako je zapomenutí hesel a uzamčení účtu byly omezeny implementace resetování hesla pomocí samoobslužné služby Azure, při povolení samoobslužného přístupu k aplikacím umožňuje samoobslužné zjišťování a žádat o přístup k aplikacím bez nutnosti spoléhat se na vaši pracovníci IT.

Pokud není sledovat redukce volání podpory, doporučujeme analyzovat ovladače volání podpory v pokus o potvrzení, pokud samoobslužné resetování HESLA nebo samoobslužného přístupu k aplikacím není nakonfigurovaná správně, nebo pokud není nějakým novým problémům, které mohou být systematicky vyřeší.

*"V naší digitální transformace cesty, potřebujeme spolehlivé identita a přístup poskytovatele správy k usnadnění bezproblémové ještě zabezpečenou integraci mezi USA, partnery a poskytovatelů cloudových služeb pro efektivní ekosystém; Azure AD je nejlepší možností nabídky nám potřebné funkce a viditelnosti, který nám umožňuje detekovat a reagovat na rizika."* --- [Aramex Yazan Almasri, globální ředitel zabezpečení informací,](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Sledování využití aplikací k získání přehledů

Kromě zjišťování stínového IT, monitorování využití aplikací v rámci vaší organizace pomocí [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) může vaší organizaci pomoct při přesunu do plně využít potenciál cloudových aplikací. Může to pomoci si zachovat kontrolu nad vaše prostředky pomocí lepší přehled o aktivitě a zvýšení ochrany důležitých dat napříč cloudovými aplikacemi. Sledování využití aplikací ve vaší organizaci pomocí MCAS vám můžou dát odpověď na následující otázky:

* Jaké neschválené aplikace zaměstnanci používají k ukládání dat v?
* Kde a kdy je citlivá data se ukládají v cloudu?
* Kdo přistupuje k citlivá data v cloudu?

*"S Cloud App Security, můžete rychle detekovat anomálie a provádět akci."* --- [Eric LePenske, Senior Manager, informace o zabezpečení, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Souhrn

Existuje mnoho aspektů implementace hybridní řešení identit, ale tento krok čtyři kontrolní seznam vám pomůže rychle provést infrastruktura identity, která umožní uživatelům vyšší produktivitu a zabezpečení.

* Snadné připojení k aplikacím
* Automaticky vytvořit jednu identitu pro každého uživatele
* Bezpečně Zlepšete možnosti svých uživatelů
* Zprovoznění přehledy

Doufáme, že tento dokument je užitečné návod k vytvoření základ využívá silné identity pro vaši organizaci.

## <a name="identity-checklist"></a>Kontrolní seznam identit

Doporučujeme vám vytisknout následující kontrolní seznam pro referenci podle začněte svou cestu k více solidní základ identity ve vaší organizaci.

### <a name="today"></a>Dnes

|Hotovo?|Položka|
|:-|:-|
||Pilotní Self – služby heslo resetovat (SSPR) pro skupinu|
||Monitorování komponenty hybridního používání služby Azure AD Connect Health|
||Přiřazení rolí nejnižším oprávněním správce pro operaci|
||Zjistit pomocí Microsoft Cloud App Security stínové IT|
||Použití Azure monitoru ke shromažďování dat protokolů pro analýzu|

### <a name="next-two-weeks"></a>Další dva týdny

|Hotovo?|Položka|
|:-|:-|
||Zpřístupnění aplikace uživatelům|
||Pilotní zřizování Azure AD pro aplikaci SaaS podle výběru|
||Pracovní server nastavit pro Azure AD Connect a stále aktuální|
||Zahájení migrace aplikací ze služby AD FS do Azure AD|
||Vytvořit vlastní řídicí panely pro vaše vedení a pracovního dne na den|

### <a name="next-month"></a>Příští měsíc

|Hotovo?|Položka|
|:-|:-|
||Sledování využití aplikací k získání přehledů|
||Pilotní zabezpečený vzdálený přístup k aplikacím|
||Ujistěte se, že všichni uživatelé jsou registrovány pro vícefaktorové ověřování a samoobslužné resetování HESLA|
||Ověření cloudu|

### <a name="next-three-months"></a>Následující tři měsíce

|Hotovo?|Položka|
|:-|:-|
||Povolení správy aplikací samoobslužných služeb|
||Povolit samoobslužnou správu skupin|
||Sledování využití aplikací k získání přehledů|
||Pochopení vašeho ovladače volání podpory|

## <a name="next-steps"></a>Další postup

Zjistěte, jak můžete zvýšit tak zabezpečené stav pomocí možností Azure Active Directory a kontrolní seznam pěti krocích - [zabezpečení vaší infrastruktury identit v pěti krocích](https://aka.ms/securitysteps).

Zjistěte, jak funkce identity ve službě Azure AD vám může pomoci Zrychlete svůj přechod do cloudu tím, že poskytuje řešení a funkcí, které umožňují organizacím rychle přijmout a přesunout informace z jejich Správa identit od tradiční správy řídí v místních systémech pro Azure AD – [Azure AD poskytuje řídí správu cloudu pro místní úlohy](https://aka.ms/cloudgoverned).
