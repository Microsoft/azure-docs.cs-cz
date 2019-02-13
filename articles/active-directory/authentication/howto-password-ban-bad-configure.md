---
title: Jak zakázat hesla ve službě Azure AD
description: Zakázat Slabá hesla z vaší envirionment s Azure AD dynamicky zakázaná passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30627994a8fe8cfe73e7945dadf2153ea60860b8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175128"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurace seznamu vlastních zakázaných hesel

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Mnoho organizací zjišťuje, že se že jejich uživatelé vytvářet hesla pomocí běžných místní slova, jako jsou školy, sportovní tým nebo slavných osoby, opuštění je snadno uhodnutelné. Seznam vlastních zakázaných hesel od Microsoftu umožňuje organizacím přidejte řetězce k vyhodnocení a blokovat, kromě globálních zakázané seznamu heslo, když uživatelé a správci pokus o změnu nebo resetování hesla.

## <a name="add-to-the-custom-list"></a>Přidání vlastního seznamu

Konfigurace seznamu zakázaných hesel vlastní vyžaduje licenci Azure Active Directory Premium P1 nebo P2. Podrobné informace o licencování Azure Active Directory, najdete v článku [Azure Active Directory, na stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory/). |

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory**, **metody ověřování**, pak **ochrana heslem (Preview)**.
1. Nastavte možnost **vynutit vlastní seznam**do **Ano**.
1. Přidejte do řetězce **vlastní zakázané seznam hesel**, jeden řetězec na řádek
   * Seznam vlastních zakázaných hesel může obsahovat až 1000 slova.
   * Seznam vlastních zakázaných hesel je velká a malá písmena.
   * Seznam vlastních zakázaných hesel bere v úvahu běžné náhradní znak.
      * Příklad: "o" a "0" nebo "a" a "\@"
   * Minimální délka řetězce je čtyři znaky a maximální hodnota je 16 znaků.
1. Pokud jste přidali všechny řetězce, klikněte na tlačítko **Uložit**.

> [!NOTE]
> To může trvat několik hodin aktualizací do seznamu zakázaných hesel vlastní uplatňovat.

![Upravit vlastní zakázaných hesel seznamu metod ověřování na webu Azure Portal](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Jak to funguje

Pokaždé, když uživatel nebo správce obnoví nebo se změní heslo k Azure AD se prochází seznamy zakázaných hesel, které chcete potvrdit, že není na seznamu. Tato kontrola je součástí všechna hesla, nastavit nebo změnit pomocí služby Azure AD.

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí o resetování hesla na něco, co může být vyloučen, zobrazí se následující chybová zpráva:

Vaše heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadno uhodnutelných heslo. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další postup

[Koncepční přehled seznamy zakázaných hesel](concept-password-ban-bad.md)

[Koncepční přehled ochrany hesla Azure AD](concept-password-ban-bad-on-premises.md)

[Povolit místní integraci se seznamy zakázaných hesel](howto-password-ban-bad-on-premises.md)
