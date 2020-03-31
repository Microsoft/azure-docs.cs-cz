---
title: Plánování nasazení přístupového panelu Azure active directory
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
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897073"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Plánování nasazení přístupového panelu Azure active directory

Přístupový panel Azure Active Directory (Azure AD) je webový portál, který pomáhá snižovat náklady na podporu, zvyšovat produktivitu a zabezpečení a snižovat frustraci uživatelů. Systém obsahuje podrobné hlášení, které sleduje při přístupu k systému a upozorní správce na zneužití nebo zneužití.

Pomocí přístupového panelu Azure AD můžete:

* Zjišťování a přístup ke všem prostředkům připojeným k Azure AD jejich společnosti, jako jsou aplikace
* Žádost o přístup k novým aplikacím a skupinám
* Správa přístupu k těmto prostředkům pro ostatní uživatele
* Správa samoobslužných resetování hesla a nastavení azure multifaktorového ověřování
* Správa jejich zařízení

Umožňuje také správcům spravovat:

* Smluvní podmínky
* Organizace
* Kontroly přístupu


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Výhody integrace přístupového panelu Azure AD

Přístupový panel Azure AD přináší výhody firmám následujícími způsoby:

**Poskytuje intuitivní uživatelské prostředí:** Přístupový panel poskytuje jednu platformu pro všechny vaše aplikace připojené k jednotnému přihlašování (SSO) Azure. Máte jednotný portál, který vyhledá stávající nastavení a nové funkce, jako je správa skupin a samoobslužné resetování hesla, když jsou přidány. Intuitivní prostředí umožňuje uživatelům vrátit se do práce rychleji a být produktivnější, a zároveň snížit jejich frustraci.

**Zvýšení produktivity**: Všechny uživatelské aplikace na přístupovém panelu mají povoleno s uzadlužování. Povolení mj. Přístupový panel používá samoobslužné a dynamické členství a zlepšuje celkové zabezpečení vašeho systému identit. Je to tím, že zajistí, aby právo lidé spravovat přístup k aplikacím. Přístupový panel slouží jako souvislá vstupní stránka pro rychlé vyhledání zdrojů a pokračování v pracovních úkolech.

**Správa nákladů**: Povolení přístupového panelu pomocí Azure AD může pomoci s odprodejem místní infrastruktury. Snižuje náklady na podporu tím, že poskytuje konzistentní portál pro vyhledání všech vašich aplikací, vyžádání přístupu k prostředkům a správu účtů.

**Zvyšuje flexibilitu a zabezpečení**: Přístupový panel umožňuje přístup k zabezpečení a flexibilitě, které cloudová platforma poskytuje. Správci mohou snadno změnit nastavení na aplikace a prostředky a mohou vyhovět novým požadavkům na zabezpečení bez ovlivnění uživatelů.

**Umožňuje robustní auditování a sledování využití:** Auditování a sledování využití pro všechny uživatelské funkce vás dozvíte, kdy uživatelé používají své prostředky, a zajistí, že můžete posoudit zabezpečení.

### <a name="licensing-considerations"></a>Aspekty licencování

Přístupový panel je zdarma a nevyžaduje žádné licence k použití na základní úrovni. Počet objektů v adresáři a další funkce, které chcete nasadit, však mohou vyžadovat další licence. Některé běžné scénáře Azure AD, které mají licenční požadavky patří následující funkce zabezpečení:

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Skupinové členství](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Ochrana identit služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Podívejte se na [úplný průvodce licencováním pro Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Požadavky pro nasazení přístupového panelu Azure AD

Před zahájením tohoto projektu proveďte následující požadavky:

* [Integrace aplikace SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Správa infrastruktury uživatelů a skupin Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Plánování nasazení přístupového panelu Azure AD

V následující tabulce jsou uvedeny klíčové případy použití nasazení přístupového panelu:

| Oblast| Popis |
| - | - |
| Access| Portál přístupových panelů je přístupný z firemních a osobních zařízení v rámci podnikové sítě. |
|Access | Portál přístupového panelu je přístupný z podnikových zařízení mimo podnikovou síť. |
| Auditování| Údaje o využití se stahují do podnikových systémů nejméně každých 29 dní. |
| Zásady správného řízení| Životní cyklus přiřazení uživatelů k aplikacím a skupinám připojeným k Azure AD je definován a monitorován. |
| Zabezpečení| Přístup k prostředkům je řízen pomocí přiřazení uživatelů a skupin. Přístup k prostředkům mohou spravovat pouze oprávnění uživatelé. |
| Výkon| Časové osy šíření přiřazení přístupu jsou dokumentovány a monitorovány. |
| Zkušenosti uživatele| Uživatelé jsou si vědomi možností přístupového panelu a jejich použití.|
| Zkušenosti uživatele| Uživatelé mohou spravovat svůj přístup k aplikacím a skupinám.|
| Zkušenosti uživatele| Uživatelé mohou spravovat své účty. |
| Zkušenosti uživatele| Uživatelé jsou si vědomi kompatibility prohlížeče. |
| Podpora| Uživatelé mohou najít podporu pro problémy s přístupovým panelem. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Doporučené postupy pro nasazení přístupového panelu Azure AD

Funkce přístupového panelu lze povolit postupně. Doporučujeme následující pořadí nasazení:

1. Moje aplikace
   * Spouštěč aplikací
   * Samoobslužná správa aplikací
   * Integrace Microsoft Office 365

1. Zjišťování samoobslužných aplikací
   * Samoobslužné resetování hesla
   * Nastavení vícefaktorového ověřování
   * Správa zařízení
   * Podmínky použití
   * Správa organizací

1. Moje skupiny
   * Samoobslužná správa skupin
1. Kontroly přístupu
   * Správa kontrol přístupu

Počínaje my apps zavádí uživatele na portál jako běžné místo pro přístup k prostředkům. Přidání zjišťování samoobslužné aplikace vychází ze zkušenosti s aplikacemi Moje aplikace. Moje skupiny a recenze přístupu vycházejí ze samoobslužných funkcí.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Plánování konfigurací přístupového panelu Azure AD

V následující tabulce je uvedeno několik důležitých konfigurací přístupových panelů a typické hodnoty, které můžete použít:

| Konfigurace| Typické hodnoty |
| - | - |
| Určení pilotních skupin| Identifikujte skupinu zabezpečení Azure AD, která se má použít, a ujistěte se, že všichni členové pilotního projektu jsou součástí skupiny. |
| Určete skupinu nebo skupiny, které mají být povoleny pro výrobu.| Identifikujte skupiny zabezpečení Azure AD nebo skupiny Služby Active Directory synchronizované se službou Azure AD, které se mají použít. Ujistěte se, že všichni členové pilota jsou součástí skupiny. |
| Povolit uživatelům používat spo.| Federated SSO, OAuth, Heslo SSO, Proxy aplikace |
| Povolit uživatelům používat samoobslužné resetování hesla | Ano |
| Povolit uživatelům používat vícefaktorové ověřování| Ano |
| Povolit uživatelům používat samoobslužnou správu skupin pro určité typy skupin| Skupiny zabezpečení, skupiny Office 365 |
| Povolit uživatelům používat samoobslužnou správu aplikací| Ano |
| Povolit uživatelům používat recenze přístupu| Ano |

### <a name="plan-consent-strategy"></a>Plánstrategie souhlasu

Uživatelé nebo správci musí souhlasit s podmínkami použití a zásadami ochrany osobních údajů jakékoli aplikace. Pokud je to možné, použijte vzhledem k vašim obchodním pravidlům souhlas správce, který uživatelům poskytuje lepší prostředí.

Chcete-li použít souhlas správce, musíte být globálním správcem organizace a aplikace musí být buď:

* Registrace ve vaší organizaci

* Zaregistrovaná v jiné organizaci Azure AD a dříve schválená alespoň jedním uživatelem

Další informace [najdete v tématu Konfigurace způsobu, jakým koncoví uživatelé souhlasí s aplikací ve službě Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Zapojte správné zúčastněné strany

Když technologické projekty selžou, obvykle tak činí z důvodu neodpovídajících očekávání dopadu, výsledků a odpovědností. Abyste se těmto nástrahám vyhnuli, [ujistěte se, že zapojujete správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md) a že role zúčastněných stran v projektu jsou dobře pochopeny.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je rozhodující pro úspěch každé nové služby. Proaktivně informujte uživatele o tom, jak a kdy se jejich zkušenosti změní a jak v případě potřeby získat podporu.

Přestože přístupový panel obvykle nevytváří problémy s uživateli, je důležité se připravit. Před spuštěním vytvořte průvodce a seznam všech zdrojů pro pracovníky podpory.

#### <a name="communications-templates"></a>Šablony komunikace

Společnost Microsoft poskytuje [přizpůsobitelné šablony pro e-maily a další komunikaci](https://aka.ms/APTemplates) pro přístupový panel. Tyto prostředky můžete přizpůsobit pro použití v jiných komunikačních kanálech podle potřeby pro firemní kulturu.

## <a name="plan-your-sso-configuration"></a>Plánování konfigurace služby SSO

Když se uživatel přihlásí k aplikaci, projde procesem ověřování a je povinen prokázat, kdo jsou. Bez přihlašuje heslo je uloženv aplikaci a uživatel je povinen znát toto heslo. S přihlašovat el.I při řízení uživatelských údajů pověření uživatelů jsou předány do aplikace, takže není nutné znovu zadat hesla pro každou aplikaci.

Chcete-li spouštět aplikace v mých aplikacích, musí být povoleno přihlašování.

Azure AD podporuje tři různé způsoby, jak povolit [jednotné přihlašování v aplikacích](what-is-single-sign-on.md):

* **Federované jednotné přihlašování** 
    * Umožňuje aplikaci přesměrovat na Azure AD pro ověřování uživatelů, namísto výzvy k zadání hesla. 
    * Je podporována pro aplikace, které používají protokoly, jako je NAPříklad SAML 2.0, WS-Federation nebo OpenID Connect, a je nejbohatším režimem jednotného přihlašování.

* **Jednotné přihlašování založené na heslech** 
    * Umožňuje bezpečné ukládání a přehrávání hesel aplikací pomocí rozšíření webového prohlížeče nebo mobilní aplikace. 
    * Využívá existující proces přihlášení poskytované aplikací, ale umožňuje správci spravovat hesla. Uživatel nemusí znát heslo.

* **Existující jednotné přihlašování** 
    * Umožňuje Azure AD využít všechny existující jednotné přihlašování, které bylo nakonfigurované pro aplikaci.
    * Umožňuje propojení těchto aplikací s portály přístupových panelů Office 365 nebo Azure AD. 
    * Povolí další vytváření sestav ve službě Azure AD při spuštění aplikací. 
    * Zahrnuje použití proxy aplikací Azure a propojeného režimu jednotného přihlašování.

Naučte se, jak nakonfigurovat režim jednotného přihlašování aplikace zde: [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pro nejlepší zkušenosti se stránkou Moje aplikace začněte s integrací cloudových aplikací, které jsou k dispozici pro federované přihlašování. Federované jednotné přihrádky umožňuje uživatelům mít konzistentní prostředí jedním kliknutím napříč jejich aplikace spouštění povrchů a inklinuje být robustnější v řízení konfigurace.

Federované přihlašované služby SSO s Azure AD (OpenID Connect/SAML) při aplikaci podporuje, namísto hesla založené při hlášky a ADFS.

Další informace o tom, jak nasadit a nakonfigurovat aplikace SaaS, najdete v tématu [plán nasazení přihlašování saas](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Plánování nasazení rozšíření prohlížeče Moje aplikace

Když se uživatelé přihlašují k aplikacím s přihlašováním pomocí hesla, musí nainstalovat a používat rozšíření zabezpečeného přihlášení Moje aplikace. Rozšíření spustí skript, který přenáší heslo do přihlašovacího formuláře aplikace. Uživatelé jsou vyzváni k instalaci rozšíření při prvním spuštění aplikace s přihlašování hesla. Další informace o rozšíření naleznete v této dokumentaci o [instalaci rozšíření prohlížeče přístupového panelu](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Pokud je nutné integrovat aplikace s přihlašování založené na heslech, měli byste definovat mechanismus nasazení rozšíření ve velkém měřítku s [podporovanými prohlížeči](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Mezi možnosti patří:

* [Zásady skupiny pro aplikaci Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Správce konfigurace pro aplikaci Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Uživatelsky řízené stahování a konfigurace pro Chrome, Firefox, Microsoft Edge nebo IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Další informace: [Jak nakonfigurovat jednotné přihlašování heslem](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Uživatelé, kteří nepoužívají aplikace s přihlašováním hesla také těžit z rozšíření. Mezi tyto výhody patří možnost spustit libovolnou aplikaci z vyhledávacího panelu, najít přístup k naposledy použitým aplikacím a mít odkaz na stránku Moje aplikace.

Tady je to, co uživatel uvidí při prvním spuštění aplikace připřihlašování hesla:

![Snímek obrazovky s instalací rozšíření prohlížeče Moje aplikace ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Plán pro mobilní přístup

Prohlížeč chráněný zásadami Intune (Microsoft Edge nebo Intune Managed Browser) je nezbytný pro mobilní uživatele, kteří spouštějí aplikace s přihlašování pomocí hesel. Prohlížeč chráněný zásadami umožňuje přenos hesla uloženého pro aplikaci. Microsoft Edge nebo spravovaný prohlížeč poskytuje sadu funkcí ochrany webových dat. Microsoft Edge můžete použít také pro podnikové scénáře na zařízeních se systémem iOS a Android. Microsoft Edge podporuje stejné scénáře správy jako spravovaný prohlížeč Intune a zlepšuje uživatelské prostředí. Další informace: [Správa webového přístupu pomocí prohlížeče chráněného zásadami Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Plánování nasazení mých aplikací

Základem přístupového panelu je spouštěč aplikací Moje [https://myapps.microsoft.com](https://myapps.microsoft.com/)aplikace, ke kterému mají uživatelé přístup na adrese . Stránky Moje aplikace poskytují uživatelům jediné místo, kde mohou začít pracovat a dostat se k potřebným aplikacím. Zde uživatelé najdou seznam všech aplikací, ke kterým mají přístup k jednotnému přihlášení. 

![Snímek obrazovky panelu aplikace](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Stejné aplikace se zobrazí ve spouštěči aplikací Office 365, když uživatelé používají portál Office 365.

Naplánujte si pořadí, ve kterém budete přidávat aplikace do spouštěče Moje aplikace, a rozhodněte se, zda je zavádíte postupně nebo najednou. Chcete-li tak učinit, vytvořte inventář aplikace se seznamem typu ověřování a všech existujících integrací přihlašování pro každou aplikaci.

#### <a name="add-applications-to-the-my-apps-panel"></a>Přidání aplikací do panelu Moje aplikace

Do spouštěče Moje aplikace se dají přidat libovolnou aplikaci s podporou azure asousu. Další aplikace se přidávají pomocí možnosti propojeného přihlašování. Můžete nakonfigurovat dlaždici aplikace, která odkazuje na adresu URL existující webové aplikace. Propojené přihlašování umožňuje zahájit směrování uživatelů na portál Moje aplikace bez migrace všech aplikací do služby Azure AD SSO. Můžete postupně přejít na aplikace nakonfigurované službou Azure AD SSO bez narušení uživatelského prostředí.

#### <a name="use-my-apps-collections"></a>Použití kolekcí Moje aplikace

Ve výchozím nastavení jsou všechny aplikace uvedeny společně na jedné stránce. Ale můžete použít kolekce seskupit související aplikace a prezentovat je na samostatné kartě, což usnadňuje jejich nalezení. Kolekce můžete například použít k vytvoření logických seskupení aplikací pro konkrétní role úlohy, úkoly, projekty a tak dále. Další informace naleznete v tématu [Jak pomocí kolekcí Moje aplikace přizpůsobit panely pro přístup uživatelů](access-panel-collections.md). 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Plánování, zda používat moje aplikace nebo existující portál

Uživatelé už možná mají jinou aplikaci nebo portál než Moje aplikace. Pokud ano, rozhodněte se, zda chcete podporovat oba portály, nebo použít pouze jeden.

Pokud se existující portál již používá jako výchozí bod pro uživatele, můžete integrovat funkce Moje aplikace pomocí adres URL přístupu uživatelů. Adresy URL pro přístup uživatelů fungují jako přímé odkazy na aplikace dostupné na portálu Moje aplikace. Tyto adresy URL lze vložit do libovolného existujícího webu. Když uživatel vybere odkaz, otevře se aplikace z portálu Moje aplikace.

Vlastnost URL přístupu uživatele najdete v oblasti **Vlastnosti** aplikace na webu Azure Portal.

![Snímek obrazovky panelu aplikace](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Plánování zjišťování a přístupu samoobslužných aplikací

Po nasazení základní sady aplikací na stránku Moje aplikace uživatele byste měli povolit funkce samoobslužné správy aplikací. Zjišťování samoobslužných aplikací umožňuje uživatelům:

* Najděte nové aplikace, které chcete přidat do svých aplikací Moje aplikace. 
* Přidejte volitelné aplikace, o kterých možná během instalace nevědí.

Pracovní postupy schválení jsou k dispozici pro explicitní schválení přístupu k aplikacím. Uživatelé, kteří jsou schvalovatelé, obdrží oznámení na portálu Moje aplikace, pokud existují nevyřízené žádosti o přístup k aplikaci.

## <a name="plan-self-service-group-membership"></a>Plánování samoobslužného členství ve skupinách 

Uživatelům můžete povolit vytváření a správu vlastních skupin zabezpečení nebo skupin Office 365 ve službě Azure AD. Vlastník skupiny může schválit nebo odepřít žádosti o členství a delegovat řízení členství ve skupině. Funkce samoobslužné správy skupin nejsou k dispozici pro skupiny zabezpečení povolené poštou nebo distribuční seznamy.

Chcete-li naplánovat samoobslužné členství ve skupinách, určete, zda povolíte všem uživatelům ve vaší organizaci vytvářet a spravovat skupiny nebo pouze podmnožinu uživatelů. Pokud povolujete podmnožinu uživatelů, budete muset nastavit skupinu, do které jsou tito uživatelé přidáni. Podrobnosti o povolení těchto scénářů najdete v tématu [Nastavení samoobslužné správy skupin ve službě Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)

## <a name="plan-reporting-and-auditing"></a>Plánování vykazování a auditování

Azure AD poskytuje [sestavy, které nabízejí technické a obchodní přehledy](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Spolupracujte s vlastníky vašich obchodních a technických aplikací, abyste převzali vlastnictví těchto sestav a pravidelně je spotřebovávali. Následující tabulka obsahuje některé příklady typických scénářů vykazování.

|   | Řízení rizik| Zvýšení produktivity| Zásady správného řízení a dodržování předpisů |
|  - |- | - | - |
| Typy sestav|  Oprávnění a použití aplikace| Aktivita zřizování účtu| Kontrola toho, kdo přistupuje k aplikacím |
| Potenciální akce| Přístup k auditu; odvolání oprávnění| Náprava všech chyb zřizování| Odvolat přístup |

Azure AD uchovává většinu dat auditování po dobu 30 dnů. Data jsou dostupná přes Portál pro správu Azure nebo rozhraní API, které si můžete stáhnout do svých analytických systémů.

#### <a name="auditing"></a>Auditování

Protokoly auditu pro přístup k aplikacím jsou k dispozici po dobu 30 dnů. Pokud auditování zabezpečení v rámci vašeho podniku vyžaduje delší uchovávání, je třeba protokoly exportovat do nástroje pro správu událostí a správy informací o zabezpečení (SIEM), jako je například Splunk nebo ArcSight.

Pro auditování, vytváření sestav a zálohování zotavení po havárii zdokumentujte požadovanou frekvenci stahování, jaký je cílový systém a kdo je zodpovědný za správu jednotlivých záloh. Možná nebudete potřebovat samostatné auditování a vytváření sestav záloh. Záloha zotavení po havárii by měla být samostatnou entitou.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Nasazení aplikací do panelu Moje aplikace uživatelů

Po nakonfigurované aplikace pro sso, skupiny jsou přiřazeny přístup. Uživatelé v přiřazených skupinách budou mít přístup a aplikace se jim zobrazí ve spouštěči aplikací Moje aplikace a v spouštěči aplikací Office 365.

Viz [Přiřazení uživatelů a skupin k aplikaci ve službě Active Directory](methods-for-assigning-users-and-groups.md).

Pokud během testování nebo nasazení chcete přidat skupiny, ale ještě nepovolit aplikacím zobrazit v moje aplikace, najdete v [tématu Skrýt aplikaci z uživatelského prostředí ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Nasazení aplikací Microsoft Office 365 do mých aplikací

U aplikací Office 365 uživatelé obdrží kopii Office na základě licencí, které jim byly přiřazeny. Předpokladem pro přístup k aplikacím Office je, aby uživatelům byly přiřazeny správné licence vázané na aplikace sady Office. Když uživateli přiřadíte licenci, automaticky uvidí aplikace, které jsou přidruženy k licenci, na stránce Moje aplikace a ve spouštěči aplikací Office 365.

Pokud chcete skrýt sadu aplikací Office před uživateli, je tu možnost skrýt aplikace z portálu Moje aplikace a zároveň povolit přístup z portálu Office 365. Tato nastavení naleznete v části Nastavení uživatele aplikace. Další informace: [Skrytí aplikace před uživatelským prostředím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Snímek obrazovky s konfigurací způsobu skrytí aplikací](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Nasazení samoobslužných funkcí aplikace

Samoobslužný přístup k aplikacím umožňuje uživatelům samostatně zjišťovat a požadovat přístup k aplikacím. Uživatelé mají svobodu přístupu k aplikacím, které potřebují, aniž by pokaždé procházeli it skupinou, aby požádali o přístup. Když uživatel požádá o přístup a je schválen, automaticky nebo ručně vlastníkem aplikace, přidá se do skupiny na back-endu. Vytváření sestav je povoleno na základě toho, kdo požádal o, schválil nebo odebral přístup, a poskytuje kontrolu nad správou přiřazených rolí.

Schválení žádostí o přístup k aplikacím můžete delegovat na obchodní schvalovatele. Schvalovatel firmy může nastavit hesla pro přístup k aplikacím na stránce Moje aplikace schvalovatele firmy.

Další informace: [Jak používat samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![Snímek obrazovky s konfigurací samoobslužné správy aplikací](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Ověření nasazení

Ujistěte se, že nasazení přístupového panelu je důkladně testována a plán vrácení zpět je na místě.

Následující testy by měly být prováděny jak s podnikovými zařízeními, tak s osobními zařízeními. Tyto testovací případy by měly také odrážet vaše obchodní případy použití. Následuje několik případů založených na ukázkových obchodních požadavcích v tomto dokumentu a na typických technických scénářích. Přidejte další specifické pro vaše potřeby.

#### <a name="application-sso-access-test-case-examples"></a>Příklady testovacího případu přístupu k aplikacím:


| Obchodní případ| Očekávaný výsledek |
| - | -|
| Uživatel se přihlásí k portálu Moje aplikace.| Uživatel se může přihlásit a zobrazit své aplikace |
| Uživatel spustí federované aplikace přihlašované ho správě a řízení.| Uživatel je automaticky přihlášen k aplikaci |
| Uživatel poprvé spustí aplikaci s přihlašováným heslem| Uživatel potřebuje nainstalovat rozšíření Moje aplikace |
| Uživatel spustí aplikaci s přihlašová nití pro heslo a následující čas| Uživatel je automaticky přihlášen k aplikaci |
| Uživatel spouští aplikaci z portálu Office 365| Uživatel je automaticky přihlášen k aplikaci |
| Uživatel spustí aplikaci ze spravovaného prohlížeče| Uživatel je automaticky přihlášen k aplikaci |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Příklady testovacích případů samoobslužných možností aplikace


| Obchodní případ| Očekávaný výsledek |
| - | - |
| Uživatel může spravovat členství v aplikaci| Uživatel může přidávat nebo odebírat členy, kteří mají přístup k aplikaci. |
| Uživatel může upravit aplikaci| Uživatel může upravit popis aplikace a pověření pro aplikace s přihlašování hesla |

### <a name="rollback-steps"></a>Kroky vrácení zpět

Je důležité naplánovat, co dělat, když vaše nasazení nepůjde podle plánu. Pokud konfigurace přihlašování nezdaří během nasazení, musíte pochopit, jak [řešit problémy s přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) a snížit dopad na uživatele. V extrémních případech může být nutné [vrátit zpět přisuzovací ho .](../manage-apps/plan-sso-deployment.md)


## <a name="manage-your-implementation"></a>Správa implementace

K provedení požadované úlohy ve službě Azure Active Directory byste měli použít nejméně privilegovanou roli. [Zkontrolujte různé role, které jsou k dispozici,](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a vyberte ten správný, abyste vyřešili své potřeby pro každou osobu pro tuto aplikaci. Některé role může být nutné dočasně použít a odebrat po dokončení nasazení.

| Osoby| Role| Role Azure AD  |
| - | -| -|
| Správce technické podpory| Podpora pro Tier 1| Žádný |
| Správce identity| Konfigurace a ladění, když problémy ovlivňují Azure AD| Globální správce |
| Správce aplikace| Ověření uživatele v aplikaci, konfigurace uživatelů s oprávněními| Žádný |
| Správci infrastruktury| Vlastník převrácení certifikátu| Globální správce |
| Vlastník/zúčastněná strana podniku| Ověření uživatele v aplikaci, konfigurace uživatelů s oprávněními| Žádný |

[Pomocí správy privilegovaných identit](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) můžete spravovat své role a poskytovat uživatelům další auditování, řízení a kontrolu přístupu s oprávněními adresáře.

### <a name="troubleshoot-access-panel-issues"></a>Poradce při potížích s přístupovým panelem

Vytvořte průvodce odstraňováním potíží pro vaši organizaci podpory pomocí běžných scénářů, které odkazují na dokumentaci společnosti Microsoft v jejich řešeních. Můžete chtít vytvořit průvodce, které rozdělují podporu do úrovní používaných vaší organizací.

Odkazovat na tyto pokyny pro řešení potíží:

[Aplikace, které se nezobrazují](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Neočekávané aplikace se objevují](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[Uživatel se nemůže přihlásit k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problémy s používáním samoobslužného přístupu k aplikacím](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problémy s rozšířením prohlížeče](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Další kroky

[Plánování nasazení azure vícefaktorového ověřování](https://aka.ms/deploymentplans/mfa)
