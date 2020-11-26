---
title: Uplatnění pozvánky ve spolupráci B2B – Azure AD
description: Popisuje prostředí pro uplatnění pozvánky Azure AD B2B pro koncové uživatele, včetně souhlasu s podmínkami ochrany osobních údajů.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8520afdd05ecce8604ce72596bdf06053217cc2e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173086"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory uplatnění pozvánky B2B pro spolupráci

Tento článek popisuje způsoby, kterými můžou uživatelé typu Host získat přístup k vašim prostředkům a k procesu souhlasu, ke kterým dojde. Pokud odešlete e-mailovou pozvánku na hosta, pozvánka zahrnuje odkaz, který může host uplatnit, aby získal přístup k vaší aplikaci nebo portálu. E-mail pozvánky je jedním z způsobů, jak hosté můžou získat přístup k vašim prostředkům. Jako alternativu můžete do svého adresáře přidat hosty a dát jim přímý odkaz na portál nebo aplikaci, kterou chcete sdílet. Bez ohledu na to, jakou metodu používají, se hostů prostřednictvím procesu souhlasu při prvním použití. Tento proces zajistí, že se vaši hosté dohodli na podmínky ochrany osobních údajů a přijmou [podmínky použití](../conditional-access/terms-of-use.md) , které jste si nastavili.

Když do svého adresáře přidáte uživatele typu Host, má uživatelský účet hosta stav souhlasu (zobrazitelný v PowerShellu), který je zpočátku nastavený na **PendingAcceptance**. Toto nastavení zůstane, dokud uživatel nepřijme pozvání a schválí vaše zásady ochrany osobních údajů a podmínky použití. Po tomto případě se stav souhlasu změní na **přijato** a na hostovi se již neprezentují stránky souhlasu.

   > [!IMPORTANT]
   > **Od 31. března 2021** přestane společnost Microsoft podporovat uplatnění pozvánky tím, že pro scénáře spolupráce B2B vytvoří nespravované účty a klienty Azure AD. V přípravě doporučujeme zákazníkům, aby se přihlásili k [e-mailu ověřování jednorázovým heslem](one-time-passcode.md). Uvítáme vaše názory na tuto funkci Public Preview a zajímáme si vytváření ještě více způsobů, jak spolupracovat.

## <a name="redemption-through-the-invitation-email"></a>Vyplacení prostřednictvím e-mailu s pozvánkou

Když do svého adresáře přidáte uživatele typu host [pomocí Azure Portal](./b2b-quickstart-add-guest-users-portal.md), pošle se na hostovi v procesu e-mail s pozvánkou. Můžete také odeslat e-maily pozvánky, když [používáte PowerShell](./b2b-quickstart-invite-powershell.md) k přidání uživatelů typu Host do adresáře. Tady je Popis prostředí hosta při uplatnění odkazu v e-mailu.

1. Host dostane [e-mail pozvánky](./invitation-email-elements.md) , která se pošle z **pozvánky Microsoftu**.
2. Host vybere **přijmout pozvánku** v e-mailu.
3. Host bude používat vlastní přihlašovací údaje pro přihlášení k adresáři. Pokud host nemá účet, který může být federovaný pro váš adresář a funkce [e-mailového hesla (jednorázového hesla)](./one-time-passcode.md) není povolená; Host se zobrazí výzva k vytvoření osobního [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) nebo [samoobslužného účtu služby Azure AD](../enterprise-users/directory-self-service-signup.md). Podrobnosti najdete v tématu [postup pro uplatnění pozvánky](#invitation-redemption-flow) .
4. Host se provede prostřednictvím [souhlasu](#consent-experience-for-the-guest) uživatele uvedeného níže.

## <a name="redemption-through-a-direct-link"></a>Uplatnění přes přímý odkaz

Jako alternativu k e-mailu s pozvánkou můžete hostům poskytnout přímý odkaz na vaši aplikaci nebo portál. Nejdřív je potřeba přidat uživatele typu Host do adresáře prostřednictvím [Azure Portal](./b2b-quickstart-add-guest-users-portal.md) nebo [PowerShellu](./b2b-quickstart-invite-powershell.md). Pak můžete použít kterýkoli z [přizpůsobitelných způsobů, jak nasadit aplikace pro uživatele](../manage-apps/end-user-experiences.md), včetně odkazů přímo přihlašování. Když host použije přímý odkaz namísto e-mailu s pozvánkou, bude se dál nacházet pomocí prostředí pro vyjádření souhlasu po prvním čase.

> [!IMPORTANT]
> Přímý odkaz musí být specifický pro tenanta. Jinými slovy, musí obsahovat ID tenanta nebo ověřenou doménu, aby bylo možné hosta ověřit ve vašem tenantovi, kde se nachází sdílená aplikace. Společná adresa URL, jako https://myapps.microsoft.com by se u hosta nefunguje, protože se přesměruje na svého domovského tenanta pro ověřování. Tady je několik příkladů přímých odkazů s kontextem tenanta:
 > - Přístupový panel aplikací: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Přístupový panel aplikací pro ověřenou doménu: `https://myapps.microsoft.com/<;verified domain>`
 > - Azure Portal: `https://portal.azure.com/<tenant id>`
 > - Individuální aplikace: viz jak použít [přímý odkaz na přihlášení](../manage-apps/end-user-experiences.md#direct-sign-on-links) .

V některých případech se doporučuje e-mailem pozvánky používat přímý odkaz. Pokud jsou tyto zvláštní případy pro vaši organizaci důležité, doporučujeme, abyste uživatele pozvali pomocí metod, které pořád odesílají e-mail s pozvánkami:
 - Uživatel nemá účet Azure AD, účet MSA nebo e-mailový účet ve federované organizaci. Pokud nepoužíváte funkci jednorázového hesla, Host musí uplatnit e-mail s pozvánkou, aby se provedou kroky pro vytvoření MSA.
 - Někdy je možné, že objekt pozvaného uživatele nemá e-mailovou adresu z důvodu konfliktu s objektem Contact (například objekt kontaktu aplikace Outlook). V takovém případě musí uživatel v e-mailu s pozvánkou kliknout na adresu URL pro uplatnění.
 - Uživatel se může přihlásit pomocí aliasu e-mailové adresy, která byla pozvána. (Alias je další e-mailová adresa přidružená k e-mailovému účtu.) V takovém případě musí uživatel v e-mailu s pozvánkou kliknout na adresu URL pro uplatnění.

## <a name="invitation-redemption-flow"></a>Tok uplatnění pozvánky

Když uživatel klikne na odkaz **přijmout pozvánku** v [e-mailu s pozvánkou](invitation-email-elements.md), Azure AD automaticky uplatní pozvánku na základě toku uplatnění, jak je znázorněno níže:

![Snímek obrazovky znázorňující diagram toku uplatnění](media/redemption-experience/invitation-redemption-flow.png)

**Pokud se hlavní název uživatele (UPN) uživatele shoduje s existujícím účtem Azure AD i osobním MSA, zobrazí se uživateli výzva k výběru účtu, se kterým chtějí uplatnit.*

1. Azure AD provede zjišťování na základě uživatelů a určí, jestli uživatel existuje v [existujícím Tenantovi Azure AD](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal).

2. Pokud správce povolil [přímou federaci](./direct-federation.md), Azure AD zkontroluje, jestli přípona domény uživatele odpovídá doméně konfigurovaného poskytovatele identity SAML/WS, a přesměruje uživatele na předem nakonfigurovaného zprostředkovatele identity.

3. Pokud správce povolil [Google Federation](./google-federation.md), Azure AD zkontroluje, jestli je přípona domény uživatele gmail.com nebo googlemail.com, a přesměruje uživatele na Google.

4. Proces uplatnění zkontroluje, jestli má uživatel existující osobní [účet Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

5. Po identifikaci **domovského adresáře** uživatele se uživateli pošle odpovídající zprostředkovatel identity, aby se přihlásil.  

6. Pokud kroky 1 až 4 nenaleznou domovský adresář pro pozvaného uživatele, Azure AD určí, jestli má tenant povolenou funkci [jednorázového hesla k e-mailu](./one-time-passcode.md) pro hosty.

7. Pokud [je povolený e-mailová hesla pro hosty](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode), pošle se uživateli heslo prostřednictvím pozvaného e-mailu. Uživatel načte a zadá toto heslo na přihlašovací stránce služby Azure AD.

8. Pokud je e-mailové heslo pro hosty zakázané, Azure AD ověří příponu domény a určí, jestli patří do účtu uživatele. V takovém případě se uživateli zobrazí výzva k vytvoření osobního [účet Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create). V takovém případě se uživateli zobrazí výzva k vytvoření [účtu samoobslužné služby Azure AD](../enterprise-users/directory-self-service-signup.md).

9. Služba Azure AD se pokusí vytvořit [účet samoobslužné služby Azure AD](../enterprise-users/directory-self-service-signup.md) tím, že ověří přístup k e-mailu. Ověřování účtu se provádí odesláním kódu do e-mailu a uživatel ho načte a odešle do Azure AD. Pokud je však tenant pozvaného uživatele federovaný nebo pokud je v tenantovi pozvaného uživatele nastaveno na hodnotu false, uživatel nemůže dokončit uplatnění a výsledkem toku je chyba. Další informace najdete v tématu [řešení potíží s Azure Active Directory spolupráce B2B](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. Uživateli se zobrazí výzva k vytvoření osobního [účet Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. Po ověření u správného zprostředkovatele identity se uživatel přesměruje do služby Azure AD, aby se dokončilo [prostředí pro vyjádření souhlasu](#consent-experience-for-the-guest).  

V případě využití JIT (just-in-time), kdy je uplatněna prostřednictvím odkazu na tenanta aplikace, nejsou k dispozici kroky 8 až 10. Pokud uživatel dosáhne kroku 6 a funkce jednorázového e-mailového hesla není povolená, uživatel dostane chybovou zprávu a nemůže uplatnit pozvání. Aby se zabránilo této chybě, Správci by měli buď [Povolit jednorázové heslo e-mailu](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) , nebo zajistit, aby uživatel kliknul na odkaz na pozvánku.

## <a name="consent-experience-for-the-guest"></a>Prostředí pro vyjádření souhlasu hosta

Když se host přihlásí k přístupu k prostředkům v partnerské organizaci poprvé, provedou se následující stránky. 

1. Host zkontroluje stránku **oprávnění pro kontrolu** , která popisuje prohlášení o zásadách ochrany osobních údajů ve vaší organizaci. Uživatel musí **přijmout** informace v souladu se zásadami ochrany osobních údajů v organizaci, aby bylo možné pokračovat.

   ![Snímek obrazovky se stránkou pro kontrolu oprávnění](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Informace o tom, jak se může správce klienta připojit k prohlášení o zásadách ochrany osobních údajů vaší organizace, najdete [v tématu Postup: Přidání informací o ochraně osobních údajů vaší organizace v Azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. Pokud jsou podmínky použití nakonfigurované, Host se otevře a zkontroluje podmínky použití a pak vybere **přijmout**. 

   ![Snímek obrazovky s novými podmínkami použití](media/redemption-experience/terms-of-use-accept.png) 

   Můžete nakonfigurovat podmínky použití [podmínek použití](../conditional-access/terms-of-use.md) v **externích identitách**  >  **Terms of use**.

3. Pokud není uvedeno jinak, Host se přesměruje na přístupový panel aplikace, který obsahuje seznam aplikací, ke kterým má host přístup.

   ![Snímek obrazovky s přístupovým panelem aplikací](media/redemption-experience/myapps.png) 

Ve vašem adresáři se u **pozvánky hosta přijala** hodnota **Ano**. Pokud byl vytvořen MSA, zobrazí se ve **zdroji** hosta **účet Microsoft**. Další informace o vlastnostech uživatelského účtu hosta najdete v tématu [Vlastnosti uživatele spolupráce Azure AD B2B](user-properties.md). 

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidat uživatele Azure Active Directory spolupráce B2B do Azure Portal](add-users-administrator.md)
- [Jak pracovníci s informacemi přidávají uživatelům spolupráci B2B k Azure Active Directory?](add-users-information-worker.md)
- [Přidání uživatelů spolupráce v B2B Azure Active Directory pomocí PowerShellu](customize-invitation-api.md#powershell)
- [Opuštění organizace jako uživatel typu host](leave-the-organization.md)