---
title: Rizikové zásady – Ochrana identit y služby Azure Active Directory
description: Povolení a konfigurace zásad rizik v azure active directory identity protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707001"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Postup: Konfigurace a povolení zásad rizik

Jak jsme se dozvěděli v předchozím článku, [zásady ochrany identity](concept-identity-protection-policies.md) máme dvě zásady rizika, které můžeme povolit v našem adresáři. 

- Zásady rizik pro přihlášení
- Zásady rizika pro uživatele

![Stránka přehledu zabezpečení umožňující zásady rizik pro uživatele a přihlášení](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Obě zásady pracují na automatizaci reakce na detekci rizik ve vašem prostředí a umožňují uživatelům samonapravit, když je zjištěno riziko. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Požadavky 

Pokud vaše organizace chce umožnit uživatelům, aby se při zjištění rizik sami opravou, musí být uživatelé registrováni pro samoobslužné resetování hesla i azure multifaktorové ověřování. Doporučujeme [povolit kombinované prostředí registrace informací o zabezpečení](../authentication/howto-registration-mfa-sspr-combined.md) pro nejlepší zážitek. Povolení uživatelům k vlastní nápravě je vrátí zpět do produktivního stavu rychleji, aniž by bylo nutné zásah správce. Správci mohou stále zobrazit tyto události a prozkoumat je po faktu. 

## <a name="choosing-acceptable-risk-levels"></a>Výběr přijatelných úrovní rizika

Organizace musí rozhodnout o úrovni rizika, které jsou ochotny přijmout vyvážení uživatelskézkušenosti a stav zabezpečení. 

Doporučení společnosti Microsoft je nastavit prahovou hodnotu zásad rizika uživatele na **hodnotu Vysoká** a zásady rizika přihlášení na **střední a vyšší**.

Výběr **vysoké** prahové hodnoty snižuje počet spuštění zásada a minimalizuje dopad na uživatele. Však vylučuje **nízké** a **střední** riziko detekce ze zásady, které nemusí blokovat útočníkz zneužití ohrožené identity. Výběr **nízké** prahové hodnoty zavádí další přerušení uživatele, ale zvýšené zabezpečení držení těla.

## <a name="exclusions"></a>Vyloučení

Všechny zásady umožňují vyloučit uživatele, jako je [například nouzový přístup nebo účty správce sklu](../users-groups-roles/directory-emergency-access.md). Organizace mohou určit, že je třeba vyloučit jiné účty z konkrétních zásad na základě způsobu, jakým jsou účty používány. Všechna vyloučení by měla být pravidelně přezkoumávána, aby se zjistilo, zda jsou stále použitelná.

Nakonfigurovaná důvěryhodná [síťová umístění](../conditional-access/location-condition.md) jsou používána ochranou identity v některých zjišťování rizik ke snížení počtu falešných poplachů.

## <a name="enable-policies"></a>Povolit zásady

Chcete-li povolit riziko uživatele a zásady rizik přihlášení, proveďte následující kroky.

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte na**přehled**ochrany > identity**zabezpečení** > **služby** **Azure Active Directory** > .
1. Vyberte **konfigurovat zásady rizika uživatele**.
   1. V části **Přiřazení**
      1. **Uživatelé** – pokud omezíte zavedení, zvolte **Všichni uživatelé** **nebo Vybrat jednotlivce a skupiny.**
         1. Volitelně můžete vyloučit uživatele ze zásady.
      1. **Podmínky** - **Uživatelské riziko** Microsoft doporučení je nastavit tuto možnost na **vysoké**.
   1. V části **Ovládací prvky**
      1. **Access** – Doporučení společnosti Microsoft je **povolit přístup** a **vyžadovat změnu hesla**.
   1. **Vynutit zásady** - **na**
   1. **Uložit** – Tato akce vás vrátí na stránku **Přehled.**
1. Vyberte **Konfigurovat zásady rizika přihlášení**.
   1. V části **Přiřazení**
      1. **Uživatelé** – pokud omezíte zavedení, zvolte **Všichni uživatelé** **nebo Vybrat jednotlivce a skupiny.**
         1. Volitelně můžete vyloučit uživatele ze zásady.
      1. **Podmínky** - **Riziko přihlášení Microsoft** doporučuje nastavit tuto možnost na střední a **vyšší**.
   1. V části **Ovládací prvky**
      1. **Access** – Doporučení společnosti Microsoft je **povolit přístup** a **vyžadovat vícefaktorové ověřování**.
   1. **Vynutit zásady** - **na**
   1. **Uložit**

## <a name="next-steps"></a>Další kroky

- [Povolení zásad registrace azure vícefaktorového ověřování](howto-identity-protection-configure-mfa-policy.md)

- [Co je riziko](concept-identity-protection-risks.md)

- [Šetření detekce rizik](howto-identity-protection-investigate-risk.md)

- [Simulace detekce rizik](howto-identity-protection-simulate-risk.md)
