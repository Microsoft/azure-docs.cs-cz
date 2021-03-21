---
title: Povolit omezení stáří v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si, jak identifikovat podverze pomocí vaší aplikace.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522417"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Povolit omezení stáří v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Omezení stáří v Azure Active Directory B2C (Azure AD B2C) umožňuje identifikovat nezletilé, které chtějí používat vaši aplikaci, a to i bez souhlasu rodičů. Můžete se rozhodnout, že nechcete, aby se od přihlášení do aplikace zablokoval. Nebo umožněte použití k dokončení přihlášení a poskytněte aplikaci vedlejší stav. 

>[!IMPORTANT]
>Tato funkce je ve verzi Public Preview. Nepoužívejte funkci pro produkční aplikace.
>

Pokud je pro tok uživatele povolené věkové omezení, uživatelé budou požádáni o datum narození a zemi bydliště. Pokud se uživatel přihlásí, že dříve nezadal informace, bude nutné ho zadat při příštím přihlášení. Pravidla se aplikují při každém přihlášení uživatele.

![Snímek obrazovky s informacemi o tom, jak získat informace o platnosti](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C používá informace, které uživatel zadá, k určení, jestli se jedná o vedlejší. Pole **ageGroup** se pak ve svém účtu aktualizuje. Hodnota může být `null` , `Undefined` ,, `Minor` `Adult` a `NotAdult` .  Pole **ageGroup** a **consentProvidedForMinor** se pak použijí k výpočtu hodnoty **legalAgeGroupClassification**.


## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Nastavení tenanta pro věkové omezení

Pokud chcete použít omezení stáří v uživatelském toku, musíte nakonfigurovat, aby měl váš tenant další vlastnosti.

1. V horní nabídce vyberte adresář **a filtr předplatných** a ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. Vyberte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** , vyhledejte a vyberte **Azure AD B2C**.
1. V nabídce na levé straně vyberte **vlastnosti** pro vašeho tenanta.
1. V části omezení **stáří** vyberte **Konfigurovat**.
1. Počkejte na dokončení operace a váš tenant se nastaví na stáří.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Povolení stáří v uživatelském toku

Po nastavení tenanta pro použití omezení stáří můžete tuto funkci použít v [uživatelských tocích](user-flow-versions.md) , kde je povolená. Dobu platnosti můžete povolit pomocí následujících kroků:

1. Vytvořte tok uživatele s povoleným omezením stáří.
1. Po vytvoření toku uživatele vyberte v nabídce možnost **vlastnosti** .
1. V části omezení **stáří** vyberte **povoleno**.
1. Pro **registraci nebo přihlášení** vyberte, jak chcete spravovat uživatele:
    - Umožněte pro přístup k vaší aplikaci nezletilé.
    - Zablokuje jenom ty, které mají souhlas s přístupem k vaší aplikaci jenom ty menší.
    - Zablokuje všem nezletilým přístup k vaší aplikaci.
1. **V případě blokování** vyberte jednu z následujících možností:
    - **Odeslat JSON zpátky do aplikace** – Tato možnost pošle odpověď zpátky do aplikace, která je zablokovaná.
    - **Zobrazit chybovou stránku** – uživateli se zobrazí stránka s přehledem, že nemají přístup k aplikaci.

## <a name="test-your-user-flow"></a>Testování toku uživatele

1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **tok spuštění uživatele** .
1. Přihlaste se pomocí místního nebo sociálního účtu. Pak vyberte svou zemi bydliště a datum narození, které simuluje nezletilý. 
1. Opakujte test a vyberte datum narození, které simuluje dospělé.  

Když se přihlásíte jako vedlejší, měla by se zobrazit následující chybová zpráva: *bohužel jste zablokovali vaše přihlášení. Ochrana osobních údajů a bezpečnostní zákony online ve vaší zemi brání přístupu k účtům patřícím do podřízených objektů.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Povolení stáří ve vašich vlastních zásadách

1. Získejte příklad zásady uzavírání stáří na [GitHubu](https://github.com/azure-ad-b2c/samples/tree/master/age-gating).
1. V každém souboru nahraďte řetězec `yourtenant` názvem vašeho tenanta Azure AD B2C. Například pokud je název vašeho tenanta B2C *contosob2c*, všechny instance `yourtenant.onmicrosoft.com` se stanou `contosob2c.onmicrosoft.com` .
1. Nahrajte soubory zásad.

::: zone-end

## <a name="next-steps"></a>Další kroky

- Naučte se [spravovat přístup uživatelů v Azure AD B2C](manage-user-access.md).

