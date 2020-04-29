---
title: Vlastní ovládací prvky v podmíněném přístupu Azure AD
description: Přečtěte si, jak vlastní ovládací prvky v Azure Active Directory podmíněný přístup fungují.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80050539"
---
# <a name="custom-controls-preview"></a>Vlastní ovládací prvky (Preview)

Vlastní ovládací prvky jsou funkcí verze Preview Azure Active Directory. Při použití vlastních ovládacích prvků jsou vaši uživatelé přesměrováni na kompatibilní službu, aby splnila požadavky na ověření mimo Azure Active Directory. Aby bylo možné tento ovládací prvek vyhovět, bude prohlížeč uživatele přesměrován na externí službu, provede požadované ověření a pak bude přesměrován zpět na Azure Active Directory. Azure Active Directory ověří odpověď, a pokud byl uživatel úspěšně ověřen nebo ověřen, uživatel pokračuje v toku podmíněného přístupu.

> [!NOTE]
> Další informace o změnách, které plánujeme na možnosti vlastního ovládacího prvku, najdete v článku únor 2020. února, [co je nového](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls).

## <a name="creating-custom-controls"></a>Vytváření vlastních ovládacích prvků

Vlastní ovládací prvky fungují s omezeným počtem schválených zprostředkovatelů ověřování. Chcete-li vytvořit vlastní ovládací prvek, je třeba nejprve kontaktovat poskytovatele, kterého chcete využít. Každý poskytovatel od poskytovatele Microsoftu má svůj vlastní proces a požadavky na registraci, přihlášení k odběru nebo jinou součást služby a k označení, že chcete integraci s podmíněným přístupem. Od tohoto okamžiku vám poskytovatel poskytne blok dat ve formátu JSON. Tato data umožňují poskytovateli a podmíněným přístupům spolupracovat pro vašeho tenanta, vytvoří nový ovládací prvek a určí, jak podmíněný přístup může zjistit, jestli se uživatelé úspěšně provedli ověření u poskytovatele.

Zkopírujte data JSON a pak je vložte do příslušného textového pole. Neprovádějte žádné změny JSON, pokud výslovně nerozumíte změně, kterou provádíte. Provedení jakékoli změny by mohlo způsobit přerušení spojení mezi poskytovatelem a společností Microsoft a tím, že vás a vaši uživatelé budou moci uzamknout z vašich účtů.

Možnost vytvořit vlastní ovládací prvek je v části **Správa** na stránce **podmíněný přístup** .

![Řízení](./media/controls/82.png)

Kliknutím na **Nový vlastní ovládací prvek**otevře okno s textovým polem pro data JSON ovládacího prvku.  

![Řízení](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Odstranění vlastních ovládacích prvků

Pokud chcete odstranit vlastní ovládací prvek, musíte nejdřív zkontrolovat, že se nepoužívá v žádné zásadě podmíněného přístupu. Po dokončení:

1. Přejít na seznam vlastních ovládacích prvků
1. Klikněte na...  
1. Vyberte **Odstranit**.

## <a name="editing-custom-controls"></a>Úprava vlastních ovládacích prvků

Chcete-li upravit vlastní ovládací prvek, je nutné odstranit aktuální ovládací prvek a vytvořit nový ovládací prvek s aktualizovanými informacemi.

## <a name="known-limitations"></a>Známá omezení

Vlastní ovládací prvky nejde používat s automatizací Identity Protection, která vyžaduje Azure Multi-Factor Authentication, Samoobslužné resetování hesla Azure AD (SSPR), dodržování požadavků deklarace identity na vícefaktorové ověřování nebo zvýšení úrovně rolí v Privileged identity Manageru (PIM).

## <a name="next-steps"></a>Další kroky

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

- [Režim pouze sestav](concept-conditional-access-report-only.md)

- [Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
