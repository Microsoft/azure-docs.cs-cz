---
title: Využití pozvánky ve spolupráci B2B – Azure AD
description: Popisuje možnosti využití pozvání ke spolupráci Azure AD B2B pro koncové uživatele, včetně smlouvy o podmínkách ochrany osobních údajů.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0645807aa40557c163643f1393c310668518f9be
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535127"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Využití pozvánky ke spolupráci služby Azure Active Directory B2B

Tento článek popisuje způsoby, jakými mohou uživatelé typu Host přistupovat k vašim prostředkům, a proces souhlasu, se kterým se setkají. Pokud hostovi pošlete e-mail s pozvánkou, obsahuje odkaz, který může host uplatnit, aby získal přístup k vaší aplikaci nebo portálu. E-mail s pozvánkou je jen jedním ze způsobů, jak mohou hosté získat přístup k vašim zdrojům. Jako alternativu můžete přidat hosty do svého adresáře a poskytnout jim přímý odkaz na portál nebo aplikaci, kterou chcete sdílet. Bez ohledu na metodu, kterou používají, jsou hosté vedeni procesem prvního souhlasu. Tento proces zajišťuje, že vaši hosté souhlasí s podmínkami ochrany osobních údajů a přijmou jakékoli [podmínky použití,](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) které jste nastavili.

Když přidáte uživatele typu Host do adresáře, uživatelský účet hosta má stav souhlasu (zobrazitelný v prostředí PowerShell), který je původně nastaven na **PendingAcceptance**. Toto nastavení platí, dokud host nepřijme vaši pozvánku a nebude souhlasit s vašimi zásadami ochrany osobních údajů a podmínkami použití. Poté se stav souhlasu změní na **Přijato**a stránky se souhlasem se již hostu nezobrazí.

   > [!IMPORTANT]
   > **března 2021**, Microsoft již nebude podporovat uplatnění pozvánek vytvořením nespravované účty Azure AD a tenanty pro scénáře spolupráce B2B. V rámci přípravy doporučujeme zákazníkům, aby se přihlásili k [jednorázovému ověření hesla e-mailem](one-time-passcode.md). Vítáme vaši zpětnou vazbu k této funkci veřejného náhledu a jsme nadšeni, že můžeme vytvořit ještě více způsobů spolupráce.

## <a name="redemption-through-the-invitation-email"></a>Uplatnění prostřednictvím e-mailu s pozvánkou

Když přidáte uživatele typu Host do svého adresáře [pomocí portálu Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), bude hostovi v procesu odeslán e-mail s pozvánkou. Můžete také odeslat e-maily s pozvánkami, když k přidání uživatelů typu Host do adresáře [používáte PowerShell.](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) Zde je popis zážitku hosta, když uplatní odkaz v e-mailu.

1. Host obdrží [e-mail s pozvánkami](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) odeslaným z **pozvánek společnosti Microsoft**.
2. Host vybere **možnost Začínáme** v e-mailu.
3. Pokud host nemá účet Azure AD, účet Microsoft (MSA) nebo e-mailový účet ve federované organizaci, zobrazí se výzva k vytvoření MSA (pokud není povolena funkce [jednorázového přístupového kódu,](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) která nevyžaduje MSA).
4. Host je veden zkušenostmi se [souhlasem popsanými](#consent-experience-for-the-guest) níže.

## <a name="redemption-through-a-direct-link"></a>Vykoupení prostřednictvím přímého odkazu

Jako alternativu k e-mailu s pozvánkou můžete hostovi poskytnout přímý odkaz na vaši aplikaci nebo portál. Nejdřív musíte přidat uživatele typu Host do adresáře prostřednictvím [portálu Azure nebo](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) [PowerShellu](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Pak můžete použít některý z [přizpůsobitelných způsobů nasazení aplikací uživatelům](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), včetně přímých přihlašovacích odkazů. Když host místo e-mailu s pozvánkou použije přímý odkaz, bude stále veden prostředím pro první souhlas.

> [!IMPORTANT]
> Přímé propojení musí být specifické pro klienta. Jinými slovy musí obsahovat ID klienta nebo ověřenou doménu, aby host mohl být ověřen ve vašem tenantovi, kde se sdílená aplikace nachází. Běžná adresa https://myapps.microsoft.com URL, jako je, nebude pro hosta fungovat, protože přesměruje na domácího tenanta pro ověřování. Zde jsou některé příklady přímých odkazů s kontextem klienta:
 > - Přístupový panel https://myapps.microsoft.com/?tenantid=&ltaplikací: ;id klienta&gt; 
 > - Přístupový panel aplikací pro https://myapps.microsoft.com/&ltověřenou doménu: ;ověřená doména&gt;
 > - Portál Azure: https://portal.azure.com/&lt;ID klienta&gt;
 > - Individuální aplikace: podívejte se, jak používat [přímý přihlašovací odkaz](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Existují některé případy, kdy je e-mail s pozvánkou doporučen přes přímý odkaz. Pokud jsou tyto zvláštní případy pro vaši organizaci důležité, doporučujeme pozvat uživatele pomocí metod, které stále posílají e-mail s pozvánkou:
 - Uživatel nemá účet Azure AD, MSA nebo e-mailový účet ve federované organizaci. Pokud nepoužíváte funkci jednorázového hesla, host musí uplatnit e-mail s pozvánkou, aby mohl projít kroky pro vytvoření msa.
 - V některých případě nemusí mít pozvaný objekt uživatele e-mailovou adresu z důvodu konfliktu s objektem kontaktu (například objekt kontaktu aplikace Outlook). V takovém případě musí uživatel kliknout na adresu URL pro využití v e-mailu s pozvánkou.
 - Uživatel se může přihlásit pomocí aliasu e-mailové adresy, která byla pozvána. (Alias je další e-mailová adresa přidružená k e-mailovému účtu.) V takovém případě musí uživatel kliknout na adresu URL pro využití v e-mailu s pozvánkou.

## <a name="invitation-redemption-flow"></a>Tok uplatnění pozvánky

Když uživatel klikne na odkaz **Přijmout pozvánku** v [e-mailu s pozvánkou](invitation-email-elements.md), Azure AD pozvánku automaticky uplatní na základě toku využití, jak je znázorněno níže:

![Snímek obrazovky s diagramem toku využití](media/redemption-experience/invitation-redemption-flow.png)

1. Proces uplatnění zkontroluje, zda má uživatel existující osobní [účet Microsoft (MSA).](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)

2. Pokud správce povolil [přímou federaci](direct-federation.md), Azure AD zkontroluje, jestli přípona domény uživatele odpovídá doméně nakonfigurovaného poskytovatele identity SAML/WS-Fed, a přesměruje uživatele na předem nakonfigurovaného zprostředkovatele identity.

3. Pokud správce povolil [federaci Google](google-federation.md), Azure AD zkontroluje, jestli je přípona domény uživatele gmail.com nebo googlemail.com a přesměruje uživatele na Google.

4. Azure AD provádí zjišťování na základě uživatele k určení, pokud uživatel existuje v [existujícím tenantovi Azure AD](what-is-b2b.md#easily-add-guest-users-in-the-azure-ad-portal).

5. Jakmile je identifikován **domovský adresář** uživatele, je uživatel odeslán odpovídajícímu poskytovateli identity k přihlášení.  

6. Pokud se krokům 1 až 4 nepodaří najít domovský adresář pro pozvaného uživatele, Azure AD určuje, zda zvoucí tenant povolil funkci [E-mail s jednorázovým přístupovým kódem (OTP)](one-time-passcode.md) pro hosty.

7. Pokud [je povolen jednorázový přístupový kód e-mailem pro hosty](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode), je uživateli prostřednictvím pozvaných e-mailů odeslán přístupový kód. Uživatel načte a zadá tento přístupový kód na přihlašovací stránce Azure AD.

8. Pokud je jednorázový přístupový kód pro hosty zakázán, Azure AD zkontroluje příponu domény podle seznamu domén příjemce spravovaného společností Microsoft. Pokud se doména shoduje s libovolnou doménou v seznamu domén příjemce, bude uživatel vyzván k vytvoření osobního účtu Microsoft. Pokud ne, uživatel je vyzván k vytvoření [samoobslužného účtu Azure AD](../users-groups-roles/directory-self-service-signup.md) (virální účet).

9. Azure AD se pokusí vytvořit samoobslužný účet Azure AD (virální účet) ověřením přístupu k e-mailu. Ověření účtu se provádí odesláním kódu do e-mailu a mít uživatele načíst a odeslat do Azure AD. Pokud je však klient pozvaného uživatele federován nebo pokud je pole AllowEmailVerifiedUsers v tenantovi pozvaného uživatele nastaveno na hodnotu false, uživatel nemůže dokončit uplatnění a tok má za následek chybu. Další informace naleznete [v části Poradce při potížích se službou Azure Active Directory B2B collaboration](troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. Uživatel je vyzván k vytvoření osobního účtu Microsoft (MSA).

11. Po ověření na správné poskytovatele identity, uživatel je přesměrován do Služby Azure AD k dokončení [prostředí souhlasu](redemption-experience.md#consent-experience-for-the-guest).  

Pro uplatnění just-in-time (JIT), kde je uplatnění prostřednictvím propojení aplikace klienta, kroky 8 až 10 nejsou k dispozici. Pokud uživatel dosáhne kroku 6 a funkce Jednorázového hesla E-mail není povolena, zobrazí se chybová zpráva a nemůže pozvánku uplatnit. Chcete-li tomu zabránit, správci by měli buď [povolit jednorázový přístupový kód E-mail,](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) nebo zajistit, aby uživatel kliknul na odkaz s pozvánkou.

## <a name="consent-experience-for-the-guest"></a>Zkušenosti se souhlasem hosta

Když se host poprvé přihlásí k přístupu k prostředkům v partnerské organizaci, provede se následujícími stránkami. 

1. Host zkontroluje stránku **Oprávnění k recenzi** popisující prohlášení o zásadách ochrany osobních údajů zvoucí organizace. Aby uživatel mohl pokračovat, musí **přijmout** použití svých informací v souladu se zásadami ochrany osobních údajů zvoucí organizace.

   ![Snímek obrazovky se stránkou Zkontrolovat oprávnění](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Informace o tom, jak můžete jako správce klienta odkazovat na prohlášení o zásadách ochrany osobních údajů vaší organizace, najdete v článku [Postup: Přidání informací o ochraně osobních údajů vaší organizace ve službě Azure Active Directory](https://aka.ms/adprivacystatement).

2. Pokud jsou nakonfigurovány podmínky použití, host otevře a zkontroluje podmínky použití a pak vybere **Přijmout**. 

   ![Snímek obrazovky s novými podmínkami použití](media/redemption-experience/terms-of-use-accept.png) 

   Podmínky použití můžete nakonfigurovat v části **Správa** >  [organizačních](../governance/active-directory-tou.md) **vztahů** > **Podmínky použití**.

3. Pokud není uvedeno jinak, host je přesměrován na přístupový panel Aplikace, který uvádí aplikace, ke kterým má host přístup.

   ![Snímek obrazovky s přístupovým panelem Aplikace](media/redemption-experience/myapps.png) 

V adresáři se **akceptovaná** hodnota pozvánky hosta změní na **Ano**. Pokud byl vytvořen MSA, **zdroj hosta** zobrazuje **účet Microsoft**. Další informace o vlastnostech uživatelského účtu hosta najdete [v tématu Vlastnosti uživatele spolupráce Azure AD B2B](user-properties.md). 

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidání uživatelů spolupráce služby Azure Active Directory B2B na webu Azure Portal](add-users-administrator.md)
- [Jak pracovníci s informacemi přidávají uživatele spolupráce B2B do služby Azure Active Directory?](add-users-information-worker.md)
- [Přidání uživatelů spolupráce služby Azure Active Directory B2B pomocí prostředí PowerShell](customize-invitation-api.md#powershell)
- [Opuštění organizace jako uživatele typu hosta](leave-the-organization.md)
