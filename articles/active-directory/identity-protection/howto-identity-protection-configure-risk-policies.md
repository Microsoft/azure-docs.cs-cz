---
title: Rizikové zásady – Azure Active Directory Identity Protection
description: Povolení a konfigurace zásad rizik v Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997626"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Postupy: konfigurace a povolení zásad rizik

Jak jsme se naučili v předchozím článku, [Zásady ochrany identit](concept-identity-protection-policies.md) máme dvě rizikové zásady, které můžeme v našem adresáři povolit. 

- Zásady rizik přihlašování
- Zásady rizik uživatelů

![Stránka s přehledem zabezpečení pro povolení zásad rizik uživatelů a přihlašování](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Obě zásady pracují na automatizaci reakcí na detekci rizik ve vašem prostředí a umožňují uživatelům, aby při zjištění rizika prováděli vlastní nápravu. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Požadavky 

Pokud chce vaše organizace uživatelům dovolit, aby při zjištění rizik prováděli nápravu, je nutné, aby se uživatelé zaregistrovali pro Samoobslužné resetování hesla i pro službu Azure AD Multi-Factor Authentication. Pro zajištění nejlepšího prostředí doporučujeme [, abyste povolili kombinované možnosti registrace informací o zabezpečení](../authentication/howto-registration-mfa-sspr-combined.md) . Umožnění, aby si uživatelé sami opravili stav do produktivního přípravení rychleji, aniž by museli mít zásah správce. Správci můžou tyto události pořád zobrazit a prozkoumat je po faktu. 

## <a name="choosing-acceptable-risk-levels"></a>Výběr přípustných úrovní rizika

Organizace musí rozhodnout, jakou úroveň rizika mají přijmout vyvážení uživatelského prostředí a stav zabezpečení. 

Doporučení Microsoftu je nastavit prahovou hodnotu zásad rizik uživatelů na **vysokou** a na **střední a vyšší** rizikové zásady pro přihlašování.

Výběr **vysoké** prahové hodnoty snižuje počet aktivovaných zásad a minimalizuje dopad na uživatele. Z těchto zásad ale nevylučuje detekci **nízkých** a **středních** rizik, což nemusí útočníkovi zabránit v zneužití ohrožené identity. Výběr **nízké** prahové hodnoty zavádí další přerušení uživatele, ale zvýšené zabezpečení stav.

## <a name="exclusions"></a>Vyloučení

Všechny zásady umožňují vyloučit uživatele, jako jsou například účty pro [nouzový přístup nebo správce přestávek](../roles/security-emergency-access.md). Organizace můžou určit, že budou muset vyloučit další účty z konkrétních zásad na základě způsobu použití účtů. Všechna vyloučení by měla být pravidelně přezkoumána, aby bylo možné zjistit, zda jsou stále k dispozici.

Nakonfigurovaná důvěryhodná [Síťová umístění](../conditional-access/location-condition.md) jsou používána ochranou identity v některých detekcích rizik k omezení falešně pozitivních hodnot.

## <a name="enable-policies"></a>Povolit zásady

Pokud chcete povolit rizika uživatele a zásady rizik přihlašování, proveďte následující kroky.

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte na **Azure Active Directory**  >  **Security**  >  **Přehled ochrany identit** zabezpečení  >  **Overview**.
1. Vyberte **zásady rizik uživatelů**.
   1. V části **přiřazení**
      1. **Uživatelé** – zvolte možnost **Všichni uživatelé** nebo **Vyberte jednotlivce a skupiny,** Pokud chcete omezit zavedení.
         1. Volitelně můžete vybrat možnost vyloučení uživatelů ze zásad.
      1. **Podmínky**  -  **Riziko uživatele** V doporučení Microsoftu je tato možnost nastavená na hodnotu **Vysoká**.
   1. Pod **ovládacími prvky**
      1. **Přístup** – doporučení Microsoftu je **Povolení přístupu** a **vyžadování změny hesla**.
   1. **Vyhovět zásadám**  -  **Zapnuto**
   1. **Uložit** – Tato akce vás vrátí na stránku **Přehled** .
1. Vyberte **zásady rizik přihlašování**.
   1. V části **přiřazení**
      1. **Uživatelé** – zvolte možnost **Všichni uživatelé** nebo **Vyberte jednotlivce a skupiny,** Pokud chcete omezit zavedení.
         1. Volitelně můžete vybrat možnost vyloučení uživatelů ze zásad.
      1. **Podmínky**  -  **Riziko přihlášení** Doporučení Microsoftu je nastavit tuto možnost na **střední a vyšší**.
   1. Pod **ovládacími prvky**
      1. **Přístup** – doporučení Microsoftu je **Povolení přístupu** a **vyžadování služby Multi-Factor Authentication**.
   1. **Vyhovět zásadám**  -  **Zapnuto**
   1. **Uložit**

## <a name="next-steps"></a>Další kroky

- [Povolit zásady registrace Multi-Factor Authentication služby Azure AD](howto-identity-protection-configure-mfa-policy.md)

- [Co je riziko](concept-identity-protection-risks.md)

- [Šetření detekce rizik](howto-identity-protection-investigate-risk.md)

- [Simulace detekce rizik](howto-identity-protection-simulate-risk.md)
