---
title: Čtyři kroky k vytvoření základu silné identity – Azure AD
description: Toto téma popisuje čtyři kroky hybridní identity, které mohou zákazníci podniknout k vytvoření pevného základu identity.
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
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206785"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Čtyři kroky k vytvoření silného základu identity pomocí Služby Azure Active Directory

Správa přístupu k aplikacím a datům se již nemůže spoléhat na tradiční strategie hraničního zabezpečení sítě, jako jsou hraniční sítě a brány firewall, a to z důvodu rychlého pohybu aplikací do cloudu. Organizace teď musí důvěřovat svému identitovanému řešení, aby mohly řídit, kdo a co má přístup k aplikacím a datům organizace. Více organizací umožňuje zaměstnancům, aby si přinesli vlastní zařízení do práce a používali svá zařízení odkudkoli, kde se mohou připojit k internetu. Zajištění, že tato zařízení jsou kompatibilní a zabezpečená, se stalo důležitým aspektem v řešení identit, které se organizace rozhodne implementovat. Na dnešním digitálním pracovišti [je identita primární řídicí rovinou](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) každé organizace, která se stěhuje do cloudu.

Při přijímání řešení hybridní identity služby Azure Active Directory (Azure AD) získají organizace přístup k prémiovým funkcím, které odemknou produktivitu prostřednictvím automatizace, delegování, samoobslužných služeb a možností jednotného přihlašování. Umožňuje vašim pracovníkům přístup k firemním zdrojům z místa, kde potřebují dělat svou práci, a zároveň umožňuje vašemu IT týmu řídit tento přístup tím, že zajistí, aby ti správní lidé měli správný přístup ke správným zdrojům pro zajištění bezpečné produktivity.

Na základě našich poznatků vám tento kontrolní seznam doporučených postupů pomůže rychle nasadit doporučené akce k vytvoření *silného* základu identity ve vaší organizaci:

* Snadné připojení k aplikacím
* Automatické vytvoření jedné identity pro každého uživatele
* Zabezpečte zabezpečení svým uživatelům
* Zprovozněte své přehledy

## <a name="step-1---connect-to-apps-easily"></a>Krok 1 – Snadné připojení k aplikacím

Propojením aplikací s Azure AD můžete zvýšit produktivitu a zabezpečení koncových uživatelů povolením jednotného přihlašování (SSO) a zřizováním uživatelů. Díky správě aplikací na jednom místě, Azure AD, můžete minimalizovat administrativní režii a dosáhnout jediného bodu kontroly pro zásady zabezpečení a dodržování předpisů.

Tato část popisuje možnosti správy přístupu uživatelů k aplikacím, povolení zabezpečeného vzdáleného přístupu k interním aplikacím a výhody migrace aplikací do Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Bezproblémová zpřístupnění aplikací uživatelům

Azure AD umožňuje správcům [přidávat aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) do galerie podnikových aplikací na webu [Azure Portal](https://portal.azure.com/). Přidání aplikací do galerie podnikových aplikací usnadňuje konfiguraci aplikací tak, aby používaly Azure AD jako poskytovatele identity. Umožňuje také spravovat přístup uživatelů k aplikaci pomocí zásad podmíněného přístupu a konfigurovat jednotné přihlašování (SSO) pro aplikace tak, aby uživatelé nemuseli zadávat svá hesla opakovaně a byli automaticky přihlášeni k místním i místním. cloudových aplikací.

Po přidání aplikací do galerie Azure AD můžou uživatelé zobrazit aplikace, které jsou jim přiřazené, a podle potřeby vyhledávat a požadovat další aplikace. Azure AD poskytuje [několik metod](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) pro uživatele přístup k jejich aplikacím:

* Přístupový panel/Moje aplikace
* Spouštěč aplikací Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé přihlašovací odkazy

Další informace o přístupu uživatelů k aplikacím najdete v **tématu Krok 3 – Umocněte uživatele** v tomto článku.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrace aplikací ze služby AD Federitu do Služby Azure AD

Migrace konfigurace jednotného přihlášení ze služby ADFS (ADFS) do služby Azure AD umožňuje další funkce zabezpečení, konzistentnější možnosti správy a spolupráci. Pro dosažení optimálních výsledků doporučujeme migrovat aplikace ze služby AD FS do Služby Azure AD. Uvedení vašeho ověřování a autorizace aplikace do Azure AD vám poskytuje následující výhody:

* Správa nákladů
* Řízení rizik
* Zvýšení produktivity
* Řešení dodržování předpisů a řízení

Další informace najdete v dokumentu migrace aplikací do služby White Paper [služby Azure Active Directory.](https://aka.ms/migrateapps/whitepaper)

### <a name="enable-secure-remote-access-to-apps"></a>Povolení zabezpečeného vzdáleného přístupu k aplikacím

[Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) poskytuje jednoduché řešení pro organizace publikovat místní aplikace do cloudu pro vzdálené uživatele, kteří potřebují přístup k interním aplikacím bezpečným způsobem. Po jednotném přihlášení k Azure AD mají uživatelé přístup ke cloudovým i místním aplikacím prostřednictvím externích adres URL nebo interního portálu aplikací.

Azure AD Application Proxy nabízí následující výhody:

* Rozšíření Azure AD na místní prostředky
  * Zabezpečení a ochrana v cloudovém měřítku
  * Funkce, jako je podmíněný přístup a vícefaktorové ověřování, které lze snadno povolit
* Žádné součásti v hraniční síti, jako je VPN a tradiční řešení reverzníproxy
* Není vyžadováno žádné příchozí připojení.
* Jednotné přihlašování (SSO) napříč zařízeními, prostředky a aplikacemi v cloudu i v místním prostředí
* Umožňuje koncovým uživatelům být produktivní kdykoli a kdekoli

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Objevte stínové IT pomocí zabezpečení aplikací Microsoft Cloud App Security

V moderních podnicích si IT oddělení často nejsou vědoma všech cloudových aplikací, které uživatelé používají ke své práci. Když se správci IT získají, kolik cloudových aplikací podle nich jejich zaměstnanci používají, v průměru říkají 30 nebo 40. Ve skutečnosti je průměr více než 1 000 samostatných aplikací používaných zaměstnanci ve vaší organizaci. 80 % zaměstnanců používá neschválené aplikace, které nikdo nezkontroloval a nemusí být v souladu s vašimi zásadami zabezpečení a dodržování předpisů.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) vám může pomoci identifikovat užitečné aplikace, které jsou oblíbené u uživatelů, které it může sankcionovat a přidat do galerie podnikových aplikací, aby uživatelé mohli využívat funkce, jako je sociální zabezpečení a podmíněný přístup.

<em>"**Cloud App Security** nám pomáhá zajistit, aby naši lidé správně používali naše cloudové aplikace a aplikace SaaS, a to způsobem, který podporuje základní zásady zabezpečení, které pomáhají chránit Accenture."</em> --- [John Blasi, generální ředitel pro bezpečnost informací, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Kromě detekce stínovéHO IT může mcas také určit úroveň rizika aplikací, zabránit neoprávněnému přístupu k podnikovým datům, možnému úniku dat a dalším bezpečnostním rizikům spojeným s aplikacemi.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Krok 2 – Vytvoření jedné identity pro každého uživatele automaticky

Spojení místních a cloudových adresářů do hybridního identitového řešení Azure AD vám umožní znovu použít vaše stávající místní investice do služby Active Directory zřízením vašich stávajících identit v cloudu. Řešení synchronizuje místní identity s Azure AD, zatímco IT udržuje místní službu Active Directory spuštěnou s existujícími řešeními zásad správného řízení jako primárním zdrojem pravdy pro identity. Řešení hybridní identity Azure AD společnosti Microsoft zahrnuje místní a cloudové funkce a vytváří společnou identitu uživatele pro ověřování a autorizaci pro všechny prostředky bez ohledu na jejich umístění.

Integrace místních adresářů s Azure AD činí vaše uživatele produktivnější a zabraňuje uživatelům používat více účtů napříč aplikacemi a službami tím, že poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Použití více účtů je bolest bod pro koncové uživatele a IT podobně. Z pohledu koncového uživatele znamená mít více účtů nutnost zapamatovat si více hesel. Aby se tomu zabránilo, mnoho uživatelů znovu použít stejné heslo pro každý účet, což je špatné z hlediska zabezpečení. Z hlediska IT opakované použití často vede k dalším resetováním hesla a nákladům na helpdesk spolu se stížnostmi koncových uživatelů.

Azure AD Connect je nástroj, který se používá pro synchronizaci místních identit do Azure AD, který pak můžete použít pro přístup ke cloudovým aplikacím. Jakmile jsou identity ve službě Azure AD, mohou zřídit saas aplikace jako Salesforce nebo Concur.

V této části uvádíme doporučení pro poskytování vysoké dostupnosti, moderní ověřování pro cloud a snížení vaší místní nároky na půdorys.

> [!NOTE]
> Pokud se chcete dozvědět víc o Azure AD Connect, přečtěte [si, co je Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Nastavení pracovního serveru pro Azure AD Connect a jeho aktuální nastavení

Azure AD Connect hraje klíčovou roli v procesu zřizování. Pokud synchronizační server přejde z nějakého důvodu do režimu offline, změny v místním prostředí se v cloudu neaktualizují a způsobí uživatelům problémy s přístupem. Je důležité definovat strategii převzetí služeb při selhání, která správcům umožní rychle obnovit synchronizaci po přepnesynchronizační server do režimu offline.

Chcete-li poskytnout vysokou dostupnost v případě, že váš primární server Azure AD Connect přejde do režimu offline, doporučujeme nasadit samostatný [pracovní server](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) pro Azure AD Connect. Nasazení serveru umožňuje správci "povýšit" pracovní server do produkčního prostředí jednoduchým konfiguračním přepínačem. S pohotovostní server nakonfigurován v pracovním režimu také umožňuje testovat a nasazovat nové změny konfigurace a zavést nový server, pokud vyřazení starého.

> [!TIP]
> Azure AD Connect se pravidelně aktualizuje. Proto důrazně doporučujeme zachovat pracovní server aktuální, abyste mohli využít vylepšení výkonu, opravy chyb a nové funkce, které každá nová verze poskytuje.

### <a name="enable-cloud-authentication"></a>Povolení cloudového ověřování

Organizace s místní službou Active Directory by měly rozšířit svůj adresář do služby Azure AD pomocí služby Azure AD Connect a nakonfigurovat příslušnou metodu ověřování. [Výběr správné metody ověřování](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) pro vaši organizaci je prvním krokem na cestě k přesunu aplikací do cloudu. Je to kritická součást, protože řídí přístup ke všem cloudovým datům a prostředkům.

Nejjednodušší a doporučenou metodou pro povolení cloudového ověřování pro místní objekty adresáře ve službě Azure AD je povolení [synchronizace hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). Některé organizace mohou také zvážit povolení [předávacího ověřování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Ať už si vyberete PHS nebo PTA, nezapomeňte povolit [bezproblémové jednotné přihlašování,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) abyste uživatelům umožnili přístup ke cloudovým aplikacím, aniž byste při používání zařízení se systémem Windows 7 a 8 v podnikové síti neustále zavádali své uživatelské jméno a heslo do aplikace. Bez jednotného přihlášení si uživatelé musí pamatovat hesla specifická pro aplikaci a přihlásit se do každé aplikace. Stejně tak pracovníci IT potřebují vytvořit a aktualizovat uživatelské účty pro každou aplikaci, jako je Office 365, Box a Salesforce. Uživatelé si musí pamatovat svá hesla a navíc trávit čas přihlášením ke každé aplikaci. Poskytování standardizovaného jednotného přihlašovacího mechanismu pro celý podnik je zásadní pro nejlepší uživatelské prostředí, snížení rizika, schopnost podávat zprávy a zásadsprávné řízení.

Pro organizace, které už používají službu AD FS nebo jiného místního poskytovatele ověřování, může přechod na Azure AD jako poskytovatele identity snížit složitost a zlepšit dostupnost. Pokud nemáte konkrétní případy použití pro použití federace, doporučujeme migrovat z federovaného ověřování buď phs a bezproblémové jednotné přihlašování nebo PTA a bezproblémové jednotné přihlašování, abyste mohli využívat výhod snížené místní nároky a flexibility, kterou cloud nabízí lepší uživatelské prostředí. Další informace naleznete v [tématu Migrace synchronizace hash dat ve federaci do hesla pro službu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Povolit automatické zrušení zřízení účtů

Povolení automatického zřizování a deprovisioning u vašich aplikací je nejlepší strategie pro řízení životního cyklu identit ve více systémech. Azure AD podporuje [automatické zřizování založené na zásadách a zrušení zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) uživatelských účtů do různých oblíbených aplikací SaaS, jako jsou ServiceNow a Salesforce, a další, které implementují [protokol SCIM 2.0](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). Na rozdíl od tradičních zřizovacích řešení, která vyžadují vlastní kód nebo ruční nahrávání souborů CSV, zřizovací služba je hostovaná v cloudu a obsahuje předem integrované konektory, které lze nastavit a spravovat pomocí portálu Azure. Klíčovou výhodou automatického zrušení zřizování je, že pomáhá zabezpečit vaši organizaci okamžitým odebráním identit uživatelů z klíčových aplikací SaaS při odchodu z organizace.

Další informace o automatickém zřizování uživatelských účtů a jeho fungování najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Krok 3 – Ušetři své uživatele bezpečně

Na dnešním digitálním pracovišti je důležité vyvážit zabezpečení produktivitou. Koncoví uživatelé však často tlačí zpět na bezpečnostní opatření, která zpomalují jejich produktivitu a přístup ke cloudovým aplikacím. Chcete-li tento problém vyřešit, Azure AD poskytuje samoobslužné funkce, které umožňují uživatelům zůstat produktivní při minimalizaci administrativní režie.

V této části jsou uvedena doporučení pro odstranění tření z vaší organizace tím, že uživatelům zmocníte a zároveň zůstanete ostražití.

### <a name="enable-self-service-password-reset-for-all-users"></a>Povolit samoobslužné resetování hesla pro všechny uživatele

[Samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) Azure (SSPR) nabízí správcům IT jednoduchý způsob, jak uživatelům umožnit resetovat a odemknout hesla nebo účty bez zásahu správce. Tento systém zahrnuje generování podrobných sestav, které sledují, kdy uživatelé k systému přistupují, spolu s oznámeními, která upozorňují na zneužití.

Ve výchozím nastavení Azure AD odemkne účty při obnovení hesla. Pokud však povolíte integraci Azure AD Connect [místně](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), máte také možnost oddělit tyto dvě operace, které uživatelům umožňují odemknout svůj účet bez nutnosti resetovat heslo.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Ujistěte se, že všichni uživatelé jsou registrováni pro vícefaktorové a jednotné přihrádky

Azure poskytuje sestavy, které můžete použít vy a vaše organizace k zajištění, že uživatelé jsou registrováni pro vícefaktorové zabezpečení a sspr. Uživatelé, kteří se nezaregistrovali, nemusí být o tomto procesu vzděláváni.

[Sestava přihlášení vícefaktorové registrace](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) obsahuje informace o využití vícefaktorové žádosti a poskytuje přehled o tom, jak vícefaktorové informace ve vaší organizaci fungují. Přístup k přihlašovací aktivitě (a auditům a detekci rizik) pro Azure AD je zásadní pro řešení potíží, analýzy využití a forenzní vyšetřování.

Podobně [samoobslužné správě hesel sestavy](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) lze určit, kdo má (nebo nebyl) registrována pro samoobslužné resetování hesla.

### <a name="self-service-app-management"></a>Samoobslužná správa aplikací

Než budou uživatelé moci sami zjišťovat aplikace ze svého přístupového panelu, musíte povolit [samoobslužný přístup k aplikacím,](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) které chcete uživatelům umožnit vlastní zjišťování a vyžádání přístupu. Samoobslužný přístup k aplikacím je skvělý způsob, jak uživatelům umožnit vlastní zjišťování aplikací a volitelně umožnit obchodní skupině schvalovat přístup k těmto aplikacím. Obchodní skupině můžete povolit správu přihlašovacích údajů přiřazených těmto uživatelům pro [aplikace s jedním přihlášením hesla](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) přímo z jejich přístupových panelů.

### <a name="self-service-group-management"></a>Samoobslužná správa skupin

Přiřazování uživatelů k aplikacím je při použití skupin nejlépe mapováno, protože umožňují velkou flexibilitu a schopnost správy ve velkém měřítku:

* Na základě atributů pomocí dynamického členství ve skupině
* Delegování na vlastníky aplikací

Azure AD poskytuje možnost spravovat přístup k prostředkům pomocí skupin zabezpečení a skupin Office 365. Tyto skupiny mohou spravovat vlastník skupiny, který může schválit nebo odepřít žádosti o členství a delegovat řízení členství ve skupině. Tato funkce, známá jako [samoobslužná správa skupin,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)šetří čas tím, že umožňuje vlastníkům skupin, kterým není přiřazena role pro správu, vytvářet a spravovat skupiny, aniž by museli spoléhat na správce při zpracování jejich požadavků.

## <a name="step-4---operationalize-your-insights"></a>Krok 4 – Zprovoznit své přehledy

Auditování a protokolování událostí souvisejících se zabezpečením a souvisejících výstrah jsou základními součástmi efektivní strategie, která zajistí, že uživatelé zůstanou produktivní a vaše organizace bude zabezpečená. Protokoly zabezpečení a sestavy mohou pomoci odpovědět na otázky, jako jsou:

* Používáš to, za co platíš?
* Děje se v mém tenantovi něco podezřelého nebo škodlivého?
* Kdo byl ovlivněn během bezpečnostního incidentu?

Protokoly zabezpečení a sestavy poskytují elektronický záznam podezřelých aktivit a pomáhají vám odhalit vzorce, které mohou naznačovat pokus o nebo úspěšné externí proniknutí do sítě a interní útoky. Auditování můžete použít ke sledování aktivity uživatelů, dodržování předpisů dokumentů, forenzní analýze a dalším činnostem. Výstrahy poskytují oznámení o událostech zabezpečení.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Přiřazení role správce s nejnižšími oprávněními pro operace

Jak si myslíte o svém přístupu k operacím, existuje několik úrovní správy, aby zvážila. První úroveň klade břemeno správy na globální správce.First Level klade břemeno správy na globální správce .) Vždy použití role globálního správce může být vhodné pro menší společnosti. Ale pro větší organizace s pracovníky technické podpory a administrátory odpovědnými za konkrétní úkoly může být přiřazení role globálního správce bezpečnostním rizikem, protože poskytuje těmto osobám možnost spravovat úkoly, které jsou nad rámec co by měli být schopni dělat.

V takovém případě byste měli zvážit další úroveň správy. Pomocí Azure AD můžete označit koncové uživatele jako "omezené správce", kteří mohou spravovat úkoly v méně privilegovaných rolích. Můžete například přiřadit pracovníky technické podpory roli [čtečky zabezpečení,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) která jim poskytne možnost spravovat funkce související se zabezpečením s přístupem jen pro čtení. Nebo možná má smysl přiřadit roli [správce ověřování](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) jednotlivcům, aby mohli resetovat přihlašovací údaje bez hesla nebo číst a konfigurovat Azure Service Health.

Další informace najdete [v tématu Oprávnění rolí správce ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorování hybridních komponent (synchronizace Azure AD Connect, služba AD FS) pomocí služby Azure AD Connect Health

Azure AD Connect a AD FS jsou důležité součásti, které mohou potenciálně přerušit správu životního cyklu a ověřování a nakonec vést k výpadkům. Proto byste měli nasadit Azure AD Connect Health pro monitorování a vykazování těchto součástí.

Další informace najdete v textu [na čtení monitoru Služby AD FS pomocí služby Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Použití Azure Monitoru ke shromažďování datových protokolů pro analýzy

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) je jednotný portál monitorování pro všechny protokoly Azure AD, který poskytuje podrobné přehledy, pokročilé analýzy a inteligentní strojové učení. Pomocí Azure Monitoru můžete využívat metriky a protokoly v rámci portálu a prostřednictvím api získat větší přehled o stavu a výkonu vašich prostředků. Umožňuje jediné podokno zkušeností se sklem v rámci portálu a zároveň umožňuje širokou škálu integrací produktů prostřednictvím rozhraní API a možností exportu dat, které podporují tradiční systémy SIEM třetích stran. Azure Monitor také umožňuje nakonfigurovat pravidla výstrah tak, aby byla upozorňována, nebo provést automatické akce v problémech, které mají vliv na vaše prostředky.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Vytvořte vlastní řídicí panely pro vaše vedení a každodenní nastavení

Organizace, které nemají řešení SIEM, si můžou stáhnout [balíček Obsahu Power BI](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) pro Azure AD. Sada obsahu Power BI obsahuje předem vymyšlené sestavy, které vám pomohou pochopit, jak uživatelé přijímají a používají funkce Azure AD, což vám umožní získat přehled o všech aktivitách ve vašem adresáři. Můžete si také vytvořit vlastní [řídicí panel](https://docs.microsoft.com/power-bi/service-dashboards) a sdílet je se svým vedoucím týmem a podávat zprávy o každodenních aktivitách. Řídicí panely jsou skvělým způsobem, jak sledovat vaši firmu a vidět všechny nejdůležitější metriky na první pohled. Vizualizace na řídicím panelu můžou pocházet z jedné nebo mnoha podkladových datových sad a z jedné nebo mnoha podkladových sestav. Řídicí panel kombinuje místní a cloudová data, a nabízí tak celkový přehled bez ohledu na to, kde jsou původní data.

![Vlastní řídicí panel Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Porozumějte ovladači volání podpory

Při implementaci řešení hybridní identity, jak je popsáno v tomto článku, byste měli nakonec oznámení snížení volání podpory. Běžné problémy, jako jsou zapomenutá hesla a uzamčení účtů, jsou zmírněny implementací samoobslužného resetování hesla Azure a zároveň povolení samoobslužného přístupu k aplikacím umožňuje uživatelům samostatně zjišťovat a požadovat přístup k aplikacím bez spoléhání se na vaše IT pracovníky.

Pokud nezaznamenáte snížení počtu volání podpory, doporučujeme analyzovat ovladače volání podpory ve snaze potvrdit, zda byl správně nakonfigurován přístup k samoobslužným spojvám nebo samoobslužným aplikacím nebo zda existují další nové problémy, které lze systematicky Určeno.

*"Na naší cestě za digitální transformací jsme potřebovali spolehlivého poskytovatele správy identity a přístupu, abychom usnadnili bezproblémovou a bezpečnou integraci mezi námi, partnery a poskytovateli cloudových služeb pro efektivní ekosystém; Azure AD byla nejlepší možnost, která nám nabízí potřebné funkce a viditelnost, které nám umožnily detekovat rizika a reagovat na ně."* --- [Yazan Almasri, ředitel pro globální bezpečnost informací, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Sledování používání aplikací pro získání přehledů

Kromě zjišťování StínovéHO IT může sledování využití aplikací v celé organizaci pomocí [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) pomoci vaší organizaci při přechodu plně využívat výhod příslibu cloudových aplikací. Může vám pomoci udržet si kontrolu nad vašimi prostředky díky lepšímu přehledu o aktivitě a zvýšení ochrany důležitých dat napříč cloudovými aplikacemi. Sledování využití aplikací ve vaší organizaci pomocí mcas vám může pomoci odpovědět na následující otázky:

* Do jakých neschválených aplikací zaměstnanci ukládají data?
* Kde a kdy jsou citlivá data uložena v cloudu?
* Kdo přistupuje k citlivým datům v cloudu?

*"Díky cloudovému zabezpečení aplikací můžeme rychle rozpoznat anomálie a podniknout kroky."* --- [Eric LePenske, senior manažer pro bezpečnost informací, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Souhrn

Implementace hybridního řešení identity má mnoho aspektů, ale tento kontrolní seznam ve čtyřech krocích vám pomůže rychle dosáhnout infrastruktury identit, která uživatelům umožní zvýšit produktivitu a zabezpečení.

* Snadné připojení k aplikacím
* Automatické vytvoření jedné identity pro každého uživatele
* Zabezpečte zabezpečení svým uživatelům
* Zprovozněte své přehledy

Doufáme, že tento dokument je užitečným plánem pro vytvoření silného základu identity pro vaši organizaci.

## <a name="identity-checklist"></a>Kontrolní seznam identity

Doporučujeme vytisknout následující kontrolní seznam pro referenci, jak začnete cestu k pevnější identity nadace ve vaší organizaci.

### <a name="today"></a>Dnes

|Hotovo?|Položka|
|:-|:-|
||Samoobslužné resetování hesla pilotního objektu (SSPR) pro skupinu|
||Monitorování hybridních komponent pomocí služby Azure AD Connect Health|
||Přiřazení role správce s nejnižšími oprávněními pro operaci|
||Objevte stínové IT pomocí zabezpečení aplikací Microsoft Cloud App Security|
||Použití Azure Monitoru ke shromažďování datových protokolů pro analýzu|

### <a name="next-two-weeks"></a>Další dva týdny

|Hotovo?|Položka|
|:-|:-|
||Zpřístupnění aplikace uživatelům|
||Pilotní zřizování Azure AD pro aplikaci SaaS dle vlastního výběru|
||Nastavení pracovního serveru pro Azure AD Connect a jeho aktuální nastavení|
||Zahájení migrace aplikací z ADFS do Azure AD|
||Vytvořte vlastní řídicí panely pro vaše vedení a každodenní nastavení|

### <a name="next-month"></a>Příští měsíc

|Hotovo?|Položka|
|:-|:-|
||Sledování používání aplikací pro získání přehledů|
||Pilotní zabezpečení vzdáleného přístupu k aplikacím|
||Ujistěte se, že všichni uživatelé jsou registrováni pro vícefaktorové a jednotné přihrádky|
||Povolení cloudového ověřování|

### <a name="next-three-months"></a>Další tři měsíce

|Hotovo?|Položka|
|:-|:-|
||Povolení samoobslužné správy aplikací|
||Povolení samoobslužné správy skupin|
||Sledování používání aplikací pro získání přehledů|
||Porozumějte ovladači volání podpory|

## <a name="next-steps"></a>Další kroky

Zjistěte, jak můžete zvýšit své bezpečné držení těla pomocí funkcí služby Azure Active Directory a tohoto pětistupňového kontrolního seznamu – [pět kroků k zabezpečení infrastruktury identit](https://aka.ms/securitysteps).

Zjistěte, jak funkce identit ve službě Azure AD vám můžou pomoci urychlit přechod na správu řízenou cloudem tím, že vám poskytnou řešení a funkce, které organizacím umožní rychle přijmout a přesunout více svých správě identit z tradičních místních systémů do Azure AD – [Jak Azure AD poskytuje správu řízenou cloudem pro místní úlohy](https://aka.ms/cloudgoverned).
