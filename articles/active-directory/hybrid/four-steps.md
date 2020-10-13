---
title: Čtyři kroky pro silnou identitu Foundation – Azure AD
description: Toto téma popisuje čtyři kroky, které zákazníci s Hybrid identity můžou využít k vytvoření silného základu identity.
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
ms.openlocfilehash: 4543a46c9362ac6d20cc7597de9f59c1a7520163
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600792"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Čtyři kroky pro silnou identitu Foundation s Azure Active Directory

Správa přístupu k aplikacím a datům už nemůže spoléhat na tradiční strategie hraničních zabezpečení sítě, jako jsou hraniční sítě a brány firewall, kvůli rychlému přesunu aplikací do cloudu. Organizace musí své řešení identit důvěřovat, abyste mohli řídit, kdo a co má přístup k aplikacím a datům organizace. Víc organizací umožňuje zaměstnancům přinášet si vlastní zařízení, aby fungovala, a využívat jejich zařízení odkudkoli, kde se můžou připojovat k Internetu. Zajištění dodržování předpisů a zabezpečení těchto zařízení se stává důležitým aspektem řešení identity, které organizace zvolí k implementaci. V dnešním digitálním pracovišti [je identitou primární Řídicí rovina](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) každé organizace, která přesouvá do cloudu.

Při přijímání řešení hybridní identity pro Azure Active Directory (Azure AD) získají organizace přístup k funkcím úrovně Premium, které mají možnost odemknout produktivitu prostřednictvím automatizace, delegování, samoobslužné služby a možností jednotného přihlašování. Umožňuje vašim pracovníkům přístup k prostředkům společnosti z libovolného místa, kde potřebují svou práci, a umožňuje tak vašemu IT týmu řídit přístup tím, že zajistí, aby měli oprávnění ke zvýšení produktivity správný přístup ke správným prostředkům.

Tento kontrolní seznam osvědčených postupů vám pomůže rychle nasadit doporučené akce pro vytvoření *silného* základu identity ve vaší organizaci, a to na základě našich kurzů:

* Snadné připojení k aplikacím
* Vytvořit jednu identitu pro každého uživatele automaticky
* Zabezpečená podpora pro uživatele
* Zprovoznění své přehledy

## <a name="step-1---connect-to-apps-easily"></a>Krok 1 – snadné připojení k aplikacím

Připojením vašich aplikací ke službě Azure AD můžete zlepšit produktivitu a zabezpečení koncového uživatele tím, že povolíte jednotné přihlašování (SSO) a provedete zřizování uživatelů. Když spravujete aplikace na jednom místě, Azure AD, můžete minimalizovat režijní náklady na správu a dosáhnout jediného kontrolního bodu pro zásady zabezpečení a dodržování předpisů.

Tato část obsahuje informace o možnostech správy přístupu uživatelů k aplikacím, povolení zabezpečeného vzdáleného přístupu k interním aplikacím a výhodám migrace vašich aplikací do Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Bezproblémové zpřístupnění aplikací pro uživatele

Azure AD umožňuje správcům [přidávat](../manage-apps/add-application-portal.md) do galerie podnikových aplikací aplikace v [Azure Portal](https://portal.azure.com/). Přidávání aplikací do galerie podnikových aplikací usnadňuje konfiguraci aplikací pro použití Azure AD jako zprostředkovatele identity. Umožňuje taky spravovat přístup uživatelů k aplikaci pomocí zásad podmíněného přístupu a nakonfigurovat jednotné přihlašování (SSO) k aplikacím, aby uživatelé nemuseli zadávat hesla opakovaně a automaticky se přihlásili k místním i cloudovým aplikacím.

Jakmile se aplikace přidají do galerie služby Azure AD, uživatelé uvidí aplikace, které jsou jim přiřazené, a podle potřeby vyhledají a požadují jiné aplikace. Azure AD poskytuje pro uživatele [několik způsobů](../manage-apps/end-user-experiences.md) přístupu ke svým aplikacím:

* Přístupový panel/Moje aplikace
* Spouštěč aplikací Microsoft 365
* Přímé přihlášení k federovaným aplikacím
* Přímé přihlašovací odkazy

Další informace o přístupu uživatelů k aplikacím najdete v **kroku 3 – podpora uživatelů** v tomto článku.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrace aplikací z Active Directory Federation Services (AD FS) do Azure AD

Migrace konfigurace jednotného přihlašování z Active Directory Federation Services (AD FS) (ADFS) do služby Azure AD umožňuje využívat další možnosti zabezpečení, jednotnější možnosti správy a spolupráce. Pro dosažení optimálních výsledků doporučujeme migrovat aplikace z AD FS do služby Azure AD. Ověřování a autorizace vaší aplikace v Azure AD vám poskytne následující výhody:

* Správa nákladů
* Řízení rizik
* Zvýšení produktivity
* Řešení dodržování předpisů a zásad správného řízení

Další informace najdete v tématu [migrace vašich aplikací do Azure Active Directory](https://aka.ms/migrateapps/whitepaper) dokumentů White Paper.

### <a name="enable-secure-remote-access-to-apps"></a>Povolit zabezpečený vzdálený přístup k aplikacím

[Azure proxy aplikací služby AD](../manage-apps/what-is-application-proxy.md) poskytuje organizacím jednoduché řešení pro publikování místních aplikací do cloudu pro vzdálené uživatele, kteří potřebují zabezpečený přístup k interním aplikacím. Po jednotném přihlášení ke službě Azure AD budou mít uživatelé přístup k cloudovým i místním aplikacím prostřednictvím externích adres URL nebo interního portálu aplikací.

Azure Proxy aplikací služby AD nabízí následující výhody:

* Rozšíření Azure AD na místní prostředky
  * Zabezpečení a ochrana v cloudovém měřítku
  * Funkce, jako je podmíněný přístup a Multi-Factor Authentication, které je snadné povolit
* Žádné součásti v hraniční síti, například VPN a tradiční řešení reverzního proxy serveru
* Nejsou požadována žádná příchozí připojení.
* Jednotné přihlašování (SSO) v rámci zařízení, prostředků a aplikací v cloudu i v místním prostředí
* Umožňuje koncovým uživatelům, aby byli produktivní kdykoli a kdekoli

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Objevte stínové IT pomocí Microsoft Cloud App Security

V moderních podnicích IT oddělení často nevědí o všech cloudových aplikacích, které používají uživatelé ke své práci. Když správci IT žádají, aby si využívali, kolik cloudových aplikací se jejich zaměstnanci považují, v průměru říká 30 nebo 40. Ve skutečnosti je průměr více než 1 000 samostatných aplikací používaných zaměstnanci ve vaší organizaci. 80% zaměstnanců používá neschválené aplikace, které nikdo nezkontroloval a nemusí být v souladu se zásadami zabezpečení a dodržování předpisů.

[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) vám může pomoct identifikovat užitečné aplikace, které jsou oblíbené s uživateli, které může schválit a přidat do galerie podnikových aplikací, aby uživatelé využili výhod funkcí, jako je jednotné přihlašování a podmíněný přístup.

<em>"**Cloud App Security** nám pomáhá zajistit, aby naši lidé správně používali naše cloudové a SaaS aplikace, a to díky způsobem, který podporuje základní zásady zabezpečení, které pomáhají chránit Accenture."</em> --- [Jan Blasi, Správa ředitele, zabezpečení informací, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Kromě detekce stínového IT může MCAS také určit úroveň rizika aplikací, zabránit neoprávněnému přístupu k podnikovým datům, možnému úniku dat a dalším bezpečnostním rizikům, která jsou v aplikacích spojená.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Krok 2 – vytvoření jedné identity pro každého uživatele automaticky

Místní a cloudové adresáře společně v řešení hybridních identit Azure AD vám umožní znovu využít stávající investice do místní služby Active Directory tím, že zřídí vaše stávající identity v cloudu. Řešení synchronizuje místní identity s Azure AD, a udržuje místní službu Active Directory běžící se všemi stávajícími řešeními zásad správného řízení jako primární zdroj pravdy pro identity. Řešení hybridních identit v Azure AD se zakládá na místních i cloudových možnostech a vytváří společnou identitu uživatelů pro ověřování a autorizaci pro všechny prostředky bez ohledu na jejich umístění.

Integrace místních adresářů se službou Azure AD umožňuje uživatelům zvýšit produktivitu a zabránit uživatelům v používání několika účtů napříč aplikacemi a službami tím, že poskytuje společnou identitu pro přístup k cloudovým i místním prostředkům. Použití více účtů je pro koncové uživatele bodovým bodem a je to podobně. V perspektivě pro koncové uživatele má několik účtů pamatovat si více hesel. Aby k tomu nedocházelo, mnoho uživatelů znovu používá stejné heslo pro každý účet, který je z hlediska zabezpečení špatný. Z perspektivy IT často opakované použití vede k většímu počtu resetování hesel a nákladům na helpdesk spolu s reklamami koncového uživatele.

Azure AD Connect je nástroj, který se používá k synchronizaci místních identit s Azure AD, které se pak dají použít pro přístup k cloudovým aplikacím. Jakmile jsou identity ve službě Azure AD, můžou zřídit SaaS aplikace, jako je Salesforce nebo Concur.

V této části uvádíme doporučení pro zajištění vysoké dostupnosti, moderního ověřování pro Cloud a omezení vašich místních nároků.

> [!NOTE]
> Pokud chcete získat další informace o Azure AD Connect, přečtěte si téma [co je Azure AD Connect synchronizace?](./how-to-connect-sync-whatis.md)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Nastavte přípravný Server pro Azure AD Connect a udržujte si ho v aktuálním stavu.

Azure AD Connect hraje klíčovou roli v procesu zřizování. Pokud se synchronizační Server z jakéhokoli důvodu přepne do offline režimu, změny v místním prostředí se v cloudu neaktualizují a můžou uživatelům způsobit problémy s přístupem. Je důležité definovat strategii převzetí služeb při selhání, která umožňuje správcům rychle obnovit synchronizaci po přechodu synchronizačního serveru do režimu offline.

Pro zajištění vysoké dostupnosti v případě přechodu primárního Azure AD Connect serveru do režimu offline doporučujeme, abyste nasadili samostatný [přípravný Server](./how-to-connect-sync-staging-server.md) pro Azure AD Connect. Nasazení serveru umožňuje správci "zvýšit úroveň" přípravného serveru do produkčního prostředí pomocí jednoduchého přepínače konfigurace. Pokud máte pohotovostní server nakonfigurovaný v pracovním režimu, můžete také testovat a nasazovat nové změny konfigurace a zavést nový server, pokud vyřadíte starou jinou.

> [!TIP]
> Azure AD Connect se pravidelně aktualizují. Proto důrazně doporučujeme, abyste zachovali aktuální pracovní server, abyste mohli využívat vylepšení výkonu, opravy chyb a nové funkce, které poskytuje každá nová verze.

### <a name="enable-cloud-authentication"></a>Povolit cloudové ověřování

Organizace s místní službou Active Directory by měly svůj adresář do Azure AD zvětšit pomocí Azure AD Connect a nakonfigurovat příslušnou metodu ověřování. [Výběr správné metody ověřování](./choose-ad-authn.md) pro vaši organizaci je prvním krokem ve vaší cestě k přesunutí aplikací do cloudu. Je to kritická součást, protože řídí přístup ke všem cloudovým datům a prostředkům.

Nejjednodušší a doporučená metoda pro povolení cloudového ověřování pro místní adresářové objekty ve službě Azure AD je povolení [synchronizace hodnot hash hesel](./how-to-connect-password-hash-synchronization.md) (kosmetice). Případně můžou některé organizace zvážit povolení [předávacího ověřování](./how-to-connect-pta-quick-start.md) (PTA).

Ať už zvolíte KOSMETICE nebo PTA, Nezapomeňte povolit [bezproblémové jednotné přihlašování](./how-to-connect-sso.md) umožňující uživatelům přístup ke cloudovým aplikacím bez neustálého zadávání uživatelského jména a hesla do aplikace při použití zařízení se systémem Windows 7 a 8 ve vaší podnikové síti. Bez jednotného přihlašování si uživatelé musí pamatovat hesla specifická pro aplikace a přihlašovat se k jednotlivým aplikacím. Stejně tak pracovníci IT potřebují vytvářet a aktualizovat uživatelské účty pro každou aplikaci, například Microsoft 365, box nebo Salesforce. Uživatelé si musí pamatovat hesla a zatěžovat si čas potřebný k přihlášení ke každé aplikaci. Zajištění standardizovaného mechanismu jednotného přihlašování pro celou firmu je klíčové pro nejlepší uživatelské prostředí, snížení rizika, schopnost vykazovat a zásad správného řízení.

Pro organizace, které už používají AD FS nebo jiného místního zprostředkovatele ověřování, se přesunete do Azure AD, protože váš poskytovatel identity může snížit složitost a zlepšit dostupnost. Pokud nemáte konkrétní případy použití pro použití federace, doporučujeme migrovat z federovaného ověřování na KOSMETICE a bezproblémové jednotné přihlašování nebo PTA a bezproblémové jednotné přihlašování, které přináší výhody omezených místních nároků a flexibilitu cloudu Díky vylepšeným uživatelským prostředím. Další informace najdete v článku [migrace z federace na synchronizaci hodnot hash hesel pro Azure Active Directory](./plan-migrate-adfs-password-hash-sync.md).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Povolit automatické zrušení zřizování účtů

Povolení automatického zřizování a zrušení zřizování pro vaše aplikace je nejlepší strategií pro řízení životního cyklu identit napříč různými systémy. Azure AD podporuje [automatizované zřizování a rušení zřizování na základě zásad](../app-provisioning/configure-automatic-user-provisioning-portal.md) pro celou řadu oblíbených aplikací SaaS, jako je ServiceNow a Salesforce, a dalších, které implementují [protokol SCIM 2,0](../app-provisioning/use-scim-to-provision-users-and-groups.md). Na rozdíl od tradičních zřizovacích řešení, která vyžadují vlastní kód nebo ruční nahrávání souborů CSV, je služba zřizování hostována v cloudu a funkce předem integrovaných konektorů, které je možné nastavit a spravovat pomocí Azure Portal. Klíčovou výhodou automatického zrušení zřizování je to, že pomáhá zabezpečit vaši organizaci tím, že okamžitě odebere identity uživatelů z aplikací služby Key SaaS, když opustí organizaci.

Další informace o automatickém zřizování uživatelských účtů a o tom, jak funguje, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů při SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="step-3---empower-your-users-securely"></a>Krok 3 – zajištění bezpečného zabezpečení uživatelů

V dnešním digitálním pracovišti je důležité zajistit rovnováhu mezi zabezpečením a produktivitou. Koncoví uživatelé se ale často dodávají zpátky na bezpečnostní opatření, která zpomalují svou produktivitu a přístup ke cloudovým aplikacím. Abychom to mohli vyřešit, Azure AD poskytuje samoobslužné funkce, které uživatelům umožňují udržet si produktivitu při minimalizaci administrativní režie.

V této části jsou uvedena doporučení pro odstranění tření z vaší organizace tím, že vašim uživatelům umožníte zbývající ostražití.

### <a name="enable-self-service-password-reset-for-all-users"></a>Povolit Self-Service resetování hesla pro všechny uživatele

[Samoobslužné resetování hesla](../authentication/tutorial-enable-sspr.md) (SSPR) v Azure nabízí správcům IT jednoduchý způsob, jak uživatelům dovolit resetování a odemknutí hesel nebo účtů bez zásahu správce. Tento systém zahrnuje generování podrobných sestav, které sledují, kdy uživatelé k systému přistupují, spolu s oznámeními, která upozorňují na zneužití.

Ve výchozím nastavení Azure AD odemkne účty, když provede resetování hesla. Pokud však Azure AD Connect integraci povolíte [místně](../authentication/concept-sspr-howitworks.md#on-premises-integration), máte také možnost oddělit tyto dvě operace, které uživatelům umožňují odemknout svůj účet, aniž by museli resetovat heslo.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Zajistěte registraci všech uživatelů pro MFA a SSPR

Azure poskytuje sestavy, které vám i vaše organizace můžou použít k zajištění registrace uživatelů pro MFA a SSPR. Uživatelé, kteří si nezaregistrovali registraci, mohou být muset být na tomto procesu informováni.

[Sestava přihlášení](../authentication/howto-mfa-reporting.md) MFA obsahuje informace o využití MFA a poskytuje přehled o tom, jak vícefaktorové ověřování funguje ve vaší organizaci. Přístup k aktivitě přihlašování (a auditům a detekcím rizik) pro Azure AD je zásadní pro řešení potíží, analýzu využití a Forenzní vyšetřování.

Podobně je možné pomocí [sestavy samoobslužné správy hesel](../authentication/howto-sspr-reporting.md) určit, kdo má (nebo nemá) zaregistrovaný pro SSPR.

### <a name="self-service-app-management"></a>Samoobslužná správa aplikací

Než budou moct vaši uživatelé sami zjišťovat aplikace z přístupového panelu, musíte povolit [Samoobslužný přístup](../manage-apps/access-panel-manage-self-service-access.md) k aplikacím, u kterých chcete uživatelům umožnit, aby si mohli sami vyhledat a požádat o přístup. Samoobslužný přístup k aplikacím je skvělý způsob, jak uživatelům povolit, aby si mohli sami zjistit aplikace a volitelně povolit obchodní skupině schvalovat přístup k těmto aplikacím. Obchodní skupině můžete dovolit, aby uživatelé spravovali přihlašovací údaje přiřazené k [heslům Single-Sign v aplikacích](../manage-apps/application-sign-in-problem-password-sso-gallery.md#configure-password-sso-for-an-azure-ad-gallery-app) přímo ze svých přístupových panelů.

### <a name="self-service-group-management"></a>Samoobslužná správa skupin

Přiřazování uživatelů k aplikacím je při používání skupin nejlépe namapované, protože umožňují značnou flexibilitu a možnost správy ve velkém měřítku:

* Založený na atributech pomocí dynamického členství ve skupině
* Delegování pro vlastníky aplikace

Azure AD poskytuje možnost spravovat přístup k prostředkům pomocí skupin zabezpečení a skupin Microsoft 365. Tyto skupiny může spravovat vlastník skupiny, který může schvalovat nebo zamítnout žádosti o členství a delegovat řízení členství ve skupině. Tato funkce se označuje jako [Samoobslužná správa skupin](../users-groups-roles/groups-self-service-management.md), ale šetří čas tím, že umožňuje vlastníkům skupin, kteří nejsou přiřazeni administrativní roli k vytváření a správě skupin, aniž by museli spoléhat na to, že požádají správce o zpracování požadavků.

## <a name="step-4---operationalize-your-insights"></a>Krok 4 – zprovoznění vašich přehledů

Auditování a protokolování událostí souvisejících se zabezpečením a souvisejících výstrah jsou základními součástmi efektivní strategie, které zajistí, že uživatelé budou mít produktivitu a vaše organizace bude zabezpečená. Dotazy a protokoly zabezpečení můžou přispět k zodpovězení otázek, jako je:

* Používáte k čemu platíte?
* Dochází v mém tenantovi k nějakému podezřelému nebo škodlivému chování?
* Na koho byl dopad v případě incidentu zabezpečení?

Protokoly zabezpečení a sestavy poskytují elektronický záznam o podezřelých aktivitách a umožňují detekovat vzory, které mohou označovat pokus nebo úspěšné vnější průnik sítě a interní útoky. Pomocí auditování můžete sledovat činnost uživatelů, dokumentovat dodržování předpisů, provádět analýzu forenzní a provádět další akce. Výstrahy obsahují oznámení o událostech zabezpečení.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Přiřaďte k operacím alespoň role privilegovaného správce.

Jak si myslíte o přístupu k operacím, existuje několik úrovní správy, které je potřeba vzít v úvahu. Na první úrovni se zátěž správy na vašich globálních správcích umístí. Používání role globálního správce může být vždy vhodné pro menší společnosti. U větších organizací, které jsou zodpovědné s pracovníky helpdesku a správci, kteří jsou zodpovědní za konkrétní úkoly, ale přiřazení role globálního správce může představovat bezpečnostní riziko, protože poskytuje těmto jednotlivcům možnost spravovat výše uvedené úkoly a jejich rámec.

V takovém případě byste měli zvážit další úroveň správy. Pomocí Azure AD můžete určit koncové uživatele jako "omezené správce", kteří můžou spravovat úlohy v méně privilegovaných rolích. Například můžete přiřadit pracovníky helpdesku roli [Čtenář zabezpečení](../users-groups-roles/directory-assign-admin-roles.md#security-reader) a poskytnout jim možnost spravovat funkce související se zabezpečením s přístupem jen pro čtení. Případně je vhodné přiřadit roli [Správce ověřování](../users-groups-roles/directory-assign-admin-roles.md#authentication-administrator) jednotlivcům, kteří jim poskytnou možnost resetovat přihlašovací údaje, které nepoužívají heslo, nebo číst a konfigurovat Azure Service Health.

Další informace najdete v tématu [oprávnění role správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorování hybridních součástí (Azure AD Connect synchronizace AD FS) pomocí Azure AD Connect Health

Azure AD Connect a AD FS jsou důležité součásti, které mohou potenciálně poškodit správu a ověřování životního cyklu a nakonec vést k výpadkům. Proto byste měli nasadit Azure AD Connect Health pro monitorování a vytváření sestav těchto komponent.

Pokud se chcete dozvědět víc, přečtěte si téma [monitorování AD FS pomocí Azure AD Connect Health](./how-to-connect-health-adfs.md).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Použití Azure Monitor ke shromažďování datových protokolů pro analýzy

[Azure monitor](../../azure-monitor/overview.md) je jednotný portál monitorování pro všechny protokoly služby Azure AD, které poskytují podrobné přehledy, pokročilou analýzu a inteligentní strojové učení. Pomocí Azure Monitor můžete využívat metriky a protokoly v rámci portálu a prostřednictvím rozhraní API získat lepší přehled o stavu a výkonu svých prostředků. Umožňuje v rámci portálu jedno podokno skla a zároveň umožňuje široké spektrum integrace produktů prostřednictvím rozhraní API a možností exportu dat, které podporují tradiční systémy SIEM třetích stran. Azure Monitor vám taky umožní nakonfigurovat pravidla výstrah, aby se zobrazila oznámení nebo aby se na problémech, které mají vliv na prostředky, mohla provádět automatické akce.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Vytváření vlastních řídicích panelů pro svůj vedoucí a den do dne

Organizace, které nemají řešení SIEM, můžou stáhnout [Power BI balíčku obsahu](../reports-monitoring/howto-use-azure-monitor-workbooks.md) pro Azure AD. Balíček obsahu Power BI obsahuje předem připravené sestavy, které vám pomůžou pochopit, jak uživatelé přijímají a používají funkce Azure AD, což vám umožní získat přehled o všech aktivitách v adresáři. Můžete také vytvořit vlastní [řídicí panel](/power-bi/service-dashboards) a sdílet ho s týmem vedoucím k hlášení každodenních aktivit. Řídicí panely jsou skvělým způsobem, jak monitorovat vaši firmu a na první pohled vidíte všechny nejdůležitější metriky. Vizualizace na řídicím panelu můžou pocházet z jedné nebo mnoha podkladových datových sad a z jedné nebo mnoha podkladových sestav. Řídicí panel kombinuje místní a cloudová data, a nabízí tak celkový přehled bez ohledu na to, kde jsou původní data.

![Power BI vlastní řídicí panel](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Pochopení ovladačů volání podpory

Když implementujete řešení hybridní identity, jak je uvedeno v tomto článku, měli byste si všimnout, že se vám budou poznamenat snížení počtu volání podpory. Běžné problémy, jako jsou zapomenutá hesla a uzamčení účtů, jsou omezeny implementací samoobslužného resetování hesla Azure a povolení samoobslužného přístupu aplikací umožňuje uživatelům samoobslužně zjišťovat a žádat o přístup k aplikacím, aniž by se museli spoléhat na pracovníky IT.

Pokud nesledujete volání podpory, doporučujeme analyzovat ovladače volání podpory v rámci pokusu o potvrzení, jestli byl SSPR nebo Samoobslužný přístup k aplikacím správně nakonfigurovaný, nebo jestli existují nějaké jiné nové problémy, které se dají systematicky řešit.

*"V naší digitální transformaci jsme potřebovali spolehlivý poskytovatel pro správu identit a přístupu, aby bylo možné bezproblémově zabezpečit integraci mezi námi, partnery a poskytovateli cloudových služeb, a to za účelem efektivního ekosystému. Azure AD je nejlepší možností, jak nám nabídnout potřebné možnosti a viditelnost, které nám umožnily detekovat rizika a reagovat na ně. "* --- [Yazan almasri, ředitel pro zabezpečení globálních informací, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitorování využití aplikací pro účely řízení Insights

Kromě zjišťování stínového IT můžete monitorovat využití aplikací v rámci vaší organizace, a to pomocí [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) může vaší organizaci pomáhat při přesunu, abyste mohli plně využít příslib cloudových aplikací. Může vám to usnadnit kontrolu nad prostředky prostřednictvím lepší viditelnosti aktivity a zvýšit ochranu důležitých dat napříč cloudovým aplikacím. Sledování využití aplikací ve vaší organizaci pomocí MCAS vám může pomoci zodpovědět následující otázky:

* Jaké neschválené aplikace zaměstnanci používají k ukládání dat?
* Kde a kdy je citlivá data ukládána v cloudu?
* Kdo přistupuje k citlivým datům v cloudu?

*"Díky Cloud App Security můžeme rychle odhalit anomálie a provést akci."* --- [Eric LePenske, vedoucí správce, zabezpečení informací, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Shrnutí

Existuje mnoho aspektů implementace řešení hybridní identity, ale tento podrobný kontrolní seznam vám pomůže rychle provést infrastrukturu identit, která umožní uživatelům zvýšit produktivitu a zabezpečení.

* Snadné připojení k aplikacím
* Vytvořit jednu identitu pro každého uživatele automaticky
* Zabezpečená podpora pro uživatele
* Zprovoznění své přehledy

Doufáme, že tento dokument je užitečným plánem pro vytvoření silného základu identity pro vaši organizaci.

## <a name="identity-checklist"></a>Kontrolní seznam identity

Doporučujeme vytisknout následující kontrolní seznam, který bude odkazovat na vaši cestu k více Solid Identity Foundation ve vaší organizaci.

### <a name="today"></a>Dnes

|Hotovo?|Položka|
|:-|:-|
||Pilotní Samoobslužné resetování hesla (SSPR) pro skupinu|
||Monitorování hybridních komponent pomocí Azure AD Connect Health|
||Přiřaďte k operaci alespoň role privilegovaného správce.|
||Objevte stínové IT pomocí Microsoft Cloud App Security|
||Pomocí Azure Monitor shromažďovat protokoly dat pro účely analýzy|

### <a name="next-two-weeks"></a>Další dva týdny

|Hotovo?|Položka|
|:-|:-|
||Zpřístupnění aplikace pro uživatele|
||Pilotní zřízení Azure AD pro aplikaci SaaS s volbou|
||Nastavte pracovní server pro Azure AD Connect a udržujte si ho v aktuálním stavu.|
||Spuštění migrace aplikací ze služby ADFS do Azure AD|
||Vytváření vlastních řídicích panelů pro svůj vedoucí a den do dne|

### <a name="next-month"></a>Příští měsíc

|Hotovo?|Položka|
|:-|:-|
||Monitorování využití aplikací pro účely řízení Insights|
||Pilotní zabezpečený vzdálený přístup k aplikacím|
||Zajistěte registraci všech uživatelů pro MFA a SSPR|
||Povolit cloudové ověřování|

### <a name="next-three-months"></a>Další tři měsíce

|Hotovo?|Položka|
|:-|:-|
||Povolit samoobslužnou správu aplikací|
||Povolit samoobslužnou správu skupin|
||Monitorování využití aplikací pro účely řízení Insights|
||Pochopení ovladačů volání podpory|

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak můžete zvýšit zabezpečení stav pomocí možností Azure Active Directory a tohoto kontrolního seznamu pěti kroků – [pět kroků k zabezpečení vaší infrastruktury identity](https://aka.ms/securitysteps).

Přečtěte si, jak funkce identity v Azure AD vám pomůžou zrychlit přechod na správu na základě cloudu tím, že poskytují řešení a možnosti, které organizacím umožňují rychle přijmout a přesunout další správu identit z tradičních místních systémů do Azure AD – [jak Azure AD poskytuje cloudovou správu pro místní úlohy](https://aka.ms/cloudgoverned).