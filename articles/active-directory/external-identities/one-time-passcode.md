---
title: Jednorázové ověřování hesla pro uživatele typu Host B2B – Azure AD
description: Jak používat jednorázové heslo e-mailu k ověřování uživatelů typu Host B2B bez nutnosti účet Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9bc7d94cff46b54b81615cf064ed63927b3ec60
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929289"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-mailové ověřování heslem jednorázového hesla (Preview)

Tento článek popisuje, jak povolit jednorázové ověřování e-mailových hesel pro uživatele typu Host B2B. Funkce jednorázového hesla pro e-mail ověřuje uživatele typu Host B2B, když se nemůžou ověřit jiným způsobem jako Azure AD, účet Microsoft (MSA) nebo Google Federation. Při jednorázovém ověřování pomocí hesla není nutné vytvářet účet Microsoft. Když uživatel typu Host uplatňuje pozvánku nebo přistupuje ke sdílenému prostředku, může požádat o dočasný kód, který se pošle na svou e-mailovou adresu. Pak tento kód zadá, aby bylo možné pokračovat v přihlašování.

![Diagram přehledu hesla na jednom čase v e-mailu](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> **Od 1. března 2021** bude funkce jednorázového hesla pro e-mail zapnutá pro všechny existující klienty a ve výchozím nastavení povolená pro nové klienty. Pokud nechcete povolit automatické zapnutí této funkce, můžete ji zakázat. Viz téma [deaktivace e-mailového hesla One-Time](#disable-email-one-time-passcode) .

> [!NOTE]
> Jednorázovým uživatelům se musí přihlásit pomocí odkazu, který obsahuje kontext tenanta (například `https://myapps.microsoft.com/?tenantid=<tenant id>` nebo nebo `https://portal.azure.com/<tenant id>` v případě ověřené domény `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Přímé odkazy na aplikace a prostředky fungují i tak dlouho, dokud budou zahrnovat kontext tenanta. Uživatelé typu Host se aktuálně nemohou přihlašovat pomocí koncových bodů, které nemají kontext tenanta. Například použití `https://myapps.microsoft.com` , `https://portal.azure.com` způsobí chybu.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Uživatelské prostředí pro uživatele typu Host pro jednorázové heslo

Když je povolená funkce pro jednorázové e-mailové heslo, nově pozvaní uživatelé [, kteří splňují určité podmínky](#when-does-a-guest-user-get-a-one-time-passcode) , budou používat jednorázové ověřování pomocí hesla. Uživatelé typu Host, kteří požádali o pozvánku předtím, než byl povolen e-mail s jednorázovým heslem, budou nadále používat stejnou metodu ověřování.

Při jednorázovém ověřování pomocí hesla může uživatel typu Host uplatnit pozvánku kliknutím na přímý odkaz nebo pomocí e-mailu s pozvánkou. V obou případech zpráva v prohlížeči indikuje, že se kód pošle na e-mailovou adresu uživatele typu Host. Uživatel typu host vybere **Odeslat kód**:

   ![Snímek obrazovky zobrazující tlačítko pro odeslání kódu](media/one-time-passcode/otp-send-code.png)

Heslo se pošle na e-mailovou adresu uživatele. Uživatel načte heslo z e-mailu a vloží ho do okna prohlížeče:

   ![Snímek obrazovky, na které se zobrazuje znaková stránka ENTER](media/one-time-passcode/otp-enter-code.png)

Uživatel typu Host je teď ověřený a může zobrazit sdílený prostředek nebo pokračovat v přihlašování.

> [!NOTE]
> Jednorázová hesla platí po dobu 30 minut. Po 30 minutách již není konkrétní zadání jednorázového hesla platné a uživatel musí požádat o nový. Platnost uživatelských relací vyprší za 24 hodin. Po uplynutí této doby obdrží uživatel typu Host při přístupu k prostředku nové heslo. Vypršení platnosti relace poskytuje zabezpečení, zejména v případě, že uživatel typu Host opustí svou společnost nebo už nepotřebuje přístup.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Kdy uživatel typu Host získá jednorázové heslo?

Když uživatel typu Host uplatní pozvánku nebo použije odkaz na prostředek, který s nimi sdílí, obdrží jednorázové heslo, pokud:

- Nemají účet Azure AD.
- Nemají účet Microsoft
- Tenant pro pozvání nevytvořil pro uživatele Google Federation. @gmail.com @googlemail.com

V době pozvání není k dispozici žádný náznak, že uživatel, kterého Zvete, bude používat jednorázové ověřování pomocí hesla. Pokud se ale uživatel typu Host přihlásí, bude metoda jednorázového ověřování hesla záložní metodou, pokud nebude možné použít žádné jiné metody ověřování.

Můžete se podívat, jestli se uživatel typu Host ověřuje pomocí jednorázových hesel, a to zobrazením vlastnosti **zdroj** v podrobnostech uživatele. V Azure Portal klikněte na **Azure Active Directory**  >  **Uživatelé** a potom vyberte uživatele, na kterém otevřete stránku s podrobnostmi.

![Snímek obrazovky, který zobrazuje jednorázového uživatele se zdrojovou hodnotou JEDNORÁZOVého hesla](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Když uživatel uplatňuje jednorázové heslo a později získá MSA, účet Azure AD nebo jiný federovaný účet, budou se i nadále ověřovat pomocí jednorázového hesla. Pokud chcete aktualizovat metodu ověřování, můžete odstranit svůj uživatelský účet hosta a znovu ho pozvat.

### <a name="example"></a>Příklad

Uživatel typu host teri@gmail.com je pozván společnosti Fabrikam, která nemá nastavenu Google Federation. Teri nemá účet Microsoft. Obdrží pro ověřování jednorázové heslo.

## <a name="disable-email-one-time-passcode"></a>Zakázat jednorázové heslo pro odesílání e-mailů

Od 1. března 2021 bude funkce jednorázového hesla pro e-mail zapnutá pro všechny existující klienty a ve výchozím nastavení povolená pro nové klienty. V tuto chvíli už společnost Microsoft nebude podporovat uplatnění pozvánky tím, že pro scénáře spolupráce B2B vytvoří nespravované ("virové" nebo "just-in-time") účty Azure AD a klienty. Povolujeme funkci jednorázového hesla e-mailu, protože pro uživatele typu Host nabízí bezproblémové záložní metody ověřování. Nicméně máte možnost tuto funkci zakázat, pokud se rozhodnete ji nepoužívat.

> [!NOTE]
>
> Pokud je ve vašem tenantovi povolená funkce jednorázového hesla e-mailu a Vy ji vypnete, všichni uživatelé typu Host, kteří se znovu považovali za jednorázové heslo, se nebudou moct přihlásit. Uživatele typu Host můžete odstranit a znovu pozvat, aby se mohli znovu přihlásit pomocí jiné metody ověřování.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Zakázání funkce e-mailového hesla na jednom čase

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako globální správce Azure AD.

2. V navigačním podokně vyberte **Azure Active Directory**.

3. Vyberte externí **identity** externí  >  **nastavení spolupráce**.

4. V části **e-mail jednorázového hesla pro hosty** vyberte možnost **Zakázat e-mailová hesla pro hosty v jednom** čase.

    ![Nastavení jednorázového hesla pro e-mail](media/one-time-passcode/otp-admin-settings.png)

   > [!NOTE]
   > Pokud se zobrazí následující přepínač místo výše uvedených možností, znamená to, že jste dříve povolili, zakázali nebo přihlásili do verze Preview této funkce. Chcete-li funkci zakázat, vyberte možnost **ne** .
   >
   >![Povolit přístup k jednorázovému heslu pro odesílání e-mailů](media/delegate-invitations/enable-email-otp-opted-in.png)

5. Vyberte **Uložit**.

## <a name="note-for-public-preview-customers"></a>Poznámka pro zákazníky ve veřejné verzi Preview

Pokud jste se dříve přihlásili k e-mailu ve verzi Public Preview na e-mail, nebudete se na vás vztahovat datum března 2021 pro automatické povolení funkcí, takže vaše související obchodní procesy nebudou ovlivněny. Kromě toho se v Azure Portal v části **e-mail jednorázového hesla pro hosty** nezobrazí možnost **automaticky povolit jednorázové e-mailové heslo pro hosty v březnu 2021**. Místo toho se zobrazí následující přepínač **Ano** nebo **ne** :

![Povolit přístup k jednorázovému heslu pro odesílání e-mailů](media/delegate-invitations/enable-email-otp-opted-in.png)

Pokud byste ale chtěli tuto funkci odhlásit a povolit její automatické povolení v březnu 2021, můžete se vrátit k výchozímu nastavení pomocí [typu prostředku konfigurace metody ověřování e-mailu](https://aka.ms/exid-graphemailauth)Microsoft Graph rozhraní API. Až se vrátíte k výchozímu nastavení, v části **e-mail jednorázového hesla pro hosty** budou k dispozici následující možnosti:

- **Automaticky povolit jednorázové e-mailové heslo pro hosty v březnu 2021**. Výchozí Pokud funkce jednorázového hesla pro e-mail ještě není pro vašeho tenanta povolená, bude automaticky zapnutá v březnu 2021. Pokud chcete povolit funkci v daném čase, není nutná žádná další akce. Pokud jste tuto funkci již povolili nebo zakázali, tato možnost nebude k dispozici.

- **Povolte jednorázové e-mailové heslo pro hosty**, které jsou teď platné. Zapne pro vašeho tenanta funkci jednorázového hesla pro e-mail.

- **Zakažte jednorázové e-mailové heslo pro hosty**. Vypne funkci jednorázového hesla e-mailu pro vašeho tenanta a zabrání funkci v zapnutí v březnu 2021.
