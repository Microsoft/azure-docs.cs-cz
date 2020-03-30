---
title: Použití detekce rizik s podmíněným přístupem služby Azure Active Directory
description: V tomto rychlém startu se dozvíte, jak můžete nakonfigurovat zásady podmíněného přístupu služby Azure Active Directory (Azure AD) tak, aby blokovala přihlášení na základě rizik relace.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: be447b001b0b2e14999aac98ba2125f8cbfe9853
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77186611"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Úvodní příručka: Blokování přístupu při zjištění rizika relace pomocí podmíněného přístupu služby Azure Active Directory  

Chcete-li chránit prostředí, můžete zablokovat podezřelé uživatele z přihlášení. [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analyzuje každé přihlášení a vypočítá pravděpodobnost, že pokus o přihlášení nebyl proveden legitimním vlastníkem uživatelského účtu. Pravděpodobnost (nízká, střední, vysoká) je uvedena ve formě vypočtené hodnoty nazývané [úrovně rizika přihlášení](concept-conditional-access-conditions.md#sign-in-risk). Nastavením podmínky rizika přihlášení můžete nakonfigurovat zásady podmíněného přístupu tak, aby reagovaly na konkrétní úrovně rizika přihlášení.

Tento rychlý start ukazuje, jak nakonfigurovat [zásadu podmíněného přístupu,](../active-directory-conditional-access-azure-portal.md) která blokuje přihlášení, když byla zjištěna nakonfigurovaná úroveň rizika přihlášení.

![Vytvoření zásad](./media/app-sign-in-risk/1000.png)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení scénáře v tomto kurzu budete potřebovat:

- **Přístup k edici Azure AD Premium P2** – zatímco podmíněný přístup je funkce Azure AD Premium P1, potřebujete edici P2, protože scénář v tomto rychlém startu vyžaduje ochranu identity.
- **Ochrana identity** – scénář v tomto rychlém startu vyžaduje identity ochrany, které mají být povoleny. Pokud nevíte, jak povolit ochranu identity, přečtěte si informace [o povolení ochrany identity služby Azure Active Directory](../identity-protection/overview-identity-protection.md).
- **Tor Browser** - [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) je navržen tak, aby vám pomohl zachovat vaše soukromí online. Ochrana identity detekuje přihlášení z prohlížeče Tor jako přihlášení z anonymních IP adres, které mají střední úroveň rizika. Další informace naleznete v [tématu Azure Active Directory risk detections](../reports-monitoring/concept-risk-events.md).  
- **Testovací účet s názvem Alain Charon** – pokud nevíte, jak vytvořit testovací účet, přečtěte si informace [o přidání cloudových uživatelů](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Otestujte své přihlášení

Cílem tohoto kroku je zajistit, aby váš testovací účet měl přístup k vašemu tenantovi pomocí prohlížeče Tor.

**Chcete-li přihlásit:**

1. Přihlaste se na [svůj portál Azure](https://portal.azure.com) jako **Alain Charon**.
1. Odhlaste se.

## <a name="create-your-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

Scénář v tomto rychlém startu používá přihlášení z prohlížeče Tor ke generování **zjištěných přihlášení z anonymních ip adres** detekce rizik. Úroveň rizika této detekce rizika je střední. Chcete-li reagovat na tuto detekci rizika, nastavte podmínku rizika přihlášení na střední. V provozním prostředí byste měli nastavit podmínku rizika přihlášení na vysokou nebo střední a vysokou.

Tato část ukazuje, jak vytvořit požadované zásady podmíněného přístupu. V zásadách nastavte:

| Nastavení | Hodnota |
| --- | --- |
| Uživatelé a skupiny | Alain Charon  |
| Cloudové aplikace | Všechny cloudové aplikace |
| Riziko přihlášení | Střednědobé používání |
| Oprávnění | Blokovat přístup |

![Vytvoření zásad](./media/app-sign-in-risk/130.png)

**Postup konfigurace zásad podmíněného přístupu:**

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Na webu Azure Portal klikněte na levém panelu na **Položku Azure Active Directory**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Na stránce **Azure Active Directory** klikněte v části **Zabezpečení** na **podmíněný přístup**.

   ![Podmíněný přístup](./media/app-sign-in-risk/03.png)

1. Na stránce **Podmíněný přístup** klikněte na panelu nástrojů nahoře na **Přidat**.

   ![Name (Název)](./media/app-sign-in-risk/108.png)

1. Na stránce **Nový** zadejte do textového pole **Název** **pro úroveň středního rizika možnost Blokovat přístup**.

   ![Name (Název)](./media/app-sign-in-risk/104.png)

1. V části **Přiřazení** klikněte na **Položku Uživatelé a skupiny**.

   ![Uživatelé a skupiny](./media/app-sign-in-risk/06.png)

1. Na stránce **Uživatelé a skupiny:**

   ![Podmíněný přístup](./media/app-sign-in-risk/107.png)

   1. Klikněte na **Vybrat uživatele a skupiny**a potom vyberte **Uživatelé a skupiny**.
   1. Klepněte na **tlačítko Vybrat**.
   1. Na stránce **Select** vyberte **Alain Acharon**a klepněte na tlačítko **Vybrat**.
   1. Na stránce **Uživatelé a skupiny** klikněte na **Hotovo**.
1. Klikněte na **Cloudové aplikace**.

   ![Cloudové aplikace](./media/app-sign-in-risk/08.png)

1. Na stránce **Cloudové aplikace:**

   ![Podmíněný přístup](./media/app-sign-in-risk/109.png)

   1. Klikněte na **Všechny cloudové aplikace**.
   1. Klikněte na **Done** (Hotovo).
1. Klepněte na **tlačítko Podmínky**.

   ![Řízení přístupu](./media/app-sign-in-risk/19.png)

1. Na stránce **Podmínky:**

   ![Úroveň rizika přihlášení](./media/app-sign-in-risk/21.png)

   1. Klikněte na **Riziko přihlášení**.
   1. Při **konfiguraci**klepněte na tlačítko **Ano**.
   1. Jako úroveň rizika přihlášení vyberte **střední**.
   1. Klepněte na **tlačítko Vybrat**.
   1. Na stránce **Podmínky** klikněte na **Hotovo**.
1. V části **Ovládací prvky aplikace Access** klepněte na tlačítko **Udělit**.

   ![Řízení přístupu](./media/app-sign-in-risk/10.png)

1. Na stránce **Grant:**

   ![Podmíněný přístup](./media/app-sign-in-risk/105.png)

   1. Vyberte **možnost Blokovat přístup**.
   1. Klepněte na **tlačítko Vybrat**.
1. V části **Povolit zásady** klikněte **na Zapnuto**.

   ![Povolení zásady](./media/app-sign-in-risk/18.png)

1. Klikněte na **Vytvořit**.

## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení simulovanéregistrace

Nyní, když jste nakonfigurovali zásady podmíněného přístupu, pravděpodobně budete chtít vědět, zda funguje podle očekávání. Jako první krok použijte nástroj zásad podmíněného přístupu **co když** k simulaci přihlášení testovacího uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace.  

Při spuštění **nástroje zásad co if** pro tento scénář by měl být **blokový přístup pro úroveň středního rizika** uveden v části **Zásady, které budou platit**.

![Uživatel](./media/app-sign-in-risk/117.png)

**Vyhodnocení zásad podmíněného přístupu:**

1. Na stránce [Podmíněný přístup – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce nahoře na **Co kdyby**.  

   ![Citlivostní analýza](./media/app-sign-in-risk/14.png)

1. Klepněte na **tlačítko Uživatel**, na stránce **Uživatelé** vyberte **položku Alan Charon** a klepněte na tlačítko **Vybrat**.

   ![Uživatel](./media/app-sign-in-risk/116.png)

1. Jako **riziko přihlášení**vyberte možnost **Střední**.

   ![Uživatel](./media/app-sign-in-risk/119.png)

1. Klepněte na **položku Co-li**.

## <a name="test-your-conditional-access-policy"></a>Otestujte zásady podmíněného přístupu

V předchozí části jste se naučili, jak vyhodnotit simulované přihlášení. Kromě simulace byste měli také otestovat zásady podmíněného přístupu, abyste se ujistili, že funguje podle očekávání.

Chcete-li otestovat zásady, zkuste se přihlásit k [portálu Azure](https://portal.azure.com) jako **Alan Charon** pomocí prohlížeče Tor. Váš pokus o přihlášení by měl být blokován zásadami podmíněného přístupu.

![Ověřování pomocí služby Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte testovacího uživatele, prohlížeč Tor a zásady podmíněného přístupu:

- Pokud nevíte, jak odstranit uživatele Azure AD, přečtěte si informace [o odstranění uživatelů z Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Pokud chcete zásadu odstranit, vyberte zásadu a na panelu nástrojů rychlý přístup klikněte na **Odstranit.**

   ![Ověřování pomocí služby Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Pokyny k odebrání prohlížeče Tor naleznete v tématu [Odinstalace](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyžadovat, aby byly podmínky použití přijaty](require-tou.md)
> [vyžadovat vícefaktorové informace pro konkrétní aplikace](app-based-mfa.md)
