---
title: Jak zakázat Slabá hesla ve službě Azure AD – Azure Active Directory
description: Zakázat Slabá hesla z vaší envirionment s Azure AD dynamicky zakázaná passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f531174c889948308e27109ab4fd80a481ec6bdc
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798191"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurace seznamu vlastních zakázaných hesel

Mnoho organizací zjišťuje, že se že jejich uživatelé vytvářet hesla pomocí běžných místní slova, jako jsou školy, sportovní tým nebo slavných osoby, opuštění je snadno uhodnutelné. Seznam vlastních zakázaných hesel od Microsoftu umožňuje organizacím přidejte řetězce k vyhodnocení a blokovat, kromě globálních zakázané seznamu heslo, když uživatelé a správci pokus o změnu nebo resetování hesla.

## <a name="add-to-the-custom-list"></a>Přidání vlastního seznamu

Konfigurace seznamu zakázaných hesel vlastní vyžaduje licenci Azure Active Directory Premium P1 nebo P2. Podrobné informace o licencování Azure Active Directory, najdete v článku [Azure Active Directory, na stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory/).

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory**, **metody ověřování**, pak **ochrana heslem**.
1. Nastavte možnost **vynutit vlastní seznam**do **Ano**.
1. Přidejte do řetězce **vlastní zakázané seznam hesel**, jeden řetězec na řádek
   * Seznam vlastních zakázaných hesel může obsahovat až 1 000 podmínky.
   * Seznam vlastních zakázaných hesel je velká a malá písmena.
   * Seznam vlastních zakázaných hesel bere v úvahu běžné náhradní znak.
      * Příklad: "o" a "0" nebo "a" a "\@"
   * Minimální délka řetězce je čtyři znaky a maximální hodnota je 16 znaků.
1. Pokud jste přidali všechny řetězce, klikněte na tlačítko **Uložit**.

> [!NOTE]
> To může trvat několik hodin aktualizací do seznamu zakázaných hesel vlastní uplatňovat.

> [!NOTE]
> Seznam vlastních zakázaných hesel je omezen na maximálně 1000 podmínky. Není určená pro blokování velmi rozsáhlých seznamů hesel. Abyste mohli plně využít výhod seznamu vlastních zakázaných hesel, společnost Microsoft doporučuje, abyste nejdřív zkontrolovali a pochopit zamýšleného návrhu a využití ze seznamu vlastní zakázaných hesel (naleznete v tématu [vlastní zakázané seznam hesel](concept-password-ban-bad.md#custom-banned-password-list)), a také heslo algoritmus vyhodnocení (viz [jak se vyhodnocují hesla](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Upravit vlastní zakázaných hesel seznamu metod ověřování na webu Azure Portal](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Jak to funguje

Pokaždé, když uživatel nebo správce obnoví nebo se změní heslo k Azure AD se prochází seznamy zakázaných hesel, které chcete potvrdit, že není na seznamu. Tato kontrola je součástí všechna hesla, nastavit nebo změnit pomocí služby Azure AD.

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí o resetování hesla na něco, co může být vyloučen, zobrazí jednu z následujících chybových zpráv:

* Vaše heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadno uhodnutelných heslo. Zkuste to prosím znovu s jiným heslem.
* Bohužel nemůžete použít toto heslo, protože obsahuje slova nebo znaky, které byl zablokován správcem. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další postup

[Koncepční přehled seznamy zakázaných hesel](concept-password-ban-bad.md)

[Koncepční přehled ochrany hesla Azure AD](concept-password-ban-bad-on-premises.md)

[Povolit místní integraci se seznamy zakázaných hesel](howto-password-ban-bad-on-premises.md)
