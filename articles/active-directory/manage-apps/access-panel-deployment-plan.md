---
title: Plánování nasazení přístupového panelu Azure Active Directory
description: Pokyny k nasazení přístupového panelu Azure Active Directory
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
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99c52f65bdec2b164cca86a6346d8865d210cf38
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176057"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Plánování nasazení přístupového panelu Azure Active Directory

Přístupový panel Azure Active Directory (Azure AD) je webový portál, který pomáhá snižovat náklady na podporu, zvýšit produktivitu a zabezpečení a omezit frustrace uživatele. Systém zahrnuje podrobné hlášení, které se sleduje při přístupu k systému a upozorňuje správce na zneužití nebo zneužití.

Pomocí přístupového panelu Azure AD můžete:

* Zjistěte a získejte přístup ke všem prostředkům propojeným s Azure AD, jako jsou aplikace připojené ke své společnosti.
* Požádat o přístup k novým aplikacím a skupinám
* Správa přístupu k těmto prostředkům pro ostatní
* Správa resetování hesla samoobslužné služby a nastavení Azure Multi-Factor Authentication
* Správa jejich zařízení

Umožňuje správcům taky spravovat tyto možnosti:

* Podmínka služby
* Subjekt
* Revize přístupu


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Výhody integrace na přístupovém panelu Azure AD

Přístupový panel Azure AD přináší firmy následujícími způsoby:

**Poskytuje intuitivní uživatelské prostředí**: přístupový panel poskytuje jednu platformu pro všechny aplikace připojené k jednotnému přihlašování pomocí jednotného přihlašování (SSO) Azure. Máte jednotný portál, ve kterém najdete stávající nastavení a nové funkce, jako je Správa skupin a Samoobslužné resetování hesla, jak se přidávají. Intuitivní prostředí umožňuje uživatelům vrátit se rychleji a zvýšit produktivitu a zároveň omezit jejich frustrace.

**Zvýšení produktivity**: všechny uživatelské aplikace na přístupovém panelu mají povolený jednotné přihlašování. Povolení jednotného přihlašování v rámci podnikových aplikací a Office 365 vytvoří nadřazené prostředí pro přihlašování tím, že se bude snižovat nebo eliminovat další výzvy k přihlášení. Přístupový panel používá samoobslužné a dynamické členství a vylepšuje celkové zabezpečení systému identity. Tím zajistíte, že oprávnění ke správě přístupu k aplikacím mají správná osoba. Přístupový panel slouží jako souvislá cílová stránka pro rychlé vyhledání prostředků a pokračování pracovních úloh.

**Spravuje náklady**: povolení přístupového panelu k Azure AD může pomáhat s doplněním místních infrastruktur. Snižuje náklady na podporu tím, že vám poskytne konzistentní portál pro hledání všech vašich aplikací, vyžádat přístup k prostředkům a spravovat účty.

**Zvyšuje flexibilitu a zabezpečení**: přístupový panel vám poskytne přístup k zabezpečení a flexibilitě, které poskytuje cloudová platforma. Správci můžou snadno měnit nastavení aplikací a prostředků a můžou vyhovovat novým požadavkům na zabezpečení, aniž by to ovlivnilo uživatele.

**Umožňuje robustní auditování a sledování využití**: auditování a sledování využití u všech uživatelských možností vám umožní zjistit, jestli uživatelé používají své prostředky, a zajistit, aby bylo možné vyhodnotit zabezpečení.

### <a name="licensing-considerations"></a>Požadavky na licencování

Přístupový panel je zdarma a nevyžaduje žádné licence k použití na základní úrovni. Nicméně počet objektů ve vašem adresáři a další funkce, které chcete nasadit, můžou vyžadovat další licence. Mezi běžné scénáře služby Azure AD, které mají požadavky na licencování, patří následující funkce zabezpečení:

* [Multi-Factor Authentication Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Členství na základě skupin](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Seznamte se s [úplnými licencemi pro Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Předpoklady pro nasazení přístupového panelu Azure AD

Před zahájením tohoto projektu dokončete následující předpoklady:

* [Integrace jednotného přihlašování k aplikaci](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Správa infrastruktury uživatelů a skupin Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Plánování nasazení přístupového panelu Azure AD

Následující tabulka popisuje klíčové případy použití pro nasazení přístupového panelu:

| Oblast| Popis |
| - | - |
| Přístup| Portál přístupového panelu je přístupný z firemních i osobních zařízení v podnikové síti. |
|Přístup | Portál přístupového panelu je přístupný z firemních zařízení mimo podnikovou síť. |
| Auditování| Data o využití se stáhnou do podnikových systémů aspoň každých 29 dní. |
| Zásady správného řízení| Životní cyklus přiřazení uživatelů k aplikacím a skupinám připojeným k Azure AD je definován a monitorován. |
| Zabezpečení| Přístup k prostředkům je řízen pomocí přiřazení uživatelů a skupin. Přístup k prostředkům můžou spravovat jenom autorizovaní uživatelé. |
| Výkon| Časové osy šíření přístupu k přiřazení jsou zdokumentovány a monitorovány. |
| Činnost koncového uživatele| Uživatelé mají informace o možnostech přístupového panelu a o tom, jak je používat.|
| Činnost koncového uživatele| Uživatelé můžou spravovat svůj přístup k aplikacím a skupinám.|
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
1. Revize přístupu
   * Správa kontroly přístupu

Počínaje aplikací Moje aplikace zavádí uživatele na portál jako běžné místo pro přístup k prostředkům. Přidání samoobslužného zjišťování aplikací do prostředí moje aplikace Moje skupiny a kontroly přístupu sestavují funkce samoobslužné služby.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Plánování konfigurací pro přístupový panel Azure AD

Následující tabulka uvádí několik důležitých konfigurací přístupových panelů a typické hodnoty, které můžete použít:

| Konfigurace| Typické hodnoty |
| - | - |
| Určení pilotních skupin| Identifikujte skupinu zabezpečení Azure AD, která se má použít, a ujistěte se, že všichni pilotní členové jsou součástí skupiny. |
| Určete skupinu nebo skupiny, které mají být povoleny v produkčním prostředí.| Identifikujte skupiny zabezpečení Azure AD nebo skupiny služby Active Directory synchronizované se službou Azure AD, které se mají použít. Zajistěte, aby všichni pilotní členové byli součástí skupiny. |
| Umožňuje uživatelům používat jednotné přihlašování pro určité typy aplikací.| Federované jednotné přihlašování, OAuth, heslo jednotného přihlašování, App proxy |
| Povolení uživatelům používat Samoobslužné resetování hesla | Ano |
| Dovolit uživatelům používat Multi-Factor Authentication| Ano |
| Umožňuje uživatelům používat samoobslužnou správu skupin pro určité typy skupin.| Skupiny zabezpečení, skupiny Office 365 |
| Umožňuje uživatelům používat samoobslužnou správu aplikací.| Ano |
| Umožňuje uživatelům používat kontroly přístupu.| Ano |

### <a name="plan-consent-strategy"></a>Strategie souhlasu s plánem

Uživatelé nebo správci musí souhlasit s podmínkami použití a zásadami ochrany osobních údajů aplikace. Pokud je to možné, použijte pro vaše obchodní pravidla souhlas správce, který uživatelům poskytuje lepší prostředí.

Pokud chcete použít souhlas správce, musíte být globální správce organizace a aplikace musí být buď:

* Registrováno ve vaší organizaci

* Registrováno v jiné organizaci služby Azure AD a dříve byla odeslána alespoň jedním uživatelem.

Další informace najdete v tématu [Konfigurace způsobu, jakým koncoví uživatelé souhlasí s aplikací v Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne, protože neshodují očekávání na dopad, výsledek a odpovědnosti. Chcete-li se těmto nástrah vyhnout, [Ujistěte se, že jste připravují správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md) a že role účastníků v projektu jsou dobře pochopitelné.

### <a name="plan-communications"></a>Plánování komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně Informujte uživatele, jak a kdy se jejich prostředí změní a jak v případě potřeby získat podporu.

I když přístupový panel většinou nevytváří problémy s uživatelem, je důležité ho připravit. Před spuštěním vytvořte příručky a seznam všech prostředků pro pracovníky podpory.

#### <a name="communications-templates"></a>Šablony komunikace

Microsoft nabízí [přizpůsobitelné šablony pro e-maily a další komunikace](https://aka.ms/APTemplates) pro přístupový panel. Tyto prostředky můžete přizpůsobit pro použití v jiných komunikačních kanálech podle potřeby vaší podnikové jazykové verze.

## <a name="plan-your-sso-configuration"></a>Plánování konfigurace jednotného přihlašování

Když se uživatel přihlásí k aplikaci, projde procesem ověřování a musí prokázat, kdo jsou. Bez jednotného přihlašování se v aplikaci uloží heslo a uživatel musí toto heslo znát. Pomocí jednotného přihlašování se přihlašovací údaje uživatelů předávají do aplikace, takže nemusejí zadávat hesla pro každou aplikaci znovu.

Pokud chcete spouštět aplikace v mých aplikacích, musí být povolené jednotné přihlašování.

Azure AD podporuje tři různé způsoby, jak [v aplikacích povolit jednotné přihlašování](what-is-single-sign-on.md):

* **Federované jednotné přihlašování** 
    * Umožňuje aplikaci přesměrovat na ověřování uživatelů v rámci služby Azure AD místo výzvy k zadání hesla. 
    * Je podporováno pro aplikace, které používají protokoly, jako je SAML 2,0, WS-Federation nebo OpenID Connect, a je nejrozsáhlejším režimem jednotného přihlašování.

* **Jednotné přihlašování založené na heslech** 
    * Umožňuje zabezpečené ukládání hesel aplikací a opakované přehrání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. 
    * Využívá výhod stávajícího procesu přihlášení, který je součástí aplikace, ale umožňuje správcům spravovat hesla. Uživatel nemusí znát heslo.

* **Existující jednotné přihlašování** 
    * Umožňuje službě Azure AD využívat všechna existující jednotná přihlášení, která jsou nakonfigurovaná pro aplikaci.
    * Povolí propojení těchto aplikací s portálem Office 365 nebo portálem přístupového panelu Azure AD. 
    * Povolí další vytváření sestav ve službě Azure AD, když tam budou aplikace spuštěné. 
    * Zahrnuje použití služby Azure Application proxy a připojeného režimu jednotného přihlašování.

Přečtěte si, jak tady nakonfigurovat režim jednotného přihlašování aplikace: [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pro dosažení co nejlepších zkušeností se stránkou moje aplikace Začněte s integrací cloudových aplikací, které jsou k dispozici pro federované jednotné přihlašování. Federované jednotné přihlašování umožňuje uživatelům, aby v rámci svých aplikací měli konzistentní možnosti jediného kliknutí a v řízení konfigurace je lépe robustní.

Používejte federované jednotné přihlašování se službou Azure AD (OpenID Connect/SAML), když ji aplikace podporuje místo jednotného přihlašování založeného na heslech a ADFS.

Další informace o tom, jak nasadit a nakonfigurovat aplikace SaaS, najdete v [plánu nasazení SaaS SSO](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Plánování nasazení rozšíření prohlížeče moje aplikace

Když se uživatelé přihlásí k aplikacím jednotného přihlašování na základě hesla, musí nainstalovat a použít rozšíření pro zabezpečené přihlašování k aplikacím. Toto rozšíření spustí skript, který přenáší heslo do formuláře pro přihlášení k aplikaci. Uživatelům se zobrazí výzva k instalaci rozšíření při prvním spuštění aplikace jednotného přihlašování založeného na heslech. Další informace o rozšíření najdete v této dokumentaci týkající [se instalace rozšíření prohlížeče přístupového panelu](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Pokud potřebujete integrovat aplikace jednotného přihlašování založené na heslech, měli byste definovat mechanismus nasazení rozšíření ve velkém měřítku s [podporovanými prohlížeči](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Mezi možnosti patří:

* [Zásady skupiny pro Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [System Center Configuration Manager (SCCM) pro Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [Stažení a konfigurace na základě uživatele pro Chrome, Firefox, Microsoft Edge nebo IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Další informace: [jak nakonfigurovat jednotné přihlašování k heslům](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

Pro uživatele, kteří nepoužívají aplikace jednotného přihlašování založené na heslech, je také výhoda rozšíření. Mezi tyto výhody patří možnost spuštění libovolné aplikace z panelu hledání, vyhledání přístupu k nedávno používaným aplikacím a odkaz na stránku Moje aplikace.

Zde je uvedeno, co se uživateli zobrazí při prvním spuštění aplikace jednotného přihlašování na základě hesla:

![Obrazovka instalační obrazovky rozšíření prohlížeče mých aplikací ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Plánování mobilního přístupu

Prohlížeč chráněný zásadami Intune (Microsoft Edge nebo Intune Managed Browser) je nutný pro mobilní uživatele, kteří spouštějí aplikace jednotného přihlašování založené na heslech. Prohlížeč chráněný zásadami povoluje přenos hesla uloženého pro aplikaci. Microsoft Edge nebo Managed Browser poskytují sadu funkcí ochrany webových dat. Na zařízeních s iOS a Androidem můžete také používat Microsoft Edge pro podnikové scénáře. Microsoft Edge podporuje stejné scénáře správy jako Intune Managed Browser a zlepšuje činnost koncového uživatele. Další informace: [Správa webového přístupu pomocí Microsoft Intune prohlížeče chráněného zásadami](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Plánování nasazení mých aplikací

Základem přístupového panelu je spouštěč aplikací Moje aplikace, ke kterým uživatelé mají přístup na [https://myapps.microsoft.com](https://myapps.microsoft.com/). Stránky Moje aplikace přidávají uživatelům jediné místo pro zahájení práce a nastanou k potřebným aplikacím. Tady uživatelé vyhledají seznam všech aplikací, ke kterým mají přístup s jednotným přihlašováním. 

![Snímek obrazovky s panelem aplikace](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Stejné aplikace se zobrazí ve Spouštěči aplikací Office 365, když uživatelé používají portál Office 365.

Naplánujte pořadí, ve kterém přidáte aplikace do spouštěče moje aplikace, a rozhodněte se, jestli je zavedete postupně, nebo všechny najednou. Provedete to tak, že vytvoříte inventář aplikace, který obsahuje typ ověřování a všechny existující integrace jednotného přihlašování pro každou aplikaci.

#### <a name="add-applications-to-the-my-apps-panel"></a>Přidat aplikace na panel Moje aplikace

Do spouštěče moje aplikace se dá přidat libovolná aplikace s povoleným PŘIHLÁŠENÍm k Azure AD. Další aplikace se přidávají pomocí propojené možnosti jednotného přihlašování. Můžete nakonfigurovat dlaždici aplikace, která odkazuje na adresu URL vaší existující webové aplikace. Propojené jednotné přihlašování umožňuje zahájit přesměrování uživatelů na portál moje aplikace bez migrace všech aplikací do jednotného přihlašování služby Azure AD. Můžete postupně přecházet na aplikace konfigurované pro jednotné přihlašování služby Azure AD, aniž by došlo k přerušení činnosti uživatelů.

#### <a name="use-my-apps-workspaces-preview"></a>Použití pracovních prostorů moje aplikace (Preview)

Ve výchozím nastavení jsou všechny aplikace uvedeny společně na jedné stránce. Pracovní prostory ale můžete použít k seskupení souvisejících aplikací a jejich zobrazení na samostatné kartě, což usnadňuje jejich vyhledání. Pracovní prostory můžete například použít k vytvoření logických skupin aplikací pro konkrétní role úlohy, úkoly, projekty a tak dále. Informace najdete v tématu [Jak používat pracovní prostory moje aplikace k přizpůsobení panelů přístupu uživatele (Preview)](access-panel-workspaces.md). 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Plánování, jestli se mají používat moje aplikace nebo stávající portál

Uživatelé už můžou mít aplikaci nebo portál jinou než moje aplikace. Pokud ano, rozhodněte se, zda chcete podporovat portály, nebo použijte pouze jeden.

Pokud se už existující portál používá jako výchozí bod pro uživatele, můžete funkce aplikace integrovat pomocí adres URL pro přístup uživatelů. Adresa URL pro přístup uživatelů funguje jako přímá propojení k aplikacím dostupným na portálu moje aplikace. Tyto adresy URL mohou být vloženy do libovolného existujícího webu. Když uživatel vybere odkaz, otevře se aplikace z portálu moje aplikace.

Vlastnost adresa URL přístupu uživatele můžete najít v oblasti **vlastnosti** aplikace v Azure Portal.

![Snímek obrazovky s panelem aplikace](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Plánování samoobslužného zjišťování aplikací a přístupu k nim

Po nasazení základní sady aplikací na stránku Moje aplikace uživatele byste měli povolit funkce Samoobslužné správy aplikací. Samoobslužné zjišťování aplikací umožňuje uživatelům:

* Najděte nové aplikace, které chcete přidat do svých aplikací. 
* Přidejte volitelné aplikace, které nemusí při instalaci potřebovat znát.

Pracovní postupy schvalování jsou k dispozici pro explicitní schválení přístupu k aplikacím. Uživatelé, kteří jsou schvalovatelé, budou dostávat oznámení na portálu moje aplikace, když se čeká na žádost o přístup k aplikaci.

## <a name="plan-self-service-group-membership"></a>Plánování členství ve skupině samoobslužných služeb 

Uživatelům můžete povolit vytváření a správu vlastních skupin zabezpečení nebo skupin Office 365 ve službě Azure AD. Vlastník skupiny může schvalovat nebo zamítnout žádosti o členství a delegovat řízení členství ve skupině. Funkce Samoobslužné správy skupin nejsou k dispozici pro skupiny zabezpečení s povoleným e-mailem ani pro distribuční seznamy.

Pokud chcete naplánovat členství ve skupině samoobslužných služeb, určete, jestli chcete, aby všichni uživatelé ve vaší organizaci mohli vytvářet a spravovat skupiny nebo jenom podmnožiny uživatelů. Pokud povolujete podmnožinu uživatelů, budete muset nastavit skupinu, do které se tyto osoby přidávají. Podrobnosti o povolování těchto scénářů najdete [v tématu Nastavení samoobslužné správy skupin v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) .

## <a name="plan-reporting-and-auditing"></a>Plánování generování sestav a auditování

Azure AD nabízí [sestavy, které nabízejí technické a obchodní přehledy](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Spolupracujte se svými vlastníky obchodních a technických aplikací, abyste mohli převzít vlastnictví těchto sestav a pravidelně je spotřebovávat. Následující tabulka uvádí některé příklady typických scénářů generování sestav.

|   | Řízení rizik| Zvýšení produktivity| Zásady správného řízení a dodržování předpisů |
|  - |- | - | - |
| Typy sestav|  Oprávnění a použití aplikace| Aktivita zřizování účtů| Kontrola přístupu k aplikacím |
| Potenciální akce| Přístup pro audit; odvolat oprávnění| Opravte všechny chyby zřizování.| Odvolat přístup |

Azure AD uchovává většinu dat auditování po dobu 30 dnů. Data jsou k dispozici prostřednictvím portálu pro správu Azure nebo rozhraní API, abyste je mohli stáhnout do svých analytických systémů.

#### <a name="auditing"></a>Auditování

Protokoly auditu pro přístup k aplikaci jsou k dispozici po dobu 30 dnů. Pokud audit zabezpečení v rámci vašeho podniku vyžaduje delší dobu uchovávání, je třeba tyto protokoly exportovat do nástroje SIEM (Event Information Security and Management), jako je například Splunk nebo ArcSight.

V rámci auditování, vytváření sestav a záloh zotavení po havárii si pozdokumentujte požadovanou četnost stahování, co je cílový systém a kdo je zodpovědný za správu jednotlivých záloh. Možná nebudete muset oddělit auditování a zálohy vytváření sestav. Záloha zotavení po havárii by měla být samostatnou entitou.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Nasazení aplikací do panelu Moje aplikace pro uživatele

Po nakonfigurování aplikace pro jednotné přihlašování se skupinám přiřadí přístup. Uživatelé v přiřazených skupinách budou mít přístup a uvidí aplikaci ve složce Moje aplikace a ve Spouštěči aplikací Office 365.

Viz [přiřazení uživatelů a skupin k aplikaci ve službě Active Directory](methods-for-assigning-users-and-groups.md).

Pokud při testování nebo nasazení chcete přidat skupiny, ale ještě nechcete, aby se aplikace zobrazovaly ve složkách moje aplikace, přečtěte si téma [skrytí aplikace od uživatele v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Nasazení aplikací systém Microsoft Office 365 do mých aplikací

Pro aplikace Office 365 obdrží uživatelé kopii Office na základě licencí, které jsou jim přiřazeny. Předpokladem pro přístup k aplikacím Office je, aby se uživatelům přiřadily správné licence vázané na aplikace Office. Když uživateli přiřadíte licenci, automaticky uvidí aplikace, které jsou přidružené k licenci na stránce Moje aplikace a ve Spouštěči aplikací Office 365.

Pokud chcete skrýt sadu aplikací Office od uživatelů, je k dispozici možnost skrýt aplikace na portálu moje aplikace a přitom přitom povolit přístup z portálu Office 365. Tato nastavení najdete v části aplikace s uživatelskými nastaveními. Další informace: [skrytí aplikace z uživatelského prostředí v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Snímek obrazovky s konfigurací způsobu skrývání aplikací](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Nasadit možnosti samoobslužné služby aplikace

Samoobslužný přístup k aplikacím umožňuje uživatelům samoobslužně zjišťovat a žádat o přístup k aplikacím. Uživatelé mají volnost v přístupu k aplikacím, které potřebují, aniž by museli procházet IT oddělení, kdykoli si vyžádají přístup. Když si uživatel požádá o přístup a schválí ho buď automaticky, nebo ručně vlastníkem aplikace, přidá se do skupiny na back-endu. Vytváření sestav je povoleno pro uživatele, kteří požadovali, schválili nebo odebrali přístup, a poskytuje vám kontrolu nad správou přiřazených rolí.

Můžete delegovat schválení žádostí o přístup k aplikacím na obchodní schvalovatele. Obchodní schvalovatel může nastavit hesla pro přístup k aplikacím ze stránky Moje aplikace v obchodním schvalovateli.

Další informace: [Jak používat Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![Snímek obrazovky konfigurace samoobslužné správy aplikací](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Ověření nasazení

Ujistěte se, že je nasazení přístupového panelu důkladně testováno a že je nastaven plán vrácení zpět.

Následující testy by se měly provádět u zařízení vlastněných společností i osobních zařízení. Tyto testovací případy by se měly projevit i v případech použití vaší firmy. Tady je několik případů na základě ukázkových požadavků na firmu v tomto dokumentu a v typických technických scénářích. Přidejte jiné, které jsou specifické pro vaše potřeby.

#### <a name="application-sso-access-test-case-examples"></a>Příklady testovacího případu přístupu k aplikaci jednotného přihlašování:


| Obchodní zdůvodnění| Očekávaný výsledek |
| - | -|
| Uživatel se přihlásí na portál moje aplikace.| Uživatel se může přihlásit a zobrazit své aplikace. |
| Uživatel spustí aplikaci federovaného jednotného přihlašování.| Uživatel se automaticky přihlásí k aplikaci. |
| Uživatel poprvé spustí aplikaci pro jednotné přihlašování pomocí hesla.| Uživatel musí nainstalovat rozšíření moje aplikace. |
| Uživatel spustí aplikaci pro jednotné přihlašování pomocí hesla později.| Uživatel se automaticky přihlásí k aplikaci. |
| Uživatel spustí aplikaci z portálu Office 365.| Uživatel se automaticky přihlásí k aplikaci. |
| Uživatel spustí aplikaci z Managed Browser| Uživatel se automaticky přihlásí k aplikaci. |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Příklady testovacích případů aplikace samoobslužné funkce aplikací


| Obchodní zdůvodnění| Očekávaný výsledek |
| - | - |
| Uživatel může spravovat členství v aplikaci.| Uživatel může přidat nebo odebrat členy, kteří mají přístup k aplikaci. |
| Uživatel může aplikaci upravit.| Uživatel může upravit popis aplikace a přihlašovací údaje pro aplikace jednotného přihlašování k heslům. |

### <a name="rollback-steps"></a>Kroky vrácení zpět

Je důležité, abyste naplánovali, co dělat, pokud nasazení neprojde jako plánované. Pokud během nasazení dojde k chybě v konfiguraci jednotného přihlašování, musíte pochopit, jak [řešit problémy s jednotným PŘIhlašováním](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) a snížit dopad na uživatele. V extrémních případech možná budete muset [vrátit jednotné přihlašování](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Správa implementace

K provedení požadované úlohy v Azure Active Directory byste měli použít roli s nejnižšími oprávněními. [Projděte si různé role, které jsou k dispozici](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) , a podle toho, co potřebujete, můžete pro tuto aplikaci vyřešit vaše potřeby pro každého. Některé role může být nutné použít dočasně a odebrat po dokončení nasazení.

| Osoby| Role| Role Azure AD  |
| - | -| -|
| Správce helpdesku| Podpora vrstvy 1| Žádné |
| Správce identit| Konfigurace a ladění v případě problémů ovlivňujících službu Azure AD| Globální správce |
| Správce aplikace| Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním| Žádné |
| Správci infrastruktury| Vlastník změna certifikátu| Globální správce |
| Vlastník/účastník společnosti| Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním| Žádné |

Pomocí [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) můžete spravovat své role, abyste měli k dispozici další auditování, řízení a kontrolu přístupu pro uživatele s oprávněními k adresáři.

### <a name="troubleshoot-access-panel-issues"></a>Řešení potíží s přístupovým panelem

Vytváření průvodců odstraňováním potíží pro vaši organizaci podpory pomocí běžných scénářů, které odkazují na dokumentaci Microsoftu v jejich řešení. Možná budete chtít vytvořit vodítka, která přeruší podporu do vrstev používaných vaší organizací.

Další informace najdete v těchto příručkách k odstraňování potíží:

[Nezobrazují se aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Zobrazují se neočekávané aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[Uživatel se nemůže přihlásit k přístupovému panelu.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problémy s použitím samoobslužného přístupu k aplikacím](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problémy s rozšířením prohlížeče](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Další kroky

[Plánování nasazení Azure Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)
