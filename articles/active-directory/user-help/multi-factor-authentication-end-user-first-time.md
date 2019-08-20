---
title: Přehled metod dvou faktorů ověřování – Azure Active Directory | Microsoft Docs
description: Přehled o tom, jak nastavit metody dvou faktorů ověřování pro dvojúrovňové ověřování.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bba343ac73fd0df575ae1e8a83c589a0bc15ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616170"
---
# <a name="set-up-your-two-factor-verification-methods-overview"></a>Přehled metod ověřování dvou faktorů

Vaše organizace zapnula dvojúrovňové ověřování, což znamená, že přihlášení k vašemu pracovnímu nebo školnímu účtu teď vyžaduje kombinaci vašeho uživatelského jména, hesla a mobilního zařízení nebo telefonu. Vaše organizace toto dodatečné ověření zapnula, protože je bezpečnější než heslo, spoléhá se na dvě formy ověřování: něco, co znáte a co s vámi máte. Dvojúrovňové ověřování může přispět k tomu, aby nedocházelo ke škodlivým podvodníkům, protože by to bylo v úmyslu, protože i když mají vaše heslo, lichá, že nemají vaše zařízení.

>[!Important]
>Tento obsah je určený pro uživatele. Pokud jste správce, najdete další informace o nastavení a správě vašeho prostředí Azure Active Directory (Azure AD) v [dokumentaci k Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="who-decides-if-you-use-this-feature"></a>Kdo se rozhodne, jestli tuto funkci používáte?

V závislosti na typu účtu může vaše organizace rozhodnout, že je nutné použít dvojúrovňové ověřování, nebo se můžete rozhodnout sami.

- **Pracovní nebo školní účet.** Pokud používáte pracovní nebo školní účet (například alain@contoso.com), je to až do vaší organizace bez ohledu na to, jestli musíte použít dvojúrovňové ověřování společně s konkrétními metodami ověřování. Vzhledem k tomu, že vaše organizace rozhodla použít tuto funkci, neexistuje způsob, jak ji individuálně vypnout.

- **Osobní účet Microsoft.** Pro osobní účty Microsoft (například alain@outlook.com) si můžete nastavit dvojúrovňové ověřování. Pokud máte problémy se dvojúrovňovém ověřováním a osobním účet Microsoft, přečtěte si téma [Zapnutí a vypnutí dvojúrovňového ověřování pro účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Vzhledem k tomu, že se rozhodnete, jestli chcete tuto funkci používat, můžete ji zapnout nebo vypnout, kdykoli budete chtít.

## <a name="access-the-additional-security-verification-page"></a>Přístup k další stránce ověření zabezpečení

Jakmile vaše organizace zapne a nastaví dvojúrovňové ověřování, zobrazí se výzva, abyste zadali další informace, které vám pomůžou zajistit zabezpečení vašeho účtu.

![Další informace vyžadovat výzvu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Přístup k další stránce ověření zabezpečení

1. V části **Další informace požadované** na příkazovém řádku vyberte **Další** .

    Zobrazí se stránka **Další ověření zabezpečení** .

2. Na stránce **Další ověření zabezpečení** musíte rozhodnout, kterou ověřovací metodu ověření použít k ověření, na koho jste se přihlásili k pracovnímu nebo školnímu účtu. Můžete vybrat:

    | Způsob kontaktování | Popis |
    | --- | --- |
    | Mobilní aplikace | <ul><li>**Dostanou oznámení k ověření.** Tato možnost vloží oznámení do aplikace ověřovatele na telefonu Smartphone nebo tabletu. Podívejte se na oznámení a pokud je legitimní, vyberte v aplikaci **ověřit** . Vaše práce nebo škola může vyžadovat zadání kódu PIN před ověřením.</li><li>**Použijte ověřovací kód.** V tomto režimu aplikace ověřovatele vygeneruje ověřovací kód, který se aktualizuje každých 30 sekund. Do přihlašovací obrazovky zadejte nejaktuálnější ověřovací kód.<br>Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594) a [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefon pro ověření | <ul><li>**Telefonní hovor** vede automatizované hlasové volání na telefonní číslo, které zadáte. Přivolejte volání a stisknutím křížku (#) na klávesnici telefonu proveďte ověření.</li><li>**Textová zpráva** končí textovou zprávou obsahující ověřovací kód. Po zobrazení výzvy v textu odpovězte buď na textovou zprávu, nebo zadejte ověřovací kód, který jste zadali do přihlašovacího rozhraní.</li></ul> |
    | Telefon do kanceláře | Vloží automatizované hlasové volání do telefonního čísla, které zadáte. Přivolejte volání a stisknutím křížku (#) na klávesnici telefonu proveďte ověření. |

## <a name="next-steps"></a>Další kroky

Po otevření stránky pro **Další ověření zabezpečení** je nutné vybrat a nastavit metodu ověřování dvou faktorů:

- [Nastavení mobilního zařízení jako metody ověřování](multi-factor-authentication-setup-phone-number.md)

- [Nastavte si telefon do kanceláře jako metodu ověřování.](multi-factor-authentication-setup-office-phone.md)

- [Nastavení aplikace Microsoft Authenticator jako metody ověřování](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Související prostředky

- [Správa nastavení dvojúrovňové metody ověřování](multi-factor-authentication-end-user-manage-settings.md)

- [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md)

- [Přihlášení Pomocí dvojúrovňového ověřování](multi-factor-authentication-end-user-signin.md)

- [Získat pomoc se dvojúrovňovém ověřováním](multi-factor-authentication-end-user-troubleshoot.md) 
