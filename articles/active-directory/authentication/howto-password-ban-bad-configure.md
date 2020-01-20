---
title: Jak v Azure AD zakázat slabé heslo – Azure Active Directory
description: Zakázání slabého hesla ze služby envirionment pomocí Azure AD dynamicky zakázaného passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb47b9df51803c76662b5fb4ca1fe23740e7af9a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155049"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurace vlastního seznamu zakázaných hesel

Mnoho organizací hledá své uživatele pomocí běžných místních slov, jako je například škola, sportovní tým nebo slavných osoba, která je snadno uhodnout. Vlastní seznam zakázaných hesel společnosti Microsoft umožňuje organizacím přidávat řetězce k vyhodnocení a blokování, a to i v případě, že se uživatelé a správci pokusí změnit nebo resetovat heslo.

## <a name="add-to-the-custom-list"></a>Přidat do vlastního seznamu

Konfigurace vlastního seznamu zakázaných hesel vyžaduje Azure Active Directory Premium licenci P1 nebo P2. Podrobnější informace o Azure Active Directory licencování najdete na [stránce s cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do **Azure Active Directory** > **metody ověřování** **zabezpečení** >  > **ochrana heslem**.
1. Nastavte možnost **vykonat vlastní seznam**na **Ano**.
1. Přidání řetězců do **vlastního seznamu zakázaných hesel**, jednoho řetězce na řádek
   * Vlastní seznam zakázaných hesel může obsahovat až 1000 podmínek.
   * Vlastní seznam zakázaných hesel rozlišuje velká a malá písmena.
   * Vlastní seznam zakázaných hesel je považován za běžné nahrazení znaků.
      * Příklad: "o" a "0" nebo "a" a "\@"
   * Minimální délka řetězce je 4 znaky a maximální délka je 16 znaků.
1. Po přidání všech řetězců klikněte na **Uložit**.

> [!NOTE]
> Použití aktualizací vlastního seznamu zakázaných hesel může trvat několik hodin.

> [!NOTE]
> Vlastní seznam zakázaných hesel je omezen na maximálně 1000 podmínek. Není navržený pro blokování extrémně velkých seznamů hesel. Aby bylo možné plně využít výhody vlastního seznamu zakázaných hesel, společnost Microsoft doporučuje nejprve projít a pochopit zamýšlený návrh a použití vlastního seznamu zakázaných hesel (viz [vlastní seznam zakázaných](concept-password-ban-bad.md#custom-banned-password-list)hesel) a také algoritmus pro vyhodnocení hesla (viz [jak jsou vyhodnocena hesla](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![V Azure Portal upravte vlastní seznam zakázaných hesel v části metody ověřování.](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Jak to funguje

Pokaždé, když uživatel nebo správce resetuje nebo změní heslo Azure AD, projde seznamy zakázaných hesel, aby se ověřilo, že není na seznamu. Tato Tato součást je obsažena v jakýchkoli heslech nastavených nebo změněných pomocí Azure AD.

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí resetovat heslo na něco, co by bylo zakázané, zobrazí se některá z následujících chybových zpráv:

* Vaše heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadnou možnost uhodnout heslo. Zkuste to prosím znovu s jiným heslem.
* Toto heslo bohužel nemůžete použít, protože obsahuje slova nebo znaky blokované vaším správcem. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další kroky

[Koncepční přehled seznamů zakázaných hesel](concept-password-ban-bad.md)

[Koncepční přehled ochrany heslem služby Azure AD](concept-password-ban-bad-on-premises.md)

[Povolit místní integraci se seznamy zakázaných hesel](howto-password-ban-bad-on-premises.md)
