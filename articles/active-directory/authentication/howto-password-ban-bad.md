---
title: Jak zakázat hesel ve službě Azure AD
description: Zakázat slabý hesla z vaší envirionment s Azure AD dynamicky zakázané passwrords
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 0c1517f94d4a6d59077b62614eec8fef665b1529
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296138"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurace seznamu zakázaných vlastního hesla

|     |
| --- |
| Ochrana heslem Azure AD a seznamu zakázaných vlastního hesla jsou funkce verze public preview služby Azure Active Directory. Další informace o verze Preview najdete v tématu [doplňkové podmínky použití pro Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Mnoho organizací najít že jejich uživatelé vytvářet hesla pomocí běžných místní slova, jako jsou školy, sportu tým nebo famous osoba, necháte snadno uhodnout. Seznam zakázaných vlastního hesla společnosti Microsoft umožňuje organizacím přidat řetězce k vyhodnocení a blokovat, kromě na globální zakázané seznamu heslo, když uživatelé a správci pokus o změnu nebo resetování hesla.

## <a name="add-to-the-custom-list"></a>Přidejte do seznamu vlastní

Konfigurace seznamu zakázaných vlastního hesla vyžaduje licenci Azure Active Directory Premium P1 a P2. Podrobné informace o licencování Azure Active Directory, najdete v článku [Azure Active Directory, na stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory/). |

1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory**, **metody ověřování**, pak **heslo protection (Preview)**.
1. Nastavte možnost **vlastní seznamu vynutit**do **Ano**.
1. Přidat řetězce **vlastní zakázané heslo seznamu**, jeden řetězec na každý řádek
   * Vlastní zakázaného hesla seznam může obsahovat až 1000 slova.
   * V seznamu zakázaných vlastního hesla nerozlišuje velká a malá písmena.
   * V seznamu zakázaných vlastního hesla zvažuje běžné nahrazování znaků.
      * Příklad: "o" a "0" nebo "a" a "@"
   * Minimální délka řetězce je 4 znaky a maximální hodnota je 16 znaků.
1. Pokud jste přidali všechny řetězce, klikněte na tlačítko **Uložit**.

> [!NOTE]
> Může trvat několik hodin pro aktualizace do seznamu zakázaných vlastního hesla má být použita.

![Upravte seznam zakázaných vlastního hesla v rámci metod ověřování na portálu Azure](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Jak to funguje

Pokaždé, když uživatel nebo správce vynuluje nebo změny hesel služby Azure AD toky pomocí seznamů zakázaných heslo k potvrzení, že není na seznamu. Tato kontrola je součástí všechna hesla, nastavit nebo změnit pomocí Azure AD.

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí resetovat heslo na jinou hodnotu, která může být vyloučen, zobrazí se následující chybová zpráva:

Bohužel heslo obsahuje slova, fráze nebo vzor, který umožňuje snadno uhodnutelných heslo. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další postup

[Koncepční přehled seznamů zakázaných heslo](concept-password-ban-bad.md)

[Koncepční přehled ochrany hesel Azure AD](concept-password-ban-bad-on-premises.md)

[Povolit integraci místní pomocí seznamů zakázaných heslo](howto-password-ban-bad-on-premises.md)
