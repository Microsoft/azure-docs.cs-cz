---
title: Vlastní ovládací prvky v podmíněném přístupu Azure AD
description: Přečtěte si, jak vlastní ovládací prvky v Azure Active Directory podmíněný přístup fungují.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7585c91e42b2d3591532756c1ead9ea60b7035e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94837579"
---
# <a name="custom-controls-preview"></a>Vlastní ovládací prvky (Preview)

Vlastní ovládací prvky jsou funkcí verze Preview Azure Active Directory. Při použití vlastních ovládacích prvků jsou vaši uživatelé přesměrováni na kompatibilní službu, aby splnila požadavky na ověření mimo Azure Active Directory. Aby bylo možné tento ovládací prvek vyhovět, bude prohlížeč uživatele přesměrován na externí službu, provede požadované ověření a pak bude přesměrován zpět na Azure Active Directory. Azure Active Directory ověří odpověď, a pokud byl uživatel úspěšně ověřen nebo ověřen, uživatel pokračuje v toku podmíněného přístupu.

> [!NOTE]
> Další informace o změnách, které plánujeme na možnosti vlastního ovládacího prvku, najdete v části únor 2020 – [co je nového](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls).

## <a name="creating-custom-controls"></a>Vytváření vlastních ovládacích prvků

Vlastní ovládací prvky fungují s omezeným počtem schválených zprostředkovatelů ověřování. Chcete-li vytvořit vlastní ovládací prvek, je třeba nejprve kontaktovat poskytovatele, kterého chcete využít. Každý poskytovatel od poskytovatele Microsoftu má svůj vlastní proces a požadavky na registraci, přihlášení k odběru nebo jinou součást služby a k označení, že chcete integraci s podmíněným přístupem. Od tohoto okamžiku vám poskytovatel poskytne blok dat ve formátu JSON. Tato data umožňují poskytovateli a podmíněným přístupům spolupracovat pro vašeho tenanta, vytvoří nový ovládací prvek a určí, jak podmíněný přístup může zjistit, jestli se uživatelé úspěšně provedli ověření u poskytovatele.

Zkopírujte data JSON a pak je vložte do příslušného textového pole. Neprovádějte žádné změny JSON, pokud výslovně nerozumíte změně, kterou provádíte. Provedení jakékoli změny by mohlo způsobit přerušení spojení mezi poskytovatelem a společností Microsoft a tím, že vás a vaši uživatelé budou moci uzamknout z vašich účtů.

Možnost vytvořit vlastní ovládací prvek je v části **Správa** na stránce **podmíněný přístup** .

![Vlastní rozhraní ovládacích prvků v podmíněném přístupu](./media/controls/custom-controls-conditional-access.png)

Kliknutím na **Nový vlastní ovládací prvek** otevře okno s textovým polem pro data JSON ovládacího prvku.  

![Nový vlastní ovládací prvek](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>Odstranění vlastních ovládacích prvků

Pokud chcete odstranit vlastní ovládací prvek, musíte nejdřív zkontrolovat, že se nepoužívá v žádné zásadě podmíněného přístupu. Po dokončení:

1. Přejít na seznam vlastních ovládacích prvků
1. Klikněte na...  
1. Vyberte **Odstranit**.

## <a name="editing-custom-controls"></a>Úprava vlastních ovládacích prvků

Chcete-li upravit vlastní ovládací prvek, je nutné odstranit aktuální ovládací prvek a vytvořit nový ovládací prvek s aktualizovanými informacemi.

## <a name="known-limitations"></a>Známá omezení

Vlastní ovládací prvky nejde používat s automatizací Identity Protection, která vyžaduje Azure AD Multi-Factor Authentication, Samoobslužné resetování hesla Azure AD (SSPR), dodržování požadavků deklarace identity na vícefaktorové ověřování, aby bylo možné zvyšovat role v Privileged identity Manageru (PIM) jako součást registrace zařízení v Intune, nebo při připojování zařízení ke službě Azure AD.

## <a name="next-steps"></a>Další kroky

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

- [Režim pouze sestav](concept-conditional-access-report-only.md)

- [Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
