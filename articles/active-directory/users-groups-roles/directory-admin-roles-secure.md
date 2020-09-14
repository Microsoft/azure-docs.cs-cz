---
title: Postupy zabezpečeného přístupu pro správce v Azure AD | Microsoft Docs
description: Ujistěte se, že účty pro správu a přístup správce vaší organizace jsou zabezpečené. Pro systémové architekty a odborníky na IT, kteří konfigurují služby Azure AD, Azure a Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/30/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: d845c1fbefd5c9a6119d089824eba6cc35228a3e
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055805"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD

Zabezpečení obchodních prostředků závisí na integritě privilegovaných účtů, které spravují vaše IT systémy. Internetoví útočníci používají útoky krádeže přihlašovacích údajů k cíli účtů správců a dalšího privilegovaného přístupu k pokusu o získání přístupu k citlivým datům.

Pro cloudové služby jsou prevence a reakce společné zodpovědnosti poskytovatele cloudové služby a zákazníka. Další informace o nejnovějších hrozbách koncových bodů a cloudu najdete v [sestavě Microsoft Security Intelligence](https://www.microsoft.com/security/operations/security-intelligence-report). Tento článek vám může pomoci při vývoji plánu pro uzavírání mezer mezi aktuálními plány a pokyny, které jsou zde popsané.

> [!NOTE]
> Společnost Microsoft se zavazuje k nejvyšší úrovni důvěryhodnosti, transparentnosti, dodržování standardů a dodržování předpisů. Přečtěte si další informace o tom, jak tým globálních odpovědí na incidenty od Microsoftu omezuje důsledky útoků na cloudové služby a jak je zabudované zabezpečení v obchodních produktech a cloudových službách Microsoftu v centru [zabezpečení Microsoftu –](https://www.microsoft.com/trustcenter/security) dodržování předpisů a cílů dodržování předpisů Microsoftu v [Centru zabezpečení Microsoftu – dodržování předpisů](https://www.microsoft.com/trustcenter/compliance).

Tradičně se zabezpečení organizace zaměřuje na vstupní a výstupní body sítě jako hraniční zabezpečení. Nicméně aplikace SaaS a osobní zařízení v Internetu udělali tento přístup méně efektivní. Ve službě Azure AD nahrazujeme hraniční zabezpečení sítě pomocí ověřování ve vrstvě identity vaší organizace s uživateli přiřazenými k privilegovaným rolím správy v řízení. Jejich přístup musí být chráněný bez ohledu na to, jestli je prostředí místní, cloudové nebo hybridní.

Zabezpečení privilegovaného přístupu vyžaduje změny:

* Procesy, postupy správy a Správa znalostí
* Technické komponenty, jako jsou obrany hostitelů, ochrana účtů a Správa identit

Zabezpečte privilegovaný přístup takovým způsobem, který je spravovaný a nahlášený ve službách Microsoftu, které vás zajímají. Pokud máte místní účty správců, přečtěte si pokyny pro místní a hybridní privilegovaný přístup ve službě Active Directory při [zabezpečení privilegovaného přístupu](/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> Pokyny v tomto článku se primárně vztahují k funkcím Azure Active Directory, které jsou zahrnuté v Azure Active Directory Premiumch plánech P1 a P2. Azure Active Directory Premium P2 je součástí EMS E5 Suite a Microsoft 365 E5 Suite. V těchto pokynech předpokládáme, že vaše organizace už má pro vaše uživatele zakoupené licence Azure AD Premium P2. Pokud tyto licence nemáte, nemusí se některé doprovodné materiály vztahovat na vaši organizaci. V celém tomto článku se taky pojem globální správce (neboli globální správce) označuje jako správce společnosti nebo správce tenanta.

## <a name="develop-a-roadmap"></a>Vývoj plánu

Microsoft doporučuje vyvinout a postupovat podle plánu zabezpečení privilegovaného přístupu proti internetovým útočníkům. Plán můžete kdykoli upravit tak, aby vyhovoval vašim stávajícím funkcím a konkrétním požadavkům v rámci vaší organizace. Každá fáze plánu by měla zvýšit náklady a obtížnost nežádoucí osoby, aby mohl ohrozit privilegovaný přístup k místním, cloudovým a hybridním prostředkům. Microsoft doporučuje následující čtyři fáze průvodce. Naplánujte nejúčinnější a nejrychlejší implementaci jako první. Tento článek může být vaším průvodcem, a to na základě zkušeností Microsoftu s implementací incidentu a reakce na počítačové útoky. Časové osy pro tento plán jsou aproximace.

![Fáze plánu s časovými čárami](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fáze 1 (24-48 hodin): kritické položky, které vám doporučujeme hned

* Fáze 2 (2-4 týdny): zmírnění nejčastěji používaných technik útoku

* Fáze 3 (1-3 měsíců): viditelnost sestavení a sestavení úplné řízení aktivity správce

* Fáze 4 (šest měsíců a mimo): pokračování v sestavování ochrany za účelem dalšího posílení zabezpečení platformy

Tento rámec plánu je navržený tak, aby maximalizoval používání technologií Microsoftu, které jste už možná nasadili. Vezměte v úvahu jakékoli nástroje zabezpečení od jiných dodavatelů, které jste už nasadili nebo uvažujete o jejich nasazení.

## <a name="stage-1-critical-items-to-do-right-now"></a>Fáze 1: kritická položka, která se má provést hned

![Fáze 1 nejdůležitějších položek, které se mají provést jako první](./media/directory-admin-roles-secure/stage-one.png)

Fáze 1 plánu se zaměřuje na kritické úkoly, které je možné rychle a snadno implementovat. Doporučujeme vám, abyste tyto položky provedli hned za prvních 24-48 hodin, abyste zajistili základní úroveň zabezpečeného privilegovaného přístupu. Tato fáze plánu zabezpečení privilegovaného přístupu zahrnuje tyto akce:

### <a name="general-preparation"></a>Obecná Příprava

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Zapnout Azure AD Privileged Identity Management

Doporučujeme, abyste ve svém produkčním prostředí Azure AD zapnuli Azure AD Privileged Identity Management (PIM). Když zapnete PIM, obdržíte e-mailové zprávy s oznámením o změnách role privilegovaného přístupu. Oznámení poskytují včasné upozornění, když se do vysoce privilegovaných rolí přidá další uživatelé.

Azure AD Privileged Identity Management je zahrnutý v Azure AD Premium P2 nebo EMS E5. Abyste vám pomohli chránit přístup k aplikacím a prostředkům místně a v cloudu, zaregistrujte se [Enterprise mobility + Security bezplatné 90 zkušební verze](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Azure AD Privileged Identity Management a Azure AD Identity Protection monitorovat aktivitu zabezpečení pomocí sestav, auditování a výstrah služby Azure AD.

Po zapnutí Azure AD Privileged Identity Management:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu, který je globálním správcem vaší produkční organizace Azure AD.

2. Pokud chcete vybrat organizaci Azure AD, kterou chcete použít Privileged Identity Management, vyberte své uživatelské jméno v pravém horním rohu Azure Portal.

3. V nabídce Azure Portal vyberte **všechny služby** a vyfiltrujte seznam pro **Azure AD Privileged Identity Management**.

4. V seznamu **všechny služby** otevřete Privileged Identity Management a připněte ho na řídicí panel.

Ujistěte se, že je první osoba, která ve vaší organizaci používá PIM, přiřazená k rolím Správce **zabezpečení** a **správcům privilegovaných rolí** . Pouze správci privilegovaných rolí můžou spravovat přiřazení rolí uživatelů adresáře služby Azure AD. Průvodce zabezpečením PIM vás provede počátečním zjišťováním a přiřazením. Průvodce můžete ukončit bez provedení jakýchkoli dalších změn.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifikace a kategorizace účtů, které jsou ve vysoce privilegovaných rolích

Po zapnutí Azure AD Privileged Identity Management se podívejte na uživatele, kteří jsou v následujících rolích služby Azure AD:

* Globální správce
* Správce privilegovaných rolí
* Správce Exchange
* Správce SharePointu

Pokud ve vaší organizaci nemáte Azure AD Privileged Identity Management, můžete použít [rozhraní PowerShell API](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Začněte s rolí globálního správce, protože globální správce má stejná oprávnění pro všechny cloudové služby, pro které má vaše organizace předplacené předplatné. Tato oprávnění se udělují bez ohledu na to, kam byla přiřazena: v centru pro správu Microsoft 365, Azure Portal nebo modulu Azure AD pro Microsoft PowerShell.

Odeberte všechny účty, které už v těchto rolích nepotřebujete. Pak zařaďte do kategorií zbývající účty, které jsou přiřazené rolím Správce:

* Přiřazeno uživatelům s právy pro správu, ale používá se i pro účely bez správy (například osobní e-mail).
* Přiřazeno správcům, kteří se používají jenom pro účely správy
* Sdíleno mezi více uživateli
* Pro scénáře nouzového přístupu k zábrusu
* Pro automatizované skripty
* Pro externí uživatele

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definice alespoň dvou účtů pro nouzový přístup

Je možné, že se uživatel náhodně zamkne od své role. Pokud například není k dispozici federovaný místní zprostředkovatel identity, uživatelé se nebudou moci přihlásit ani aktivovat existující účet správce. Můžete připravit na náhodný nedostatek přístupu uložením dvou nebo více účtů pro nouzový přístup.

Účty pro nouzový přístup umožňují omezit privilegovaný přístup v rámci organizace Azure AD. Tyto účty jsou vysoce privilegované a nepřiřazují se konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezené na stav nouze pro scénáře "break sklo", kde nelze použít běžné účty pro správu. Ujistěte se, že máte kontrolu a omezení využití účtu v nouzi jenom na dobu, kdy je to nezbytné.

Vyhodnoťte účty, které jsou přiřazeny nebo mají nárok na roli globálního správce. Pokud nevidíte žádné účty pouze cloudu pomocí \* domény. onmicrosoft.com (pro nouzový přístup "break sklo"), vytvořte je. Další informace najdete v tématu [Správa účtů pro správu pro nouzový přístup ve službě Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Zapnutí vícefaktorového ověřování a registrace všech dalších vysoce privilegovaných účtů pro jednoho uživatele, kteří nejsou federované

Vyžadovat Azure Multi-Factor Authentication (MFA) při přihlašování pro všechny jednotlivé uživatele, kteří jsou trvale přiřazeni k jedné nebo více rolím správce Azure AD: globální správce, privilegovaný správce, správce Exchange a správce služby SharePoint. Pomocí průvodce povolte [vícefaktorové ověřování (MFA) pro účty správců](../authentication/howto-mfa-userstates.md) a zajistěte, aby všichni uživatelé byli zaregistrovaní v [https://aka.ms/mfasetup](https://aka.ms/mfasetup) . Další informace najdete v části Krok 2 a 3. krok příručky průvodce [chránit přístup k datům a službám v Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Fáze 2: zmírnění často používaných útoků

![Fáze 2 zmírňující často používané útoky](./media/directory-admin-roles-secure/stage-two.png)

Fáze 2 plánu se zaměřuje na zmírnění nejčastěji používaných technik útoků krádeže a zneužití přihlašovacích údajů a může být implementováno přibližně 2-4 týdnů. Tato fáze plánu zabezpečení privilegovaného přístupu zahrnuje následující akce.

### <a name="general-preparation"></a>Obecná Příprava

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Provádění inventáře služeb, vlastníků a správců

Zvýšením možnosti "Přineste si vlastní zařízení" a práce z domácích zásad a nárůstu bezdrátového připojení je důležité monitorovat uživatele, kteří se připojují k vaší síti. Audit zabezpečení může odhalit zařízení, aplikace a programy v síti, které vaše organizace nepodporuje a který představuje vysoké riziko. Další informace najdete v tématu [Přehled správy a monitorování zabezpečení Azure](../../security/fundamentals/management-monitoring-overview.md). Ujistěte se, že v procesu inventarizace zahrnete všechny následující úkoly.

* Identifikujte uživatele, kteří mají administrativní role a služby, kde mohou spravovat.
* Pomocí Azure AD PIM zjistíte, kteří uživatelé ve vaší organizaci mají přístup správce ke službě Azure AD.
* Mimo role definované ve službě Azure AD Microsoft 365 obsahuje sadu rolí správce, které můžete přiřadit uživatelům ve vaší organizaci. Každá role správce je namapována na běžné obchodní funkce a poskytuje lidem ve vaší organizaci oprávnění provádět konkrétní úkoly v [centru pro správu Microsoft 365](https://admin.microsoft.com). Pomocí centra pro správu Microsoft 365 zjistíte, kteří uživatelé ve vaší organizaci mají přístup správce k Microsoft 365, včetně rolí nespravovaných ve službě Azure AD. Další informace najdete v tématu [informace o Microsoft 365 rolích správce](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) a [postupech zabezpečení pro Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Seznamte se s inventářem ve službách, na kterých vaše organizace spoléhá, jako je Azure, Intune nebo Dynamics 365.
* Zajistěte, aby byly účty používané pro účely správy:

  * Mají připojené pracovní e-mailové adresy
  * Registrováno pro Azure Multi-Factor Authentication nebo používá ověřování v místním prostředí
* Požádejte uživatele o obchodní odůvodnění pro přístup pro správu.
* Odeberte přístup správce pro ty jednotlivce a služby, které ho nepotřebují.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifikujte účty Microsoft v rolích pro správu, které je potřeba přepnout na pracovní nebo školní účty.

Pokud vaše počáteční globální správci při zahájení používání služby Azure AD znovu použije stávající přihlašovací údaje účet Microsoft, nahraďte účty Microsoft jednotlivými cloudovým nebo synchronizovaným účtem.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Zajistěte samostatné uživatelské účty a přesměrování pošty pro účty globálních správců.

Osobní e-mailové účty jsou pravidelně podvodné prostřednictvím internetoví útočníků, což je riziko, že osobní e-mailové adresy neumožňují nesouhlasit s globálním účtem správce. Chcete-li pomoci oddělit Internetová rizika od oprávnění správce, vytvořte vyhrazené účty pro každého uživatele s oprávněními správce.

* Nezapomeňte vytvořit samostatné účty pro uživatele, aby mohli provádět globální úlohy správy.
* Ujistěte se, že globální správci omylem neotevřou e-maily nebo nespouštějí programy s účty správců.
* Ujistěte se, že tyto účty mají e-maily předané pracovní schránce.

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Zajistěte, aby se nedávno změnila hesla účtů pro správu.

Zajistěte, aby se všichni uživatelé přihlásili ke svým účtům pro správu a v posledních 90 dnech změnili hesla aspoň jednou. Ověřte také, že se v poslední době změnila hesla u všech sdílených účtů.

#### <a name="turn-on-password-hash-synchronization"></a>Zapnout synchronizaci hodnot hash hesel

Azure AD Connect synchronizuje hodnotu hash hesla uživatele z místní služby Active Directory k organizaci Azure AD založené na cloudu. Pokud používáte federaci s Active Directory Federation Services (AD FS) (AD FS), můžete použít synchronizaci hodnot hash hesel jako zálohu. Tato záloha může být užitečná v případě, že vaše místní služba Active Directory nebo servery AD FS nejsou dočasně k dispozici.

Synchronizace hodnot hash hesel umožňuje uživatelům přihlásit se ke službě pomocí stejného hesla, které používají pro přihlášení ke své místní instanci služby Active Directory. Synchronizace hodnot hash hesel umožňuje službě Identity Protection zjistit napadené přihlašovací údaje porovnáním hodnot hash hesel pomocí hesel, která jsou známá k ohrožení. Další informace najdete v tématu [implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Vyžadovat vícefaktorové ověřování pro uživatele v privilegovaných rolích a exponovaných uživatelů

Azure AD doporučuje vyžadovat službu Multi-Factor Authentication (MFA) pro všechny vaše uživatele. Nezapomeňte vzít v úvahu uživatele, kteří by měli významný dopad, pokud došlo k ohrožení jejich účtu (například finanční důstojníci). Vícefaktorové ověřování snižuje riziko útoku kvůli napadenému heslu.

Zapnout:

* [Vícefaktorové ověřování pomocí zásad podmíněného přístupu](../authentication/howto-mfa-getstarted.md) pro všechny uživatele ve vaší organizaci.

Pokud používáte Windows Hello pro firmy, požadavek MFA se dá splnit pomocí přihlašovacího prostředí Windows Hello. Další informace najdete v tématu [Windows Hello](/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Konfigurace identity Protection

Azure AD Identity Protection je nástroj pro monitorování a vytváření sestav založený na algoritmech, který detekuje potenciální ohrožení zabezpečení, která mají vliv na identity vaší organizace. Můžete nakonfigurovat automatizované odpovědi na ty zjištěné podezřelé aktivity a provést odpovídající opatření k jejich vyřešení. Další informace najdete v článku [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>Získat Microsoft 365 zabezpečeného skóre (Pokud používáte Microsoft 365)

Služba Secure skore vyhledává vaše nastavení a aktivity pro Microsoft 365 služby, které používáte, a porovnává je se směrným plánem vytvořeným Microsoftem. Získáte skóre na základě toho, jak jste se zarovnali s postupy zabezpečení. Každý, kdo má oprávnění správce pro předplatné Microsoft 365 Business Standard nebo Enterprise, má přístup k zabezpečenému skóre na adrese [https://securescore.office.com](https://securescore.office.com/) .

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Přečtěte si pokyny k zabezpečení Microsoft 365 a dodržování předpisů (Pokud používáte Microsoft 365).

[Plán pro zabezpečení a dodržování předpisů](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) popisuje přístup pro zákazníka sady Office 365, který konfiguruje Office 365 a povoluje další možnosti EMS. Pak si přečtěte kroky 3-6 o tom, jak [chránit přístup k datům a službám v Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) a průvodci, jak [monitorovat zabezpečení a dodržování předpisů v Microsoft 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Konfigurace monitorování aktivity Microsoft 365 (při použití Microsoft 365)

Monitorujte své organizace pro uživatele, kteří používají Microsoft 365 k identifikaci zaměstnanců, kteří mají účet správce, ale nemusí Microsoft 365 přístup, protože se k těmto portálům přihlašuje. Další informace najdete v tématu [sestavy aktivit v centru pro správu Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Navázání vlastníků plánů pro incidenty nebo naléhavé reakce

Navázání úspěšné schopnosti reakce na incidenty vyžaduje značné plánování a prostředky. Je nutné nepřetržitě monitorovat počítačové útoky a stanovit priority pro zpracování incidentů. Shromažďovat, analyzovat a nahlásit data o incidentech k sestavování vztahů a navázat komunikaci s dalšími interními skupinami a naplánovat vlastníky. Další informace najdete v tématu [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Zabezpečení místních privilegovaných účtů správců, pokud se ještě neudělaly

Pokud je vaše organizace Azure Active Directory synchronizovaná s místní službou Active Directory, postupujte podle pokynů v části [Průvodce zabezpečením privilegovaného přístupu](/windows-server/identity/securing-privileged-access/securing-privileged-access): Tato fáze zahrnuje:

* Vytváření samostatných účtů správce pro uživatele, kteří potřebují provádět místní úlohy správy
* Nasazení pracovních stanic s privilegovaným přístupem pro správce služby Active Directory
* Vytváření jedinečných hesel místních správců pro pracovní stanice a servery

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace, které spravují přístup k Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Dokončení inventarizace předplatných

Použijte portál Enterprise a Azure Portal k identifikaci předplatných ve vaší organizaci, které hostují produkční aplikace.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Odebrání účtů Microsoft z rolí správce

Účty Microsoft z jiných programů, například Xbox, Live a Outlook, by se neměly používat jako účty správců pro předplatná vaší organizace. Odeberte stav správce ze všech účtů Microsoft a nahraďte ho chris@contoso.com pracovními nebo školními účty Azure AD (například). Pro účely správy závisí na účtech, které jsou ověřené ve službě Azure AD, nikoli v jiných službách.

#### <a name="monitor-azure-activity"></a>Monitorování aktivity Azure

Protokol aktivit Azure poskytuje historii událostí na úrovni předplatného v Azure. Nabízí informace o tom, kdo vytvořil, aktualizoval a odstranil prostředky a kdy k těmto událostem došlo. Další informace najdete v tématu [auditování a příjem oznámení o důležitých akcích ve vašem předplatném Azure](../../azure-monitor/platform/alerts-activity-log.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace, které spravují přístup k jiným cloudovým aplikacím přes Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurace zásad podmíněného přístupu

Připravte zásady podmíněného přístupu pro místní a cloudové aplikace hostované v cloudu. Pokud máte zařízení připojená k síti na pracovišti, získáte další informace z [nastavení místního podmíněného přístupu pomocí registrace zařízení Azure Active Directory](../devices/overview.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Fáze 3: převzetí kontroly nad aktivitou správy

![Fáze 3: převzetí kontroly nad aktivitou správy](./media/directory-admin-roles-secure/stage-three.png)

Fáze 3 navazuje na zmírnění rizik z fáze 2 a měla by se implementovat přibližně 1-3 měsíců. Tato fáze plánu zabezpečení privilegovaného přístupu zahrnuje následující součásti.

### <a name="general-preparation"></a>Obecná Příprava

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Dokončení kontroly přístupu uživatelů v rolích správce

Více firemních uživatelů získává privilegovaný přístup prostřednictvím cloudových služeb, což může vést k nespravovanému přístupu. Uživatelé dnes můžou být globální správci pro Microsoft 365, Správce předplatného Azure nebo mají přístup správce k virtuálním počítačům nebo prostřednictvím aplikací SaaS.

Vaše organizace by měla považovat za běžné obchodní transakce jako neprivilegovaných uživatelů a pak udělit práva správce jenom podle potřeby. Kompletní kontroly přístupu identifikujte a potvrďte uživatele, kteří mají nárok na aktivaci oprávnění správce.

Doporučený postup:

1. Určete, kteří uživatelé jsou správci Azure AD, umožněte na vyžádání přístup správce za běhu a řízení zabezpečení na základě rolí.
2. Převeďte uživatele, kteří nemají žádné jasné zdůvodnění přístupu správce k jiné roli (Pokud žádnou z oprávněných rolí neodeberete).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Pokračovat v zavedení silnějšího ověřování pro všechny uživatele

Vyžadovat, aby vysoce vyexponované uživatelé měli moderní, silné ověřování, jako je Azure MFA nebo Windows Hello. Příklady vysoce exponovaných uživatelů zahrnují:

* Vedení sady C-Suite
* Manažeři vysoké úrovně
* Kritické pracovníky IT a zabezpečení

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Použití vyhrazených pracovních stanic pro správu Azure AD

Útočníci se můžou snažit cílit na privilegovaný účet, aby mohli narušit integritu a pravost dat. Často používají škodlivý kód, který mění logiku programu nebo snooping správce, který zadává přihlašovací údaje. Pracovní stanice s privilegovaným přístupem poskytují vyhrazený operační systém pro citlivé úlohy, který je chráněný před útoky z internetu a jinými vektory hrozeb. Oddělení těchto citlivých úloh a účtů od každodenního použití pracovní stanice a zařízení zajišťuje silnou ochranu od:

* Útoky typu phishing
* Ohrožení zabezpečení aplikací a operačních systémů
* Útoky typu zosobnění
* Útoky krádeže přihlašovacích údajů, jako je protokolování kláves, pass-the-hash a Pass-The-Ticket

Nasazením pracovních stanic s privilegovaným přístupem můžete snížit riziko, že správci zadají svoje přihlašovací údaje v desktopovém prostředí, které nedošlo k posílení zabezpečení. Další informace najdete v tématu [pracovní stanice s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Kontrola Národního institutu standardů a technologických doporučení pro zpracování incidentů

National Institute of Standards and Technology (NIST) poskytuje pokyny pro zpracování incidentů, zejména pro analýzu dat týkajících se incidentů a určení vhodné reakce na jednotlivé incidenty. Další informace najdete v tématu [Příručka pro zpracování incidentů zabezpečení počítače (NIST) (SP 800-61, revize 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementace Privileged Identity Management (PIM) pro JIT v dalších rolích pro správu

Pro Azure Active Directory použijte možnost [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) . Časově omezená aktivace privilegovaných rolí funguje tak, že vám umožní:

* Aktivace oprávnění správce k provedení konkrétního úkolu
* Vynutil MFA během procesu aktivace
* Informování správců o změnách v nedostatku pásma pomocí výstrah
* Umožněte uživatelům zachovat privilegovaný přístup pro předem nakonfigurované množství času.
* Umožněte správcům zabezpečení:

  * Zjištění všech privilegovaných identit
  * Zobrazení sestav auditu
  * Vytvoření kontrol přístupu pro identifikaci každého uživatele, který má nárok na aktivaci oprávnění správce

Pokud již používáte Azure AD Privileged Identity Management, upravte časové rámce pro oprávnění s časovou vazbou podle potřeby (například časová období údržby).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Určení vystavení přihlašovacích protokolů založených na heslech (Pokud používáte Exchange Online)

Doporučujeme, abyste identifikovali každého potenciálního uživatele, který může být pro organizaci nevědomý, pokud jejich přihlašovací údaje byly ohroženy. Pro tyto uživatele založte požadavky na silné ověřování a pomocí podmíněného přístupu Azure AD je zajistěte, aby se přihlašovat k e-mailu pomocí uživatelského jména a hesla. Můžete zablokovat [starší ověřování pomocí podmíněného přístupu](../conditional-access/block-legacy-authentication.md)a pomocí Exchange Online můžete [zablokovat základní ověřování](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) .

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Dokončení vyhodnocení přezkoumání rolí pro role Microsoft 365 (Pokud používáte Microsoft 365)

Vyhodnoťte, jestli všichni správci jsou ve správných rolích (odstraňte a znovu přiřadíte podle tohoto posouzení).

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Projděte si přístup ke správě incidentů zabezpečení, který se používá v Microsoft 365 a porovnejte s vaší vlastní organizací.

Tuto sestavu můžete stáhnout ze [správy incidentů zabezpečení v Microsoft 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Pokračovat v zabezpečení místních privilegovaných účtů pro správu

Pokud je váš Azure Active Directory připojený k místní službě Active Directory, postupujte podle pokynů v části [plán zabezpečení privilegovaného přístupu](/windows-server/identity/securing-privileged-access/securing-privileged-access): fáze 2. V této fázi:

* Nasazení pracovních stanic s privilegovaným přístupem pro všechny správce
* Vyžadování MFA
* Používejte stačí dostatečného správce pro údržbu řadiče domény, což snižuje množství možností útoku u domén.
* Nasazení pokročilého vyhodnocení hrozeb pro detekci útoků

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace, které spravují přístup k Azure

#### <a name="establish-integrated-monitoring"></a>Zavedení integrovaného monitorování

[Azure Security Center](../../security-center/security-center-intro.md):

* Poskytuje integrované monitorování zabezpečení a správu zásad v rámci předplatných Azure.
* Pomáhá detekovat hrozby, které by jinak neinformovaly
* Funguje s rozsáhlým polem řešení zabezpečení.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventarizaci privilegovaných účtů v rámci hostovaných Virtual Machines

Obvykle nemusíte uživatelům poskytovat neomezená oprávnění k vašim předplatným nebo prostředkům Azure. Role správce Azure AD můžete použít k udělení přístupu pouze k uživatelům, kteří potřebují své úlohy. Role správce Azure AD můžete použít k umožnění jednoho správce spravovat pouze virtuální počítače v rámci předplatného, zatímco jiný může spravovat databáze SQL v rámci stejného předplatného. Další informace najdete v tématu [co je řízení přístupu na základě role v Azure](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementace rolí PIM pro správce Azure AD

Využijte Privileged Identity Management s rolemi správce Azure AD ke správě, řízení a monitorování přístupu k prostředkům Azure. Ochrana osobních údajů je chráněná tím, že se snižuje doba expozice oprávnění a zvyšuje se jejich využití prostřednictvím sestav a výstrah. Další informace najdete v tématu [co je Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Použití integrace protokolů Azure k posílání relevantních protokolů Azure do systémů SIEM

Integrace protokolů Azure umožňuje integrovat nezpracované protokoly z vašich prostředků Azure do stávajících systémů zabezpečení a SIEM (Security Information and Event Management) vaší organizace. [Integrace protokolu Azure](/previous-versions/azure/security/fundamentals/azure-log-integration-overview) shromažďuje události Windows z protokolů Windows Prohlížeč událostí a prostředků Azure z těchto zdrojů:

* Protokoly aktivit Azure
* Upozornění služby Azure Security Center
* Protokoly prostředků Azure

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace, které spravují přístup k jiným cloudovým aplikacím přes Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementace zřizování uživatelů pro připojené aplikace

Azure AD umožňuje automatizovat vytváření a údržbu identit uživatelů v cloudových aplikacích, jako jsou Dropbox, Salesforce a ServiceNow. Další informace najdete v tématu [Automatizace zřizování a rušení uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrace ochrany informací

Microsoft Cloud App Security umožňuje prozkoumat soubory a nastavovat zásady na základě popisků klasifikace Azure Information Protection a umožnit tak lepší viditelnost a kontrolu nad daty v cloudu. Prohledávání a klasifikace souborů v cloudu a použití popisků služby Azure Information Protection. Další informace najdete v tématu [integrace Azure Information Protection](/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

Nakonfigurujte podmíněný přístup na základě skupiny, umístění a citlivosti aplikace pro aplikace [SaaS](https://azure.microsoft.com/overview/what-is-saas/) a aplikace připojené k Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorování aktivity v připojených cloudových aplikacích

Doporučujeme použít [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) , abyste zajistili, že přístup uživatelů je taky chráněný v připojených aplikacích. Tato funkce zabezpečení podnikového přístupu ke cloudovým aplikacím a zabezpečení účtů správců vám umožní:

* Rozšiřování viditelnosti a řízení pro cloudové aplikace
* Vytvoření zásad pro přístup, aktivity a sdílení dat
* Automaticky identifikovat rizikové aktivity, neobvyklé chování a hrozby
* Zabránit úniku dat
* Minimalizace rizik a automatizované ochrany před hrozbami a vynucení zásad

Agent Cloud App Security SIEM integruje Cloud App Security se serverem SIEM, aby umožnil centralizované monitorování Microsoft 365 výstrah a aktivit. Spouští se na vašem serveru a vybírá výstrahy a aktivity z Cloud App Security a streamuje je na server SIEM. Další informace najdete v tématu věnovaném [integraci Siem](/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses"></a>Fáze 4: pokračování v budování obrany

![Fáze 4: přijetí aktivního stav zabezpečení](./media/directory-admin-roles-secure/stage-four.png)

Fáze 4 plánu by se měla implementovat po dobu šesti měsíců a později. Dokončete svůj plán, abyste posílili ochranu privilegovaného přístupu proti potenciálním útokům, které jsou dnes známy. Pro bezpečnostní hrozby zítřka doporučujeme zobrazit zabezpečení jako průběžný proces pro zvýšení nákladů a snížení míry úspěšnosti nežádoucí osoby, která cílí na vaše prostředí.

Zabezpečení privilegovaného přístupu je důležité pro zajištění bezpečnostních ujištění vašich obchodních prostředků. Měl by však být součástí kompletního programu zabezpečení, který poskytuje průběžné záruky zabezpečení. Tento program by měl obsahovat tyto prvky:

* Zásady
* Operace
* Zabezpečení informací
* Servery
* Aplikace
* Počítače
* Zařízení
* Cloudové prostředky infrastruktury

Při správě privilegovaných účtů pro přístup doporučujeme následující postupy:

* Zajistěte, aby správci prováděli své každodenní podnikání jako Neprivilegovaní uživatelé.
* Udělit privilegovaný přístup pouze v případě potřeby a později ho odebrat (za běhu)
* Udržování protokolů aktivit auditu týkajících se privilegovaných účtů

Další informace o vytvoření kompletního plánu zabezpečení najdete v tématu [prostředky architektury Cloud IT v Microsoftu](https://almbok.com/office365/microsoft_cloud_it_architecture_resources). Pokud se chcete zapojit se službami Microsoftu, které vám pomůžou s implementací jakékoli části svého plánu, obraťte se na zástupce Microsoftu nebo si přečtěte téma [Vytvoření důležitých obranných obrany k ochraně vašeho podniku](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Tato poslední nepřetržitá fáze plánu zabezpečení privilegovaného přístupu zahrnuje následující součásti.

### <a name="general-preparation"></a>Obecná Příprava

#### <a name="review-admin-roles-in-azure-ad"></a>Kontrola rolí správce ve službě Azure AD

Zjistěte, jestli jsou aktuální předdefinované role správce Azure AD pořád aktuální, a ujistěte se, že jsou uživatelé v jenom rolích, které potřebují. Pomocí Azure AD můžete přiřadit samostatné správce pro poskytování různých funkcí. Další informace najdete v tématu [přiřazení rolí správce v Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Kontrola uživatelů, kteří mají správu zařízení připojených k Azure AD

Další informace najdete v tématu [jak nakonfigurovat zařízení připojená k hybridním Azure Active Directory](../devices/hybrid-azuread-join-plan.md).

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>Kontrola členů [předdefinovaných rolí správce Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Pokud nepoužíváte Microsoft 365, tento krok přeskočte.
‎
#### <a name="validate-incident-response-plan"></a>Ověřit plán reakce na incidenty

Pro zlepšení plánu vám Microsoft doporučuje pravidelně ověřovat, jestli váš plán funguje podle očekávání:

* Projděte si stávající mapu cest a podívejte se, co se vynechalo.
* V závislosti na Postmortem analýze, revizi stávajících nebo definování nových postupů
* Zajistěte, aby byl aktualizovaný plán a postupy odpovědí na incidenty distribuovány v rámci celé organizace.


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace, které spravují přístup k Azure 

Určete, jestli potřebujete [přenášet vlastnictví předplatného Azure na jiný účet](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Break sklo": co dělat v nouzi

![Účty pro přístup do nouzového skla](./media/directory-admin-roles-secure/emergency.jpeg)

1. Upozorněte správce klíčů a bezpečnostní důstojníci o informace o incidentu.

2. Prohlédněte si PlayBook útoku.

3. Přihlaste se ke službě Azure AD, abyste se přihlásili k kombinaci uživatelského jména a hesla účtu "break sklo".

4. Získejte pomoc od Microsoftu [otevřením žádosti o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Podívejte se na [sestavy přihlášení ke službě Azure AD](../reports-monitoring/overview-reports.md). Mezi událostí a okamžikem, kdy je zahrnutá v sestavě, může nějakou dobu trvat.

6. Pokud v hybridních prostředích není dostupná vaše místní infrastruktura a váš AD FS Server, můžete dočasně přepnout z federovaného ověřování na použití synchronizace hodnot hash hesel. Tento přepínač vrátí doménovou federaci zpět do spravovaného ověřování, dokud nebude server AD FS k dispozici.

7. Monitorujte e-mail pro privilegované účty.

8. Nezapomeňte uložit zálohy příslušných protokolů pro případné forenzní a soudní šetření.

Další informace o tom, jak systém Microsoft Office 365 zpracovává incidenty zabezpečení, najdete [v tématu Správa incidentů zabezpečení v systém Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>Nejčastější dotazy: odpovědi na zabezpečení privilegovaného přístupu  

**Otázka:** Jak mám postupovat, pokud jsem ještě neimplementoval nějaké součásti zabezpečeného přístupu?

**Odpověď:** Definujte aspoň dva účty pro přerušení, přiřaďte MFA účtům privilegovaného správce a oddělte uživatelské účty z účtů globálních správců.

**Otázka:** Jaký je hlavní problém, který je třeba vyřešit jako první, co je v rozporu?

**Odpověď:** Ujistěte se, že požadujete nejsilnější ověřování pro vysoce exponované uživatele.

**Otázka:** Co se stane, když jsme deaktivovali naše privilegované správce?

**Odpověď:** Vytvořte účet globálního správce, který je vždycky aktuální.

**Otázka:** Co se stane, když je nalevo jenom jeden globální správce a nedají se k němu získat přístup?

**Odpověď:** K získání okamžitého privilegovaného přístupu použijte jeden z vašich účtů se systémem Break.

**Otázka:** Jak můžu chránit správce v mojí organizaci?

**Odpověď:** Správci mají vždycky v běžném podnikání jako standardní "Neprivilegovaný" uživatel.

**Otázka:** Jaké jsou osvědčené postupy pro vytváření účtů správců v rámci služby Azure AD?

**Odpověď:** Rezervujte privilegovaný přístup pro konkrétní úlohy správce.

**Otázka:** Jaké nástroje existují pro omezení trvalého přístupu správce?

**Odpověď:** Privileged Identity Management (PIM) a role správce Azure AD.

**Otázka:** Jaká je Microsoft pozice při synchronizaci účtů správců se službou Azure AD?

**Odpověď:** Účty správců vrstvy 0 se používají jenom pro místní účty AD. Tyto účty nejsou obvykle synchronizovány se službou Azure AD v cloudu. Účty správců vrstvy 0 zahrnují účty, skupiny a další prostředky, které mají přímou nebo nepřímou administrativní kontrolu nad místní doménovou strukturou služby Active Directory, doménami, řadiči domény a prostředky.

**Otázka:** Jak správcům bráníme v přiřazení náhodného přístupu správce na portálu?

**Odpověď:** Používejte neprivilegované účty pro všechny uživatele a většinu správců. Začněte tím, že vyvíjíte řadu organizací, abyste zjistili, který z nich by měl mít oprávnění k privilegovanému účtu správce. A monitorujte nově vytvořené administrativní uživatele.

## <a name="next-steps"></a>Další kroky

* [Microsoft Trust Center pro zabezpečení produktů](https://www.microsoft.com/trustcenter/security) – funkce zabezpečení cloudových produktů a služeb Microsoftu

* [Centrum zabezpečení Microsoftu – dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – komplexní sada nabídek dodržování předpisů od Microsoftu pro cloudové služby

* [Pokyny k posouzení rizik](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – Správa požadavků na zabezpečení a dodržování předpisů pro cloudové služby Microsoftu

### <a name="other-microsoft-online-services"></a>Další online služby Microsoftu

* [Microsoft Intune zabezpečení](https://www.microsoft.com/trustcenter/security/intune-security) – Intune poskytuje možnosti správy mobilních zařízení, správy mobilních aplikací a počítačů z cloudu.

* [Microsoft Dynamics 365 Security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 je cloudové řešení Microsoftu, které sjednocuje možnosti pro správu vztahů se zákazníky (CRM) a plánování podnikových zdrojů (ERP).