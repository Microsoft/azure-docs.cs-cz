---
title: Plánování nasazení přístupového panelu Azure Active Directory
description: Pokyny k nasazení funkcí přístupového panelu Azure AD
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448af7075297c2b18df1eceaeaac34d5da762489
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576521"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Plánování nasazení přístupového panelu Azure Active Directory

Azure Active Directory přístupový panel je webový portál, který vám umožní snížit náklady na podporu, zvýšit produktivitu a zabezpečení a snížit frustrace uživatele. Systém zahrnuje podrobné vytváření sestav, které sledují, kdy uživatel přistupuje k systému, a upozorňuje správce na zneužití nebo zneužití.

Panel pro přístup k Azure Active Directory umožňuje uživatelům:

* Objevte a získejte přístup ke všem podnikovým Azure Active Directory připojeným prostředkům, jako jsou aplikace.
* Vyžádejte si přístup k novým aplikacím a skupinám nebo spravujte přístup k těmto prostředkům pro ostatní.
* Spravujte nastavení samoobslužného hesla a nastavení služby Multi-Factor Authentication.
* Spravujte svá zařízení.

Umožňuje správcům taky spravovat tyto možnosti:

* Podmínky služby
* Organizace
* Kontroly přístupu


## <a name="benefits-of-azure-active-directory-access-panel-integration"></a>Výhody integrace přístupového panelu Azure Active Directory

Přístupový panel Microsoft Azure Active Directory (Azure AD) přináší výhody firmám následujícími způsoby:

**Poskytuje intuitivní uživatelské prostředí**: Povolení přístupového panelu poskytuje uživatelům možnost mít pro všechny své aplikace s jedním přihlašováním v rámci služby Azure Single-signed na jednom panelu. Jak se přidávají možnosti, jako je Správa skupin a Samoobslužné resetování hesla, uživatelé budou mít k dispozici sjednocený portál, aby tato nastavení našli. Intuitivní prostředí umožní uživatelům rychleji se vrátit a zvýšit produktivitu a zároveň snížit jejich frustrace.

**Zvýšení produktivity**: Všechny uživatelské aplikace na přístupovém panelu mají povolený jednotné přihlašování (SSO). Povolení jednotného přihlašování pro podnikové aplikace a Office 365 poskytuje nadřazené možnosti přihlašování pro stávající uživatele, snižování nebo eliminaci dalších výzev k přihlášení. Přístupový panel umožňuje samoobslužné a dynamické členství a vylepšuje celkové zabezpečení vašeho systému identity tím, že zajišťuje správným lidem přístup k aplikacím. Přístupový panel slouží jako souvislá cílová stránka pro uživatele k rychlému vyhledání prostředků a pokračování pracovních úloh.

**Spravuje náklady**: Povolení přístupového panelu s Azure Active Directory může umožňovat dopravení místních infrastruktur. Díky tomu, že uživatelé budou mít konzistentní portál pro hledání všech svých aplikací, vyžádat přístup k prostředkům a spravovat jejich účet, snížíte tím náklady na podporu.

**Zvyšuje flexibilitu a zabezpečení**: Přístupový panel umožňuje přístup k zabezpečení a flexibilitě, které poskytuje cloudová platforma. Správci můžou snadno změnit nastavení pro aplikace a prostředky a přizpůsobit nové požadavky na zabezpečení, aniž by to ovlivnilo uživatele.

**Umožňuje robustní auditování a sledování využití**: Auditování a sledování využití pro všechny funkce koncového uživatele vám umožní zjistit, že uživatelé používají své prostředky, a vy budete mít jistotu, že budete zabezpečení hodnotit.

### <a name="licensing-considerations"></a>Požadavky na licencování

Přístupový panel je zdarma a nevyžaduje žádné licence pro použití v rámci základních uživatelů. Nicméně počet objektů ve vašem adresáři a funkce, které chcete nasadit, můžou vyžadovat další licence. Mezi běžné scénáře služby Azure AD patří následující funkce zabezpečení, které mají licenční požadavky.

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Členství na základě skupin](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Samoobslužný požadavek na heslo](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Ochrana identity](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Přečtěte si [Průvodce úplnými licencemi pro Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Předpoklady pro nasazení přístupového panelu Azure AD

Následující požadavky musí být dokončeny před začátkem tohoto projektu.

* [Integrace jednotného přihlašování k aplikacím](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Infrastruktura uživatelů a skupin Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Plánování nasazení přístupového panelu Azure AD

Následující tabulka popisuje klíčové případy použití pro nasazení přístupového panelu:

| Oblast| Popis |
| - | - |
| Access| Portál přístupového panelu je přístupný z firemních i osobních zařízení v podnikové síti. |
|Access | Portál přístupového panelu je přístupný pro podniková zařízení mimo podnikovou síť. |
| Auditování| Data o využití se stáhnou do podnikových systémů aspoň každých 29 dní. |
| Zásady správného řízení| Životní cyklus přiřazení uživatelů k aplikacím a skupinám připojeným k Azure AD se definuje a monitoruje. |
| Zabezpečení| Přístup k prostředkům je řízen pomocí přiřazení uživatelů a skupin. Přístup k prostředkům můžou spravovat jenom autorizovaní uživatelé. |
| Výkon| Časové osy šíření přístupu k přiřazení jsou zdokumentovány a monitorovány. |
| Činnost koncového uživatele| Uživatelé mají informace o možnostech přístupového panelu a o tom, jak je používat.|
| Činnost koncového uživatele| Uživatelé můžou svým uživatelům samoobslužně spravovat přístup k aplikacím a skupinám.|
| Činnost koncového uživatele| Uživatelé můžou spravovat svoje účty. |
| Činnost koncového uživatele| Uživatelé jsou vědomi kompatibility prohlížeče. |
| Podpora| Uživatelé můžou najít podporu pro problémy s přístupovým panelem. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Osvědčené postupy pro nasazení přístupového panelu Azure AD

Funkce přístupového panelu se dá povolit postupně. Doporučujeme následující pořadí nasazení:

1. Moje aplikace
   * Spouštěč aplikace
   * Samoobslužná správa aplikací
   * Integrace systém Microsoft Office 365

1. Samoobslužné zjišťování aplikací
   * Samoobslužné resetování hesla
   * Nastavení Multi-Factor Authentication
   * Správa zařízení
   * Podmínky použití
   * Správa organizací

1. Moje skupiny
   * Samoobslužná správa skupin
1. Kontroly přístupu
   * Správa kontroly přístupu

Počínaje aplikací Moje aplikace zavádí uživatele na portál jako běžné místo pro přístup k prostředkům. Přidání samoobslužného zjišťování aplikací do prostředí moje aplikace Moje skupiny a kontroly přístupu sestavují funkce samoobslužné služby.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Plánování konfigurací pro přístupový panel Azure AD

Následující tabulka uvádí několik důležitých konfigurací přístupových panelů a typické hodnoty, které můžete použít:

| Konfiguraci| Typické hodnoty |
| - | - |
| Určení pilotních skupin| Identifikujte skupinu zabezpečení Azure AD, která se má použít, a ujistěte se, že všichni pilotní členové jsou součástí skupiny. |
| Určete skupinu nebo skupiny, které mají být povoleny v produkčním prostředí.| Identifikujte skupiny zabezpečení Azure AD nebo skupiny služby Active Directory synchronizované se službou Azure AD, které se mají použít. Zajistěte, aby všichni pilotní členové byli součástí skupiny. |
| Umožňuje uživatelům používat jednotné přihlašování k typům aplikací.| Federované jednotné přihlašování, OAuth, heslo jednotného přihlašování, App proxy |
| Povolení uživatelům používat Samoobslužné resetování hesla| Ano |
| Umožňuje uživatelům používat vícefaktorové ověřování.| Ano |
| Umožňuje uživatelům používat samoobslužnou správu skupin pro typy skupin.| Skupiny zabezpečení, skupiny O365 |
| Umožňuje uživatelům používat samoobslužnou správu aplikací.| Ano |
| Umožňuje uživatelům používat kontroly přístupu.| Ano |

### <a name="plan-consent-strategy"></a>Strategie souhlasu s plánem

Uživatelé nebo správci musí souhlasit s podmínkami použití a zásadami ochrany osobních údajů aplikace. Pokud je to možné pro vaše obchodní pravidla, doporučujeme použít souhlas správce, který uživatelům poskytuje lepší prostředí.

Pokud chcete použít souhlas správce, musíte být globální správce tenanta a aplikace musí být:

* Registrováno ve vašem tenantovi nebo

* Registrováno v jiném tenantovi služby Azure AD a dříve bylo posláno alespoň jedním koncovým uživatelem.

Další informace najdete v tématu [Konfigurace způsobu, jakým koncoví uživatelé souhlasí s aplikací v Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne, protože neshodují očekávání na dopad, výsledek a odpovědnosti. Chcete-li se těmto nástrah vyhnout, [Ujistěte se, že jste připravují správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md) a že role účastníků v projektu jsou dobře pochopitelné.

### <a name="plan-communications"></a>Plánování komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně Informujte uživatele, jak a kdy se jejich prostředí změní a jak v případě potřeby získat podporu.

I když přístupový panel většinou nevytváří problémy s uživatelem, je důležité ho připravit. Před spuštěním vytvořte příručky a seznam všech prostředků pro pracovníky podpory.

#### <a name="communications-templates"></a>Šablony komunikace

Microsoft nabízí [přizpůsobitelné šablony pro e-maily a další komunikace](https://aka.ms/APTemplates) pro přístupový panel. Tyto prostředky můžete přizpůsobit pro použití v jiných komunikačních kanálech podle potřeby vaší podnikové jazykové verze.

## <a name="plan-your-sso-configuration"></a>Plánování konfigurace jednotného přihlašování

Když se uživatel přihlásí k aplikaci, projde procesem ověřování a musí prokázat, kdo jsou. Bez jednotného přihlašování je heslo uložené v aplikaci a uživatel musí znát toto heslo. Přihlašovací údaje uživatelů jednotného přihlašování (SSO) se předávají do aplikace, takže nemusejí znovu zadávat hesla pro každou aplikaci.

Pokud chcete spouštět aplikace v mých aplikacích, musí být pro aplikace povolené jednotné přihlašování (SSO).

Azure AD podporuje tři různé způsoby, jak povolit [jednotné přihlašování k aplikacím](what-is-single-sign-on.md):

* **Federované jednotné přihlašování** 
    * Umožňuje aplikaci přesměrovat na ověření uživatele na Azure AD, a to místo na dotazování na heslo. 
    * Je podporováno pro aplikace používající protokoly, jako je SAML 2,0, WS-Federation nebo OpenID Connect, a je nejrozsáhlejším režimem jednotného přihlašování.

* **Jednotné přihlašování založené na heslech** 
    * Umožňuje zabezpečené ukládání hesel aplikací a přehrávání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. 
    * Využívá stávající proces přihlašování, který je součástí aplikace, ale umožňuje správcům spravovat hesla. Uživateli není nutné znát heslo.

* **Existující jednotné přihlašování** 
    * Umožňuje službě Azure AD využívat existující jednotné přihlašování, které je pro aplikaci nakonfigurované.
    * Povolí propojení těchto aplikací s portálem Office 365 nebo portálem přístupového panelu Azure AD. 
    * Povolí další vytváření sestav ve službě Azure AD, když tam budou aplikace spuštěné. 
    * Zahrnuje použití služby Azure Application proxy a připojeného režimu jednotného přihlašování.

Informace o tom, jak nakonfigurovat režim jednotného přihlašování aplikace, najdete tady: [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pro dosažení co nejlepších zkušeností se stránkou moje aplikace doporučujeme začít s integrací cloudových aplikací dostupných pro federované jednotné přihlašování. Federované jednotné přihlašování umožňuje uživatelům, aby v rámci svých aplikací měli konzistentní možnosti jediného kliknutí a v řízení konfigurace je lépe robustní.

Používejte federované jednotné přihlašování se službou Azure AD (OpenID Connect/SAML), když ji aplikace podporuje místo jednotného přihlašování založeného na heslech a ADFS.

Další informace o tom, jak nasazovat a konfigurovat aplikace SaaS, najdete v tématu [plán nasazení SaaS SSO](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Plánování nasazení rozšíření prohlížeče moje aplikace

Pokud jsou pro koncové uživatele dostupné aplikace jednotného přihlašování založené na heslech, musí si pro přihlášení nainstalovat rozšíření zabezpečeného přihlašování k aplikacím. Toto rozšíření spustí skript, který přenáší heslo do přihlašovacího formuláře aplikace. Uživatelům se zobrazí výzva k instalaci rozšíření při prvním spuštění aplikace jednotného přihlašování založeného na heslech. Další informace o rozšíření najdete v naší dokumentaci k [instalaci rozšíření prohlížeče přístupového panelu](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Pokud potřebujete integrovat aplikace jednotného přihlašování založené na heslech, měli byste definovat mechanismus nasazení rozšíření ve velkém měřítku s [podporovanými prohlížeči](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Vaše možnosti jsou:

* [Zásady skupiny pro Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [System Center Configuration Manager (SCCM) pro Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [Stažení a konfigurace řízená uživatelem pro Chrome, Firefox, Microsoft Edge nebo IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Další informace: [Jak nakonfigurovat jednotné přihlašování k heslům](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

Pro uživatele, kteří nepoužívají aplikace jednotného přihlašování založené na heslech, je také výhoda rozšíření. Mezi tyto výhody patří možnost spuštění libovolné aplikace z panelu hledání, vyhledání přístupu k nedávno používaným aplikacím a odkaz na stránku Moje aplikace.

Zde je uvedeno, co se uživateli zobrazí při prvním spuštění aplikace jednotného přihlašování na základě hesla:

![Obrazovka instalační obrazovky rozšíření prohlížeče mých aplikací ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Plánování mobilního přístupu

Prohlížeč chráněný zásadami Intune (Microsoft Edge nebo Intune Managed Browser) bude nezbytný pro mobilní uživatele, kteří spouštějí aplikace jednotného přihlašování založené na heslech. Prohlížeč chráněný zásadami povoluje přenos hesla uloženého pro aplikaci. Microsoft Edge nebo Managed Browser poskytují sadu funkcí ochrany webových dat. Na zařízeních s iOS a Androidem můžete také používat Microsoft Edge pro podnikové scénáře. Microsoft Edge podporuje stejné scénáře správy jako Intune Managed Browser a vylepšuje činnost koncového uživatele. Další informace: [Správa webového přístupu pomocí Microsoft Intune prohlížeče chráněného zásadami](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>Plánování nasazení mých aplikací

Základem přístupového panelu je spouštěč aplikací Moje aplikace, ke kterým uživatelé přistupují [https://myapps.microsoft.com](https://myapps.microsoft.com/). Stránky Moje aplikace přidávají uživatelům jediné místo pro zahájení práce a nastanou k potřebným aplikacím. Tady uživatelé vyhledají seznam všech aplikací, ke kterým mají přístup s jednotným přihlašováním. 

![Snímek obrazovky s panelem aplikace](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Stejné aplikace se zobrazí ve Spouštěči aplikací Office 365, když uživatelé používají portál Office 365.

Naplánujte pořadí, ve kterém přidáte aplikace do spouštěče moje aplikace, a určete, jestli je chcete postupně nebo vůbec. Provedete to tak, že vytvoříte inventář aplikace, který obsahuje typ ověřování a všechny existující integrace jednotného přihlašování (SSO) pro každou aplikaci.

#### <a name="add-applications-to-the-my-apps-panel"></a>Přidat aplikace na panel Moje aplikace

Do spouštěče moje aplikace se dá přidat libovolná aplikace s povoleným PŘIHLÁŠENÍm k Azure AD. Další aplikace se přidávají pomocí propojené možnosti jednotného přihlašování. Můžete nakonfigurovat dlaždici aplikace, která odkazuje na adresu URL vaší existující webové aplikace. Propojené jednotné přihlašování umožňuje spustit přesměrování uživatelů na portál moje aplikace bez nutnosti migrace všech aplikací na Azure Active Directory jednotného přihlašování. Můžete postupně přecházet na aplikace konfigurované pro jednotné přihlašování služby Azure AD, aniž by došlo k přerušení činnosti uživatelů.

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Plánování, jestli se mají používat moje aplikace nebo stávající portál

Uživatelé už můžou mít aplikaci nebo portál jinou než moje aplikace. Pokud ano, rozhodněte se, jestli chcete podporovat portály, nebo jestli ho budete používat jenom jednou.

Pokud se už existující portál používá jako výchozí bod pro uživatele, můžete funkce aplikace integrovat pomocí adres URL pro přístup uživatelů. Adresa URL pro přístup uživatelů funguje jako přímá propojení k aplikacím dostupným na portálu moje aplikace. Tyto adresy URL mohou být vloženy do libovolného existujícího webu. Když uživatel klikne na odkaz, spustí aplikaci z portálu moje aplikace.

Vlastnost adresa URL přístupu uživatele můžete najít v oblasti vlastnosti aplikace v Azure Portal.

![Snímek obrazovky s panelem aplikace](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Plánování samoobslužného zjišťování aplikací a přístupu k nim

Po nasazení základní sady aplikací na stránku Moje aplikace uživatele doporučujeme povolit funkce Samoobslužné správy aplikací. Samoobslužné zjišťování aplikací umožňuje:
* Uživatelé budou moci najít nové aplikace, které mohou přidat do svých aplikací. 
* Uživatelé můžou Přidat volitelné aplikace, které v průběhu instalace nemusíte znát.

Pracovní postupy schvalování jsou k dispozici pro explicitní schválení přístupu k aplikacím. Uživatelé, kteří jsou schvalovatelé, budou dostávat oznámení na portálu moje aplikace, když se čeká na žádost o přístup k aplikaci.

## <a name="plan-self-service-group-membership"></a>Plánování členství ve skupině samoobslužných služeb 

Uživatelům můžete povolit vytváření a správu vlastních skupin zabezpečení nebo skupin Office 365 ve službě Azure Active Directory (Azure AD). Vlastník skupiny může schvalovat nebo zamítnout žádosti o členství a delegovat řízení členství ve skupině. Funkce Samoobslužné správy skupin nejsou k dispozici pro skupiny zabezpečení s povoleným e-mailem ani pro distribuční seznamy.

Pokud chcete naplánovat členství ve skupině samoobslužných služeb, určete, jestli chcete, aby všichni uživatelé ve vaší organizaci mohli vytvářet a spravovat skupiny, nebo jenom podmnožinu uživatelů. Pokud máte podmnožinu uživatelů, budete muset nastavit skupinu, do které se tyto osoby přidávají. Podrobnosti o povolování těchto scénářů najdete v tématu [Nastavení samoobslužné správy skupin ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) .

## <a name="plan-reporting-and-auditing"></a>Plánování generování sestav a auditování

Azure AD nabízí [sestavy, které nabízejí technické a obchodní přehledy](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Spolupracujte se svými vlastníky firemních a technických aplikací a převezmou vlastnictví a využívání těchto sestav pravidelně. Následující tabulka uvádí některé příklady typických scénářů generování sestav.

|   | Řízení rizik| Zvýšení produktivity| Zásady správného řízení a dodržování předpisů |
|  - |- | - | - |
| Typy sestav|  Oprávnění a používání aplikace.| Aktivita zřizování účtů| Kontrola přístupu k aplikacím |
| Potenciální akce| Přístup pro audit; odvolat oprávnění| Opravte všechny chyby zřizování.| Odvolat přístup |

Azure AD uchovává většinu dat auditování po dobu 30 dnů. Data jsou k dispozici prostřednictvím portálu pro správu Azure nebo rozhraní API, abyste je mohli stáhnout do svých analytických systémů.

#### <a name="auditing"></a>Auditování

Protokoly auditu pro přístup k aplikaci jsou k dispozici po dobu 30 dnů. Pokud audit zabezpečení v rámci podniku vyžaduje delší dobu uchovávání, je třeba tyto protokoly exportovat do nástroje pro správu událostí a zabezpečení (SIEM) informací o zabezpečení, jako je například Splunk nebo ArcSight.

V případě auditování, vytváření sestav a zálohování zotavení po havárii si pozdokumentujte požadovanou četnost stahování, cílový systém a kdo zodpovídá za správu jednotlivých záloh. Možná nebudete muset oddělit auditování a zálohy vytváření sestav. Záloha zotavení po havárii by měla být samostatnou entitou.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Nasazení aplikací do panelu Moje aplikace pro uživatele

Po nakonfigurování aplikace pro jednotné přihlašování se skupinám přiřadí přístup. Uživatelé v přiřazených skupinách budou mít přístup a uvidí aplikaci ve složkách moje aplikace a spouštěč aplikací Office 365.

Viz [přiřazení uživatelů a skupin k aplikaci ve službě Active Directory](methods-for-assigning-users-and-groups.md).

Při testování nebo nasazení chcete přidat skupiny, ale ještě nepovolíte zobrazování aplikací v mých aplikacích, přečtěte si téma [skrytí aplikace od uživatele v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Nasazení aplikací systém Microsoft Office 365 do mých aplikací

Pro aplikace Office 365 obdrží uživatelé kopii Office na základě licencí, které jsou jim přiřazeny. Předpokladem pro přístup k aplikacím Office je přiřazení uživatelů ke správným licencím vázaným na aplikace Office. Když uživateli přiřadíte licenci, budou se jim automaticky zobrazovat aplikace přidružené k licenci na stránce Moje aplikace a ve Spouštěči aplikací O365.

Pokud chcete skrýt sadu aplikací Office od uživatelů, je k dispozici možnost skrývat aplikace z portálu moje aplikace a přitom pořád povolit přístup z portálu O365. Tato nastavení najdete v části aplikace s uživatelskými nastaveními. Další informace: [Skrýt aplikaci ze zkušeností uživatele v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Snímek obrazovky s konfigurací způsobu skrývání aplikací](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Nasadit možnosti samoobslužné služby aplikace

Samoobslužný přístup k aplikacím umožňuje uživatelům samoobslužně zjišťovat a žádat o přístup k jakýmkoli aplikacím. Uživatelé mají volnost v tom, že získají přístup k aplikacím, které potřebují, aniž by museli procházet skupinu IT a pokaždé, když požadují přístup. Když si uživatel požádá o přístup a schválí ho buď automaticky, nebo ručně vlastníkem aplikace, přidá se do skupiny na back-endu. Vytváření sestav je povoleno pro uživatele, kteří požadovali, schválili nebo odebrali přístup, a poskytuje kontrolu nad správou přiřazených rolí.

Můžete delegovat schválení žádostí o přístup k aplikacím na obchodní schvalovatele. Obchodní schvalovatel může nastavit hesla pro přístup k aplikaci přímo ze stránky Moje aplikace schvalující firmy.

Další informace: [Jak používat Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![Snímek obrazovky konfigurace samoobslužné správy aplikací](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Ověření nasazení

Ujistěte se, že je nasazení přístupového panelu důkladně testováno a že je nastaven plán vrácení zpět.

Následující testy by se měly provádět u zařízení vlastněných společností i osobních zařízení. Tyto testovací případy by se měly projevit i v případech použití vaší firmy. Tady je několik případů na základě ukázkových požadavků na firmu v tomto dokumentu a v typických technických scénářích. Přidejte jiné, které jsou specifické pro vaše potřeby.

#### <a name="application-sso-access-test-case-examples"></a>Příklady testovacího případu přístupu k aplikaci jednotného přihlašování:


| Obchodní zdůvodnění| Očekávaný výsledek |
| - | -|
| Uživatel se přihlásí na portál moje aplikace.| Uživatel se může přihlásit a zobrazit své aplikace. |
| Uživatel spustí aplikaci federovaného jednotného přihlašování.| Uživatel je automaticky přihlášen do aplikace. |
| Uživatel poprvé spustí aplikaci pro jednotné přihlašování pomocí hesla.| Uživatel musí nainstalovat rozšíření moje aplikace. |
| Uživatel spustí aplikaci pro jednotné přihlašování pomocí hesla později.| Uživatel je automaticky přihlášen do aplikace. |
| Uživatel spustí aplikaci z portálu O365.| Uživatel je automaticky přihlášen do aplikace. |
| Uživatel spustí aplikaci z Managed Browser| Uživatel je automaticky přihlášen do aplikace. |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Příklady testovacích případů aplikace samoobslužné funkce aplikací


| Obchodní zdůvodnění| Očekávaný výsledek |
| - | - |
| Uživatel může spravovat členství v aplikaci.| Uživatel může přidat nebo odebrat členy, kteří mají přístup k aplikaci. |
| Uživatel může aplikaci upravit.| Uživatel může upravit popis aplikace a přihlašovací údaje pro aplikace jednotného přihlašování k heslům. |

### <a name="rollback-steps"></a>Kroky vrácení zpět

Je důležité naplánovat, co dělat v případě, že nasazení nepůjde naplánovat. Pokud během nasazení dojde k chybě v konfiguraci jednotného přihlašování, musíte pochopit, jak [řešit problémy](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) s jednotným přihlašováním a snížit dopad na uživatele. V extrémních případech možná budete muset [vrátit jednotné přihlašování](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Správa implementace

Microsoft doporučuje, aby k dosažení požadované úlohy v rámci Azure Active Directory používala aspoň privilegovanou roli. Společnost Microsoft doporučuje [zkontrolovat různé role, které jsou k dispozici,](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a vybrat tu správnou roli pro řešení vašich potřeb pro tuto aplikaci. Některé role může být potřeba použít dočasně a odebrat po dokončení nasazení.

| Osoby| Role| Role Azure AD  |
| - | -| -|
| Správce helpdesku| Podpora vrstvy 1| Žádné |
| Správce identit| Konfigurace a ladění v případě problémů ovlivňujících službu Azure AD| Globální správce |
| Správce aplikace| Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním| Žádné |
| Správci infrastruktury| Vlastník změna certifikátu| Globální správce |
| Vlastník/účastník společnosti| Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním| Žádné |

Doporučujeme použít [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) ke správě vašich rolí, aby se zajistilo další auditování, řízení a kontrola přístupu pro uživatele s oprávněními k adresáři.

### <a name="troubleshoot-access-panel-issues"></a>Řešení potíží s přístupovým panelem

Pomocí běžných scénářů můžete vytvářet Průvodce odstraňováním potíží pro vaši organizaci podpory a na jejich řešení přejít na dokumentaci Microsoftu. Možná budete chtít vytvořit vodítka, která přeruší podporu do vrstev používaných vaší organizací.

Další informace najdete v následujících příručkách k odstraňování potíží:

[Nezobrazují se aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Zobrazují se neočekávané aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[Uživatel se nemůže přihlásit k přístupovému panelu.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problémy s použitím samoobslužného přístupu k aplikacím](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problémy s rozšířením prohlížeče](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Další postup

[Plánování nasazení Azure Active Directory Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)
