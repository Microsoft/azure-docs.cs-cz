---
title: Rychlý Start – blokování přístupu při zjištění rizika relace s Azure Active Directory podmíněný přístup | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak můžete nakonfigurovat zásady podmíněného přístupu Azure Active Directory (Azure AD) pro blokování přihlášení na základě rizik relace.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 619f1ea3bae001d25eb520f43da33ca94a3160c8
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880342"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Rychlý Start: blokování přístupu při zjištění rizika relace s Azure Active Directory podmíněný přístup  

Aby se zajistilo, že se vaše prostředí chrání, možná budete chtít zablokovat podezřelé uživatele od přihlášení. [Služba Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analyzuje každé přihlášení a vypočítává pravděpodobnost, že oprávněný vlastník uživatelského účtu neudělal pokus o přihlášení. Pravděpodobnost (nízká, střední, vysoká) je uvedena ve formě počítané hodnoty s názvem [úrovně rizika přihlášení](conditions.md#sign-in-risk). Nastavením rizikové podmínky přihlášení můžete nakonfigurovat zásady podmíněného přístupu, které budou reagovat na konkrétní úrovně rizika přihlašování.

V tomto rychlém startu se dozvíte, jak nakonfigurovat [zásady podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) , které blokují přihlášení, když se zjistí nakonfigurovaná úroveň rizika přihlašování.

![Vytvoření zásad](./media/app-sign-in-risk/1000.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení scénáře v tomto kurzu budete potřebovat:

- **Přístup k edici Azure AD Premium P2** – zatímco podmíněný přístup je schopnost Azure AD Premium P1, potřebujete edici P2, protože scénář v tomto rychlém startu vyžaduje ochranu identity.
- **Identity Protection** – scénář v tomto rychlém startu vyžaduje, aby byla povolená ochrana identity. Pokud si nejste jisti, jak povolit ochranu identity, přečtěte si téma [povolení Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).
- **Webový prohlížeč** – [prohlížeč pro mandát](https://www.torproject.org/projects/torbrowser.html.en) vám umožňuje zachovat ochranu osobních údajů online. Identity Protection detekuje přihlášení z prohlížeče mandátu jako přihlášení z anonymních IP adres, které mají střední úroveň rizika. Další informace najdete v tématu [Azure Active Directory detekci rizik](../reports-monitoring/concept-risk-events.md).  
- **Testovací účet s názvem Alain Charon** – Pokud nevíte, jak vytvořit testovací účet, podívejte se na téma [Přidání cloudových uživatelů](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Otestování přihlášení

Cílem tohoto kroku je zajistit, že váš testovací účet bude mít přístup k vašemu tenantovi pomocí prohlížeče systému.

**Testování přihlášení:**

1. Přihlaste se k vašemu [Azure Portal](https://portal.azure.com) jako **Alain Charon**.
1. Odhlaste se.

## <a name="create-your-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Scénář v tomto rychlém startu používá k vygenerování zjištěných **přihlášení ze zjišťování rizik anonymních IP adres** přihlášení z prohlížeče pro mandát. Úroveň rizika tohoto zjišťování rizik je střední. Pokud chcete reagovat na toto zjištění rizika, nastavte rizikové podmínky přihlášení na hodnotu Střední. V produkčním prostředí byste měli nastavit rizikové podmínky přihlášení buď na vysoká, tak na střední a vysokou.

V této části se dozvíte, jak vytvořit požadované zásady podmíněného přístupu. V zásadách nastavte:

| Nastavení | Hodnota |
| --- | --- |
| Uživatelé a skupiny | Alain Charon  |
| Cloudové aplikace | Všechny cloudové aplikace |
| Riziko přihlášení | Střední |
| Udělit | Blokovat přístup |

![Vytvoření zásad](./media/app-sign-in-risk/130.png)

**Konfigurace zásad podmíněného přístupu:**

1. Přihlaste se ke svému [Azure Portal](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. V Azure Portal klikněte v levém navigačním panelu na možnost **Azure Active Directory**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Na stránce **Azure Active Directory** v části **zabezpečení** klikněte na **podmíněný přístup**.

   ![Podmíněný přístup](./media/app-sign-in-risk/03.png)

1. Na stránce **podmíněný přístup** klikněte na panelu nástrojů v horní části na **Přidat**.

   ![Name (Název)](./media/app-sign-in-risk/108.png)

1. Na **nové** stránce zadejte do textového pole **název** **blokový přístup pro úroveň střední rizikovost**.

   ![Name (Název)](./media/app-sign-in-risk/104.png)

1. V části **přiřazení** klikněte na **Uživatelé a skupiny**.

   ![Uživatelé a skupiny](./media/app-sign-in-risk/06.png)

1. Na stránce **Uživatelé a skupiny** :

   ![Podmíněný přístup](./media/app-sign-in-risk/107.png)

   1. Klikněte na **Vybrat uživatele a skupiny**a pak vyberte **Uživatelé a skupiny**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **Vybrat** vyberte **Alain Charon**a pak klikněte na **Vybrat**.
   1. Na stránce **Uživatelé a skupiny** klikněte na **Hotovo**.
1. Klikněte na **cloudové aplikace**.

   ![Cloudové aplikace](./media/app-sign-in-risk/08.png)

1. Na stránce **cloudové aplikace** :

   ![Podmíněný přístup](./media/app-sign-in-risk/109.png)

   1. Klikněte na **všechny cloudové aplikace**.
   1. Klikněte na **Done** (Hotovo).
1. Klikněte na možnost **podmínky**.

   ![Řízení přístupu](./media/app-sign-in-risk/19.png)

1. Na stránce **podmínky** :

   ![Úroveň rizika přihlašování](./media/app-sign-in-risk/21.png)

   1. Klikněte na možnost **riziko přihlášení**.
   1. Jako **Konfigurace**klikněte na **Ano**.
   1. Jako úroveň rizika přihlašování vyberte **střední**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **podmínky** klikněte na **Hotovo**.
1. V části **řízení přístupu** klikněte na **udělit**.

   ![Řízení přístupu](./media/app-sign-in-risk/10.png)

1. Na stránce **udělení** :

   ![Podmíněný přístup](./media/app-sign-in-risk/105.png)

   1. Vyberte **blokovat přístup**.
   1. Klikněte na **Vybrat**.
1. V části **Povolit zásady** klikněte **na zapnuto**.

   ![Povolit zásadu](./media/app-sign-in-risk/18.png)

1. Klikněte na **Vytvořit**.

## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnotit simulované přihlašování

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít zjistit, jestli funguje podle očekávání. Jako první krok použijte **Nástroj zásady** podmíněného přístupu, který je v případě, že chcete simulovat přihlášení testovacího uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace.  

Pokud pro tento scénář spustíte **Nástroj citlivostní** zpracování, měla by být **úroveň přístupu blok pro střední riziko** uvedená v části **zásady, které budou platit**.

![Uživatel](./media/app-sign-in-risk/117.png)

**Vyhodnocení zásad podmíněného přístupu:**

1. Na stránce [zásady podmíněného přístupu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce v horní části na **What If**.  

   ![Co když](./media/app-sign-in-risk/14.png)

1. Klikněte na **uživatel**, na stránce **Uživatelé** vyberte **Jakub Charon** a pak klikněte na **Vybrat**.

   ![Uživatel](./media/app-sign-in-risk/116.png)

1. Jako **riziko přihlášení**vyberte **střední**.

   ![Uživatel](./media/app-sign-in-risk/119.png)

1. Klikněte na **What If**.

## <a name="test-your-conditional-access-policy"></a>Testování zásad podmíněného přístupu

V předchozí části jste se dozvěděli, jak vyhodnotit simulované přihlašování. Kromě simulace byste měli také otestovat zásady podmíněného přístupu, abyste se ujistili, že funguje podle očekávání.

Pokud chcete zásady otestovat, zkuste se k vašemu [Azure Portal](https://portal.azure.com) přihlašovat jako **Jakub Charon** pomocí prohlížeče pro prostředí. Váš pokus o přihlášení by měl blokovat vaše zásada podmíněného přístupu.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte testovacího uživatele, prohlížeč pro mandát a zásadu podmíněného přístupu:

- Pokud nevíte, jak odstranit uživatele Azure AD, přečtěte si téma [odstranění uživatelů ze služby Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Pokud chcete zásadu odstranit, vyberte zásadu a pak na panelu nástrojů Rychlý přístup klikněte na **Odstranit** .

   ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Pokyny k odebrání prohlížeče pro mandát najdete v tématu věnovaném [odinstalaci](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyžadovat, aby byly podmínkami použití přijaty](require-tou.md)
> [vyžadovat MFA pro konkrétní aplikace](app-based-mfa.md)
