---
title: Co je stránka pro dodatečné ověření? - Azure AD
description: Jak se dostat na stránku Dodatečné ověření zabezpečení pro dvoufaktorové ověření.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 5a7f0e10b23bf1a541fe83c3112962c38f7e1331
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062553"
---
# <a name="what-is-the-additional-verification-page"></a>Co je stránka pro dodatečné ověření?

Od někoho z IT nebo od šéfa jste dostali e-mail s informací, že k vašemu účtu organizace přidala další ověřování zabezpečení. Co to tedy znamená? Znamená to, že vaše organizace podniká další kroky k zajištění, že jste tím, za koho se při přihlášení vydáváte. Toto dodatečné ověření, známé také jako dvoufaktorové ověření, se provádí kombinací uživatelského jména, hesla a mobilního zařízení nebo telefonu.

Dvoufaktorové ověřování je bezpečnější než jen heslo, protože se spoléhá na dvě formy ověřování: něco, co víte, a něco, co máte s sebou. Něco, co znáte, je vaše heslo. Něco, co máte u sebe, je telefon nebo zařízení, které běžně nosíte. Dvoufaktorové ověření může pomoci zabránit škodlivým hackerům, aby předstírali, že jste vy, protože i když mají vaše heslo, je pravděpodobné, že nemají také vaše zařízení.

>[!Important]
>Tento článek je určen pro uživatele, kteří se pokoušejí použít dvoufaktorové ověření s pracovním nebo školním účtem alain@contoso.com(například). Pokud jste správce, který hledá informace o tom, jak zapnout dvoufaktorové ověřování pro zaměstnance nebo jiné uživatele, přečtěte si [dokumentaci k ověřování služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Kdo rozhoduje o tom, zda tuto funkci používáte?

V závislosti na typu účtu může vaše organizace rozhodnout, že musíte použít dvoufaktorové ověření, nebo se můžete rozhodnout sami.

- **Pracovní nebo školní účet.** Pokud používáte pracovní nebo školní účet (například ), je na vaší organizaci, alain@contoso.comzda musíte použít dvoufaktorové ověření spolu s konkrétními metodami ověřování. Vzhledem k tomu, že se vaše organizace rozhodla, že musíte tuto funkci používat, neexistuje žádný způsob, jak ji jednotlivě vypnout.

- **Osobní účet Microsoft.** Můžete nastavit dvoufaktorové ověření pro své osobní účty Microsoft alain@outlook.com(například). Pokud máte problémy s dvoufaktorovým ověřením a osobním účtem Microsoft, přečtěte si část [Zapnutí nebo vypnutí dvoufaktorového ověření pro svůj účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Protože se rozhodnete, zda chcete tuto funkci používat, můžete ji kdykoli zapnout a vypnout.

    >[!Note]
    >Pokud máte problémy s dvoufaktorovým ověřením a jedním z danielle@outlook.comvašich osobních účtů Microsoft (například), můžete vyzkoušet návrhy na způsob [použití dvoustupňového ověření s účtem Microsoft](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="access-the-additional-security-verification-page"></a>Přístup na stránku Další ověření zabezpečení

Po zapnutí a nastavení dvoufaktorového ověření zobrazí výzva, abyste vám poskytli další informace, které vám pomohou zabezpečit váš účet.

![Další informace vyžaduje výzvu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Přístup ke stránce Další ověření zabezpečení

1. V **Next** části **Další informace v požadované** výzvě vyberte Další.

    Zobrazí se stránka **Další ověření zabezpečení.**

2. Na stránce **Další ověření zabezpečení** se musíte rozhodnout, kterou metodu dvoufaktorového ověření použít k ověření, kdo říkáte, že jste po přihlášení k pracovnímu nebo školnímu účtu. Můžete vybrat:

    | Kontaktní metoda | Popis |
    | --- | --- |
    | Mobilní aplikace | <ul><li>**Dostávat oznámení k ověření.** Tato možnost odešle oznámení do ověřovací aplikace na smartphonu nebo tabletu. Zobrazení oznámení a pokud je legitimní, vyberte **ověřit** v aplikaci. Vaše práce nebo škola může vyžadovat, abyste před ověřením zadali KÓD PIN.</li><li>**Použijte ověřovací kód.** V tomto režimu ověřovací aplikace generuje ověřovací kód, který se aktualizuje každých 30 sekund. Na přihlašovací obrazovce zadejte nejaktuálnější ověřovací kód.<br>Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594) a [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Ověřovací telefon | <ul><li>**Telefonní hovor** umožňuje automatické hlasové volání na telefonní číslo, které zadáte. Přijměte hovor a stisknutím klávesy libra (#) na klávesnici telefonu ověřte.</li><li>**Textová zpráva** ukončí textovou zprávu obsahující ověřovací kód. Po zobrazení výzvy v textu odpovíte na textovou zprávu nebo zadejte ověřovací kód poskytnutý do přihlašovacího rozhraní.</li></ul> |
    | Telefon do kanceláře | Umístí automatický hlasový hovor na telefonní číslo, které zadáte. Přijměte hovor a stisknutím klávesy libra (#) na klávesnici telefonu ověřte. |

## <a name="next-steps"></a>Další kroky

Po přístupu na stránku **Další ověření zabezpečení** musíte vybrat a nastavit dvoufaktorovou metodu ověření:

- [Nastavení mobilního zařízení jako ověřovací metody](multi-factor-authentication-setup-phone-number.md)

- [Nastavení telefonu v kanceláři jako způsobu ověření](multi-factor-authentication-setup-office-phone.md)

- [Nastavení aplikace Microsoft Authenticator jako metody ověření](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Související prostředky

- [Správa nastavení dvoufaktorových metod ověření](multi-factor-authentication-end-user-manage-settings.md)

- [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md)

- [Přihlášení pomocí dvoufaktorového ověření](multi-factor-authentication-end-user-signin.md)

- [Získání nápovědy k dvoufaktorovému ověření](multi-factor-authentication-end-user-troubleshoot.md) 
