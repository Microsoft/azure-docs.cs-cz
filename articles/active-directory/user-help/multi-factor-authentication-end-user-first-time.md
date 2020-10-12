---
title: Co je stránka pro dodatečné ověření? – Azure AD
description: Jak se dostat na stránku dodatečného ověření zabezpečení pro dvojúrovňové ověřování
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e5b07f8f7ae766d110c87a495a3e1623b815e526
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88795979"
---
# <a name="what-is-the-additional-verification-page"></a>Co je stránka pro dodatečné ověření?

Vaše organizace přijímá dodatečné kroky, abyste měli jistotu, že jste se přihlásili. Toto dodatečné ověření zabezpečení se označuje také jako dvojúrovňové ověřování. Skládá se z kombinace uživatelského jména, hesla a mobilního zařízení nebo telefonu. Pokud je pro účet Microsoft, jako je třeba, vypnout dvojúrovňové ověřování alain@outlook.com , postupujte podle pokynů v tématu [Zapnutí nebo vypnutí dvojúrovňového ověřování pro účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

<center>

![Obrázek metod koncepčního ověřování](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Dvojúrovňové ověřování je bezpečnější než heslo, protože spoléhá na dvě formy ověřování:

- Něco, co znáte, jako třeba heslo.
- Něco, co máte, jako je telefon nebo jiné zařízení, které jste provedli.

Dvojúrovňové ověřování může přispět k zastavení škodlivého hackera před tím, než budete mít v úmyslu. I když mají vaše heslo, lichá je, že nemají vaše zařízení.

>[!Important]
>Pokud jste správcem a hledáte informace o tom, jak zapnout dvojúrovňové ověřování pro zaměstnance nebo jiné uživatele, přečtěte si [dokumentaci k ověřování Azure Active Directory](../authentication/index.yml). Tento článek je určený pro uživatele, kteří se pokoušejí použít dvojúrovňové ověřování pomocí pracovního nebo školního účtu (například alain@contoso.com ).

## <a name="who-decides-if-you-use-this-feature"></a>Kdo se rozhodne, jestli tuto funkci používáte?

Pokud se rozhodnete, zda použijete dvojúrovňové ověřování, záleží na tom, jaký typ účtu máte:

- **Pracovní nebo školní účet.** Pokud používáte pracovní nebo školní účet (například), je to alain@contoso.com až do vaší organizace bez ohledu na to, jestli používáte dvojúrovňové ověřování společně s konkrétními metodami ověřování. Vzhledem k tomu, že vaše organizace rozhodla použít tuto funkci, neexistuje způsob, jak ji individuálně vypnout.

- **Osobní účet Microsoft.** Pro osobní účty Microsoft (například) si můžete nastavit dvojúrovňové ověřování alain@outlook.com . Můžete ho zapnout nebo vypnout kdykoli budete chtít, a to pomocí jednoduchých pokynů v tématu [Zapnutí nebo vypnutí dvojúrovňového ověřování pro vaše účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

    >[!Note]
    >Pokud máte jiné problémy se dvojúrovňovém ověřováním a jedním z vašich osobních účtů Microsoft, je k dispozici více návrhů na [použití dvoustupňového ověřování s vaším účet Microsoft](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="open-the-additional-security-verification-page"></a>Otevřete stránku další ověření zabezpečení.

Když vaše organizace zapne dvojúrovňové ověřování, zobrazí se při každém přihlášení výzva k zadání dalších informací, které vám pomůžou zajistit zabezpečení vašeho účtu.

![Další informace vyžadovat výzvu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Přístup k další stránce ověření zabezpečení

1. V části **Další informace požadované** na příkazovém řádku vyberte **Další** .

    Zobrazí se stránka **Další ověření zabezpečení** .

2. Na stránce **Další ověření zabezpečení** vyberte metodu dvou faktorů ověřování, kterou chcete použít k ověření, na koho jste se přihlásili k pracovnímu nebo školnímu účtu. Můžete vybrat:

    | Kontakt – metoda | Description |
    | --- | --- |
    | Mobilní aplikace | <ul><li>**Dostanou oznámení k ověření.** Tato možnost vloží oznámení do aplikace ověřovatele na telefonu Smartphone nebo tabletu. Podívejte se na oznámení a pokud je legitimní, vyberte v aplikaci **ověřit** . Vaše práce nebo škola může vyžadovat zadání kódu PIN před ověřením.</li><li>**Použijte ověřovací kód.** V tomto režimu aplikace vygeneruje ověřovací kód, který se aktualizuje každých 30 sekund. Do přihlašovací obrazovky zadejte nejaktuálnější ověřovací kód.<br>Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594) a [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefon pro ověření | <ul><li>**Telefonní hovor** vede automatizované hlasové volání na telefonní číslo, které zadáte. Přivolejte volání a stisknutím křížku (#) na klávesnici telefonu proveďte ověření.</li><li>**Textová zpráva** končí textovou zprávou obsahující ověřovací kód. Po zobrazení výzvy v textu odpovězte buď na textovou zprávu, nebo zadejte ověřovací kód, který jste zadali do přihlašovacího rozhraní.</li></ul> |
    | Telefon do kanceláře | Vloží automatizované hlasové volání do telefonního čísla, které zadáte. Přivolejte volání a stisknutím křížku (#) na klávesnici telefonu proveďte ověření. |

## <a name="next-steps"></a>Další kroky

Po výběru metody dvou faktorů ověřování na stránce **Další ověření zabezpečení** je nutné ji nastavit:

- [Nastavení mobilního zařízení jako metody ověřování](multi-factor-authentication-setup-phone-number.md)

- [Nastavte si telefon do kanceláře jako metodu ověřování.](multi-factor-authentication-setup-office-phone.md)

- [Nastavení aplikace Microsoft Authenticator jako metody ověřování](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Související prostředky

- [Přihlášení Pomocí dvojúrovňového ověřování](multi-factor-authentication-end-user-signin.md)

- [Získat pomoc se dvojúrovňovém ověřováním](multi-factor-authentication-end-user-troubleshoot.md)