---
title: Jednorázové ověřování hesla pro uživatele typu Host B2B – Azure AD
description: Jak používat jednorázové heslo e-mailu k ověřování uživatelů typu Host B2B bez nutnosti účet Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7961997c6a6736c154b6217ee3f21682d0c4c3fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688463"
---
# <a name="email-one-time-passcode-authentication"></a>E-mailové ověřování heslem jednorázového hesla

Tento článek popisuje, jak povolit jednorázové ověřování e-mailových hesel pro uživatele typu Host B2B. Funkce jednorázového hesla pro e-mail ověřuje uživatele typu Host B2B, když se nemůžou ověřit jiným způsobem jako Azure AD, účet Microsoft (MSA) nebo Google Federation. Při jednorázovém ověřování pomocí hesla není nutné vytvářet účet Microsoft. Když uživatel typu Host uplatňuje pozvánku nebo přistupuje ke sdílenému prostředku, může požádat o dočasný kód, který se pošle na svou e-mailovou adresu. Pak tento kód zadá, aby bylo možné pokračovat v přihlašování.

![Diagram přehledu hesla na jednom čase v e-mailu](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **Od října 2021** se funkce jednorázového hesla pro e-mail zapne pro všechny existující klienty a ve výchozím nastavení povolená pro nové klienty. Pokud nechcete povolit automatické zapnutí této funkce, můžete ji zakázat. Viz téma [deaktivace e-mailového hesla One-Time](#disable-email-one-time-passcode) .
> - Nastavení jednorázového hesla v e-mailu se přesunula v Azure Portal z **nastavení externí spolupráce** na **všechny zprostředkovatele identity**.

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

### <a name="example&quot;></a>Příklad

Uživatel typu host teri@gmail.com je pozván společnosti Fabrikam, která nemá nastavenu Google Federation. Teri nemá účet Microsoft. Obdrží pro ověřování jednorázové heslo.

## <a name=&quot;disable-email-one-time-passcode&quot;></a>Zakázat jednorázové heslo pro odesílání e-mailů

Od října 2021 se funkce jednorázového hesla pro e-mail zapne pro všechny existující klienty a ve výchozím nastavení povolená pro nové klienty. V tuto chvíli už společnost Microsoft nebude podporovat uplatnění pozvánky tím, že pro scénáře spolupráce B2B vytvoří nespravované (&quot;virové&quot; nebo &quot;just-in-time") účty Azure AD a klienty. Povolujeme funkci jednorázového hesla e-mailu, protože pro uživatele typu Host nabízí bezproblémové záložní metody ověřování. Nicméně máte možnost tuto funkci zakázat, pokud se rozhodnete ji nepoužívat.

> [!NOTE]
>
> Pokud je ve vašem tenantovi povolená funkce jednorázového hesla e-mailu a Vy ji vypnete, všichni uživatelé typu Host, kteří se znovu považovali za jednorázové heslo, se nebudou moct přihlásit. Uživatele typu Host můžete odstranit a znovu pozvat, aby se mohli znovu přihlásit pomocí jiné metody ověřování.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Zakázání funkce e-mailového hesla na jednom čase

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako globální správce Azure AD.

2. V navigačním podokně vyberte **Azure Active Directory**.

3. Vyberte **externí identity**  >  **všichni zprostředkovatelé identity**.

4. Vyberte možnost **jednorázové heslo e-mailu** a pak **pro hosty vyberte Zakázat jednorázové heslo e-mailu**.

   > [!NOTE]
   > Nastavení jednorázového hesla v e-mailu se přesunula v Azure Portal z **nastavení externí spolupráce** na **všechny zprostředkovatele identity**.
   > Pokud se místo možnosti e-mailové adresy pro jednorázové heslo zobrazí přepínací tlačítko, znamená to, že jste dříve povolili, zakázali nebo přihlásili do verze Preview této funkce. Chcete-li funkci zakázat, vyberte možnost **ne** .
   >
   >![Zakázané přepínání hesla jedním časem](media/one-time-passcode/enable-email-otp-disabled.png)

5. Vyberte **Uložit**.

## <a name="note-for-public-preview-customers"></a>Poznámka pro zákazníky ve veřejné verzi Preview

Pokud jste se dříve přihlásili k e-mailu ve verzi Public Preview na e-mail, datum v říjnu 2021 pro automatické povolování funkcí se na vás netýká, takže vaše související obchodní procesy nebudou ovlivněny. Kromě toho se v Azure Portal v části **e-mail jednorázového hesla pro hosty** nezobrazí možnost **automaticky povolit jednorázové e-mailové heslo pro hosty od října 2021**. Místo toho se zobrazí následující přepínač **Ano** nebo **ne** :

![Heslo pro jednorázové e-maily s výslovným souhlasem](media/one-time-passcode/enable-email-otp-opted-in.png)

Pokud byste ale chtěli tuto funkci odhlásit a povolit její automatické povolení v říjnu 2021, můžete se vrátit k výchozímu nastavení pomocí [typu prostředku konfigurace metody ověřování e-mailu](/graph/api/resources/emailauthenticationmethodconfiguration)Microsoft Graph rozhraní API. Až se vrátíte k výchozímu nastavení, v části **e-mail jednorázového hesla pro hosty** budou k dispozici následující možnosti:

![Povolit přístup k jednorázovému heslu pro odesílání e-mailů](media/one-time-passcode/email-otp-options.png)

- **Automaticky povolit jednorázové e-mailové heslo pro hosty od října 2021**. Výchozí Pokud pro vašeho tenanta ještě není povolená funkce pro jednorázové e-mailové heslo, bude automaticky zapnutá od října 2021. Pokud chcete povolit funkci v daném čase, není nutná žádná další akce. Pokud jste tuto funkci již povolili nebo zakázali, tato možnost nebude k dispozici.

- **Povolte jednorázové e-mailové heslo pro hosty**, které jsou teď platné. Zapne pro vašeho tenanta funkci jednorázového hesla pro e-mail.

- **Zakažte jednorázové e-mailové heslo pro hosty**. Vypne funkci jednorázového hesla e-mailu pro vašeho tenanta a zabrání funkci v zapnutí v říjnu 2021.

## <a name="note-for-azure-us-government-customers"></a>Poznámka pro zákazníky Azure pro státní správu USA

Funkce pro jednorázové e-mailové heslo je ve výchozím nastavení v cloudu pro státní správu Azure USA zakázaná.  

 ![Heslo jednorázového e-mailu je zakázané](media/one-time-passcode/enable-email-otp-disabled.png)

Povolení funkce jednorázového hesla e-mailu v cloudu pro státní správu Azure USA:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce Azure AD.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. Vyberte **Nastavení organizačních vztahů**   >  ****.

   > [!NOTE]
   > - Pokud nevidíte **vztahy organizace**, vyhledejte v horní části panelu hledání "externí identity".

4. Vyberte možnost **e-mailového hesla s jedním časem** a pak vyberte **Ano**.
5. Vyberte **Uložit**.

Další informace o současných omezeních najdete v tématu [cloudy pro státní správu Azure USA](current-limitations.md#azure-us-government-clouds).