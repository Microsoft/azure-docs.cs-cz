---
title: Plánování nasazení Azure Active Directory moje aplikace
description: Pokyny k nasazení Azure Active Directory mých aplikací
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.openlocfilehash: 8e345b27fdb2604c0c3264d6935cb9cff8aeec9c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656730"
---
# <a name="plan-an-azure-active-directory-my-apps-deployment"></a>Plánování nasazení Azure Active Directory moje aplikace

Azure Active Directory (Azure AD) Moje aplikace jsou webový portál, který pomáhá snížit náklady na podporu, zvýšit produktivitu a zabezpečení a omezit frustrace uživatele. Systém zahrnuje podrobné hlášení, které se sleduje při přístupu k systému a upozorňuje správce na zneužití nebo zneužití. Další informace o používání mých aplikací z perspektivy koncových uživatelů najdete v [nápovědě k portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

Pomocí služby Azure AD moje aplikace můžete:

* Zjistěte a získejte přístup ke všem prostředkům propojeným s Azure AD, jako jsou aplikace připojené ke své společnosti.
* Požádat o přístup k novým aplikacím a skupinám
* Správa přístupu k těmto prostředkům pro ostatní
* Správa resetování hesla samoobslužné služby a nastavení Azure Multi-Factor Authentication
* Správa jejich zařízení

Umožňuje správcům taky spravovat tyto možnosti:

* Podmínka služby
* Organizace
* Kontroly přístupu


## <a name="benefits-of-azure-ad-my-apps-integration"></a>Výhody integrace mých aplikací v Azure AD

Výhody služby Azure AD moje aplikace představují tyto firmy:

**Poskytuje intuitivní uživatelské prostředí**: Moje aplikace nabízí jednu platformu pro všechny aplikace připojené k jednotnému přihlašování pomocí jednotného přihlašování (SSO) Azure. Máte jednotný portál, ve kterém najdete stávající nastavení a nové funkce, jako je Správa skupin a Samoobslužné resetování hesla, jak se přidávají. Intuitivní prostředí umožňuje uživatelům vrátit se rychleji a zvýšit produktivitu a zároveň omezit jejich frustrace.

**Zvýšení produktivity**: všechny uživatelské aplikace v mých aplikacích mají povolený jednotné přihlašování. Povolení jednotného přihlašování v rámci podnikových aplikací a Microsoft 365 vytvoří nadřazené prostředí pro přihlašování tím, že omezí nebo odstraní další výzvy k přihlášení. Moje aplikace využívají samoobslužné a dynamické členství a zvyšují celkové zabezpečení systému identity. Moje aplikace zajišťuje, aby oprávnění ke správě přístupu k aplikacím měli správná osoba. Moje aplikace slouží jako souvislá cílová stránka pro rychlé vyhledání prostředků a pokračování pracovních úloh.

**Spravuje náklady**: povolení mých aplikací ve službě Azure AD může pomáhat s doplněním místních infrastruktur. Snižuje náklady na podporu tím, že vám poskytne konzistentní portál pro hledání všech vašich aplikací, vyžádat přístup k prostředkům a spravovat účty.

**Zvyšuje flexibilitu a zabezpečení**: Moje aplikace poskytují přístup k zabezpečení a flexibilitě, které poskytuje cloudová platforma. Správci můžou snadno měnit nastavení aplikací a prostředků a můžou vyhovovat novým požadavkům na zabezpečení, aniž by to ovlivnilo uživatele.

**Umožňuje robustní auditování a sledování využití**: auditování a sledování využití u všech uživatelských možností vám umožní zjistit, jestli uživatelé používají své prostředky, a zajistit, aby bylo možné vyhodnotit zabezpečení.

### <a name="licensing-considerations"></a>Požadavky na licencování

Moje aplikace je zdarma a nevyžaduje žádné licence k použití na základní úrovni. Nicméně počet objektů ve vašem adresáři a další funkce, které chcete nasadit, můžou vyžadovat další licence. Mezi běžné scénáře služby Azure AD, které mají požadavky na licencování, patří následující funkce zabezpečení:

* [Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
* [Členství na základě skupin](../fundamentals/active-directory-manage-groups.md)
* [Samoobslužné resetování hesla](../authentication/tutorial-enable-sspr.md)
* [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)

Seznamte se s [úplnými licencemi pro Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-azure-ad-my-apps"></a>Předpoklady pro nasazení služby Azure AD moje aplikace

Před zahájením tohoto projektu dokončete následující předpoklady:

* [Integrace jednotného přihlašování k aplikaci](./plan-sso-deployment.md)
* [Správa infrastruktury uživatelů a skupin Azure AD](../fundamentals/active-directory-manage-groups.md)

## <a name="plan-azure-ad-my-apps-deployment"></a>Plánování nasazení Azure AD moje aplikace

Následující tabulka popisuje klíčové případy použití pro nasazení moje aplikace:

| Oblast| Popis |
| - | - |
| Access| Portál moje aplikace je přístupný z firemních i osobních zařízení v podnikové síti. |
|Access | Portál moje aplikace je přístupný z firemních zařízení mimo podnikovou síť. |
| Auditování| Data o využití se stáhnou do podnikových systémů aspoň každých 29 dní. |
| Zásady správného řízení| Životní cyklus přiřazení uživatelů k aplikacím a skupinám připojeným k Azure AD je definován a monitorován. |
| Zabezpečení| Přístup k prostředkům je řízen pomocí přiřazení uživatelů a skupin. Přístup k prostředkům můžou spravovat jenom autorizovaní uživatelé. |
| Výkon| Časové osy šíření přístupu k přiřazení jsou zdokumentovány a monitorovány. |
| Zkušenosti uživatele| Uživatelé mají informace o funkcích aplikace a o tom, jak je používat.|
| Zkušenosti uživatele| Uživatelé můžou spravovat svůj přístup k aplikacím a skupinám.|
| Zkušenosti uživatele| Uživatelé můžou spravovat svoje účty. |
| Zkušenosti uživatele| Uživatelé jsou vědomi kompatibility prohlížeče. |
| Podpora| Uživatelé můžou najít podporu pro moje problémy s aplikacemi. |


> [!TIP]
> Moje aplikace se dají používat s interními adresami URL společnosti a vzdáleným pomocí proxy aplikací. Další informace najdete v tématu [kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](application-proxy-add-on-premises-application.md).

### <a name="best-practices-for-deploying-azure-ad-my-apps"></a>Osvědčené postupy pro nasazení služby Azure AD moje aplikace

Funkce mých aplikací se dá povolit postupně. Doporučujeme následující pořadí nasazení:

1. Moje aplikace
   * Spouštěč aplikace
   * Samoobslužná správa aplikací
   * Integrace Microsoftu 365

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

### <a name="plan-configurations-for-azure-my-apps"></a>Plánování konfigurací pro Azure moje aplikace

Následující tabulka uvádí několik důležitých konfigurací aplikace a obvyklé hodnoty, které můžete použít:

| Konfigurace| Typické hodnoty |
| - | - |
| Určení pilotních skupin| Identifikujte skupinu zabezpečení Azure AD, která se má použít, a ujistěte se, že všichni pilotní členové jsou součástí skupiny. |
| Určete skupinu nebo skupiny, které mají být povoleny v produkčním prostředí.| Identifikujte skupiny zabezpečení Azure AD nebo skupiny služby Active Directory synchronizované se službou Azure AD, které se mají použít. Zajistěte, aby všichni pilotní členové byli součástí skupiny. |
| Umožňuje uživatelům používat jednotné přihlašování pro určité typy aplikací.| Federované jednotné přihlašování, OAuth, heslo jednotného přihlašování, App proxy |
| Povolení uživatelům používat Samoobslužné resetování hesla | Ano |
| Dovolit uživatelům používat Multi-Factor Authentication| Ano |
| Umožňuje uživatelům používat samoobslužnou správu skupin pro určité typy skupin.| Skupiny zabezpečení, Microsoft 365 skupiny |
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

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně Informujte uživatele, jak a kdy se jejich prostředí změní a jak v případě potřeby získat podporu.

I když moje aplikace obvykle nevytvářejí problémy s uživatelem, je důležité připravit se. Před spuštěním vytvořte příručky a seznam všech prostředků pro pracovníky podpory.

#### <a name="communications-templates"></a>Šablony komunikace

Microsoft nabízí [přizpůsobitelné šablony pro e-maily a další komunikace](https://aka.ms/APTemplates) pro moje aplikace. Tyto prostředky můžete přizpůsobit pro použití v jiných komunikačních kanálech podle potřeby vaší podnikové jazykové verze.

## <a name="plan-your-sso-configuration"></a>Plánování konfigurace jednotného přihlašování

Když se uživatel přihlásí k aplikaci, projde procesem ověřování a musí prokázat, kdo jsou. Bez jednotného přihlašování se v aplikaci uloží heslo a uživatel musí toto heslo znát. Pomocí jednotného přihlašování se přihlašovací údaje uživatelů předávají do aplikace, takže nemusejí zadávat hesla pro každou aplikaci znovu.

Pokud chcete spouštět aplikace v mých aplikacích, musí být povolené jednotné přihlašování. Azure AD podporuje více možností jednotného přihlašování. Další informace najdete v tématu [Možnosti jednotného přihlašování v Azure AD](sso-options.md).

> [!NOTE]
> Další informace o používání služby Azure AD jako zprostředkovatele identity pro aplikaci najdete v části [rychlý start série při správě aplikací](view-applications-portal.md).

Pro dosažení co nejlepších zkušeností se stránkou moje aplikace Začněte s integrací cloudových aplikací, které jsou k dispozici pro federované jednotné přihlašování. Federované jednotné přihlašování umožňuje uživatelům, aby v rámci svých aplikací měli konzistentní možnosti jediného kliknutí a v řízení konfigurace je lépe robustní.

Používejte federované jednotné přihlašování se službou Azure AD (OpenID Connect/SAML), když ji aplikace podporuje místo jednotného přihlašování založeného na heslech a ADFS.

Další informace o tom, jak nasadit a nakonfigurovat aplikace SaaS, najdete v [plánu nasazení SaaS SSO](./plan-sso-deployment.md).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Plánování nasazení rozšíření prohlížeče moje aplikace

Když se uživatelé přihlásí k aplikacím jednotného přihlašování na základě hesla, musí nainstalovat a použít rozšíření pro zabezpečené přihlašování k aplikacím. Toto rozšíření spustí skript, který přenáší heslo do formuláře pro přihlášení k aplikaci. Uživatelům se zobrazí výzva k instalaci rozšíření při prvním spuštění aplikace jednotného přihlašování založeného na heslech. Další informace o rozšíření najdete v této dokumentaci o [instalaci rozšíření prohlížeče moje aplikace]().

Pokud potřebujete integrovat aplikace jednotného přihlašování založené na heslech, měli byste definovat mechanismus nasazení rozšíření ve velkém měřítku s [podporovanými prohlížeči](../user-help/my-apps-portal-end-user-access.md). Vaše možnosti jsou:

* [Zásady skupiny pro Internet Explorer]()
* [Configuration Manager pro Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
* [Stažení a konfigurace na základě uživatele pro Chrome, Firefox, Microsoft Edge nebo IE](../user-help/my-apps-portal-end-user-access.md)

Pro uživatele, kteří nepoužívají aplikace jednotného přihlašování založené na heslech, je také výhoda rozšíření. Mezi tyto výhody patří možnost spuštění libovolné aplikace z panelu hledání, vyhledání přístupu k nedávno používaným aplikacím a odkaz na stránku Moje aplikace.

#### <a name="plan-for-mobile-access"></a>Plánování mobilního přístupu

Prohlížeč chráněný zásadami Intune (Microsoft Edge nebo Intune Managed Browser) je nutný pro mobilní uživatele, kteří spouštějí aplikace jednotného přihlašování založené na heslech. Prohlížeč chráněný zásadami povoluje přenos hesla uloženého pro aplikaci. Microsoft Edge nebo Managed Browser poskytují sadu funkcí ochrany webových dat. Na zařízeních s iOS a Androidem můžete také používat Microsoft Edge pro podnikové scénáře. Microsoft Edge podporuje stejné scénáře správy jako Intune Managed Browser a zlepšuje činnost koncového uživatele. Další informace: [Správa webového přístupu pomocí Microsoft Intune prohlížeče chráněného zásadami](/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Plánování nasazení mých aplikací

Základem mých aplikací je portál spouštěče aplikací, ke kterému mají uživatelé přístup [https://myapps.microsoft.com](https://myapps.microsoft.com/) . Stránka Moje aplikace poskytuje uživatelům jediné místo, kde můžete začít pracovat a získávat k nim potřebné aplikace. Tady uživatelé vyhledají seznam všech aplikací, ke kterým mají přístup s jednotným přihlašováním. 

> [!NOTE]
> Stejné aplikace se zobrazí ve spouštěči aplikace Microsoft 365.

Naplánujte pořadí, ve kterém přidáte aplikace do spouštěče moje aplikace, a rozhodněte se, jestli je zavedete postupně, nebo všechny najednou. Provedete to tak, že vytvoříte inventář aplikace, který obsahuje typ ověřování a všechny existující integrace jednotného přihlašování pro každou aplikaci.

#### <a name="add-applications-to-the-my-apps-panel"></a>Přidat aplikace na panel Moje aplikace

Do spouštěče moje aplikace se dá přidat libovolná aplikace s povoleným PŘIHLÁŠENÍm k Azure AD. Další aplikace se přidávají pomocí propojené možnosti jednotného přihlašování. Můžete nakonfigurovat dlaždici aplikace, která odkazuje na adresu URL vaší existující webové aplikace. Propojené jednotné přihlašování umožňuje zahájit přesměrování uživatelů na portál moje aplikace bez migrace všech aplikací do jednotného přihlašování služby Azure AD. Můžete postupně přecházet na aplikace konfigurované pro jednotné přihlašování služby Azure AD, aniž by došlo k přerušení činnosti uživatelů.

#### <a name="use-my-apps-collections"></a>Použití kolekcí moje aplikace

Ve výchozím nastavení jsou všechny aplikace uvedeny společně na jedné stránce. Kolekce ale můžete použít k seskupení souvisejících aplikací a jejich zobrazení na samostatné kartě, což usnadňuje jejich vyhledání. Kolekce můžete například použít k vytvoření logických seskupení aplikací pro konkrétní role úlohy, úkoly, projekty a tak dále. Informace najdete v tématu [Jak používat kolekce moje aplikace](access-panel-collections.md). 

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

Uživatelům můžete povolit vytváření a správu vlastních skupin zabezpečení nebo skupin Microsoft 365 v Azure AD. Vlastník skupiny může schvalovat nebo zamítnout žádosti o členství a delegovat řízení členství ve skupině. Funkce Samoobslužné správy skupin nejsou k dispozici pro skupiny zabezpečení s povoleným e-mailem ani pro distribuční seznamy.

Pokud chcete naplánovat členství ve skupině samoobslužných služeb, určete, jestli chcete, aby všichni uživatelé ve vaší organizaci mohli vytvářet a spravovat skupiny nebo jenom podmnožiny uživatelů. Pokud povolujete podmnožinu uživatelů, budete muset nastavit skupinu, do které se tyto osoby přidávají. Podrobnosti o povolování těchto scénářů najdete [v tématu Nastavení samoobslužné správy skupin v Azure Active Directory](../enterprise-users/groups-self-service-management.md) .

## <a name="plan-reporting-and-auditing"></a>Plánování generování sestav a auditování

Azure AD nabízí [sestavy, které nabízejí technické a obchodní přehledy](../reports-monitoring/overview-reports.md). Spolupracujte se svými vlastníky obchodních a technických aplikací, abyste mohli převzít vlastnictví těchto sestav a pravidelně je spotřebovávat. Následující tabulka uvádí některé příklady typických scénářů generování sestav.

| Příklad | Řízení rizik| Zvýšení produktivity| Zásady správného řízení a dodržování předpisů |
|  - |- | - | - |
| Typy sestav|  Oprávnění a použití aplikace| Aktivita zřizování účtů| Kontrola přístupu k aplikacím |
| Potenciální akce| Přístup pro audit; odvolat oprávnění| Opravte všechny chyby zřizování.| Odvolat přístup |

Azure AD uchovává většinu dat auditování po dobu 30 dnů. Data jsou k dispozici prostřednictvím portálu pro správu Azure nebo rozhraní API, abyste je mohli stáhnout do svých analytických systémů.

#### <a name="auditing"></a>Auditování

Protokoly auditu pro přístup k aplikaci jsou k dispozici po dobu 30 dnů. Pokud audit zabezpečení v rámci vašeho podniku vyžaduje delší dobu uchovávání, je třeba tyto protokoly exportovat do nástroje SIEM (Event Information Security and Management), jako je například Splunk nebo ArcSight.

V rámci auditování, vytváření sestav a záloh zotavení po havárii si pozdokumentujte požadovanou četnost stahování, co je cílový systém a kdo je zodpovědný za správu jednotlivých záloh. Možná nebudete muset oddělit auditování a zálohy vytváření sestav. Záloha zotavení po havárii by měla být samostatnou entitou.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Nasazení aplikací do panelu Moje aplikace pro uživatele

Po nakonfigurování aplikace pro jednotné přihlašování se skupinám přiřadí přístup. Uživatelé v přiřazených skupinách budou mít přístup a uvidí aplikaci ve složce Moje aplikace a ve spouštěči aplikace Microsoft 365.

Viz [přiřazení uživatelů a skupin k aplikaci ve službě Active Directory](./assign-user-or-group-access-portal.md).

Pokud při testování nebo nasazení chcete přidat skupiny, ale ještě nechcete, aby se aplikace zobrazovaly ve složkách moje aplikace, přečtěte si téma [skrytí aplikace od uživatele v Azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-microsoft-365-applications-to-my-apps"></a>Nasazení aplikací Microsoft 365 do mých aplikací

Pro Microsoft 365 aplikace obdrží uživatelé kopii Office na základě licencí, které jsou jim přiřazeny. Předpokladem pro přístup k aplikacím Office je, aby se uživatelům přiřadily správné licence vázané na aplikace Office. Když uživateli přiřadíte licenci, automaticky uvidí aplikace, které jsou přidružené k licenci na stránce Moje aplikace a ve spouštěči aplikace Microsoft 365.

Pokud chcete skrýt sadu aplikací Office od uživatelů, je k dispozici možnost skrýt aplikace z portálu moje aplikace, ale přitom povolit přístup z portálu Microsoft 365. Další informace: [skrytí aplikace z uživatelského prostředí v Azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-application-self-service-capabilities"></a>Nasadit možnosti samoobslužné služby aplikace

Samoobslužný přístup k aplikacím umožňuje uživatelům samoobslužně zjišťovat a žádat o přístup k aplikacím. Uživatelé mají volnost v přístupu k aplikacím, které potřebují, aniž by museli procházet IT oddělení, kdykoli si vyžádají přístup. Když si uživatel požádá o přístup a schválí ho buď automaticky, nebo ručně vlastníkem aplikace, přidá se do skupiny na back-endu. Vytváření sestav je povoleno pro uživatele, kteří požadovali, schválili nebo odebrali přístup, a poskytuje vám kontrolu nad správou přiřazených rolí.

Můžete delegovat schválení žádostí o přístup k aplikacím na obchodní schvalovatele. Obchodní schvalovatel může nastavit hesla pro přístup k aplikacím ze stránky Moje aplikace v obchodním schvalovateli.

Další informace: [Jak používat Samoobslužný přístup k aplikacím](access-panel-manage-self-service-access.md).

## <a name="validate-your-deployment"></a>Ověření nasazení

Ujistěte se, že vaše nasazení moje aplikace je důkladně testováno a že je nastaven plán vrácení zpět.

Následující testy by se měly provádět u zařízení vlastněných společností i osobních zařízení. Tyto testovací případy by se měly projevit i v případech použití vaší firmy. Tady je několik případů na základě ukázkových požadavků na firmu v tomto dokumentu a v typických technických scénářích. Přidejte jiné, které jsou specifické pro vaše potřeby.

#### <a name="application-sso-access-test-case-examples"></a>Příklady testovacího případu přístupu k aplikaci jednotného přihlašování:


| Obchodní případ| Očekávaný výsledek |
| - | -|
| Uživatel se přihlásí na portál moje aplikace.| Uživatel se může přihlásit a zobrazit své aplikace. |
| Uživatel spustí aplikaci federovaného jednotného přihlašování.| Uživatel se automaticky přihlásí k aplikaci. |
| Uživatel poprvé spustí aplikaci pro jednotné přihlašování pomocí hesla.| Uživatel musí nainstalovat rozšíření moje aplikace. |
| Uživatel spustí aplikaci pro jednotné přihlašování pomocí hesla později.| Uživatel se automaticky přihlásí k aplikaci. |
| Uživatel spustí aplikaci z portálu Microsoft 365.| Uživatel se automaticky přihlásí k aplikaci. |
| Uživatel spustí aplikaci z Managed Browser| Uživatel se automaticky přihlásí k aplikaci. |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Příklady testovacích případů aplikace samoobslužné funkce aplikací

| Obchodní případ| Očekávaný výsledek |
| - | - |
| Uživatel může spravovat členství v aplikaci.| Uživatel může přidat nebo odebrat členy, kteří mají přístup k aplikaci. |
| Uživatel může aplikaci upravit.| Uživatel může upravit popis aplikace a přihlašovací údaje pro aplikace jednotného přihlašování k heslům. |

### <a name="rollback-steps"></a>Kroky vrácení zpět

Je důležité, abyste naplánovali, co dělat, pokud nasazení neprojde jako plánované. Pokud během nasazení dojde k chybě v konfiguraci jednotného přihlašování, musíte pochopit, jak [řešit problémy s jednotným PŘIhlašováním](../hybrid/tshoot-connect-sso.md) a snížit dopad na uživatele. V extrémních případech možná budete muset [vrátit jednotné přihlašování](../manage-apps/plan-sso-deployment.md#rollback-process).


## <a name="manage-your-implementation"></a>Správa implementace

K provedení požadované úlohy v Azure Active Directory použijte nejnižší privilegovanou roli. [Projděte si různé role, které jsou k dispozici](../roles/permissions-reference.md) , a podle toho, co potřebujete, můžete pro tuto aplikaci vyřešit vaše potřeby pro každého. Některé role může být nutné použít dočasně a odebrat po dokončení nasazení.

| Osoby| Role| Role Azure AD  |
| - | -| -|
| Správce helpdesku| Podpora vrstvy 1| Žádné |
| Správce identit| Konfigurace a ladění v případě problémů ovlivňujících službu Azure AD| Globální správce |
| Správce aplikace| Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním| Žádné |
| Správci infrastruktury| Vlastník změna certifikátu| Globální správce |
| Vlastník/účastník společnosti| Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním| Žádné |

Pomocí [Privileged Identity Management](../privileged-identity-management/pim-configure.md) můžete spravovat své role, abyste měli k dispozici další auditování, řízení a kontrolu přístupu pro uživatele s oprávněními k adresáři.

## <a name="next-steps"></a>Další kroky
[Plánování nasazení Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)