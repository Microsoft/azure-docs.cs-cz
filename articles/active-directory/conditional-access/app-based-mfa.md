---
title: Vyžadovat Azure MFA s podmíněným přístupem – Azure Active Directory
description: V tomto rychlém startu se dozvíte, jak můžete své požadavky na ověřování spojit s typem cloudové aplikace, ke které se přistupuje pomocí podmíněného přístupu Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 55c9188a1320b92aafa5fc67a253b42b6b107711
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381083"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Rychlý Start: vyžadování MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup

Chcete-li zjednodušit přihlašování uživatelů, můžete jim dovolit, aby se k vašim cloudovým aplikacím přihlásili pomocí uživatelského jména a hesla. Mnoho prostředí ale má alespoň několik aplikací, pro které se doporučuje vyžadovat silnější formu ověření účtu, jako je vícefaktorové ověřování (MFA). Tato zásada může platit pro přístup k e-mailovým systémům vaší organizace nebo k vašim aplikacím na HR. V Azure Active Directory (Azure AD) můžete tento cíl dosáhnout pomocí zásad podmíněného přístupu.

V tomto rychlém startu se dozvíte, jak nakonfigurovat [zásady podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md) , které pro vybranou cloudovou aplikaci ve vašem prostředí vyžadují službu Multi-Factor Authentication.

![Příklad zásad podmíněného přístupu v Azure Portal](./media/app-based-mfa/32.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení scénáře v tomto rychlém startu budete potřebovat:

- **Přístup k edici Azure AD Premium** – podmíněný přístup Azure AD je Azure AD Premium funkce.
- **Testovací účet s názvem Isabella Simonsen** – Pokud nevíte, jak vytvořit testovací účet, podívejte se na téma [Přidání cloudových uživatelů](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Scénář v tomto rychlém startu vyžaduje, aby pro váš zkušební účet nebyla povolena MFA pro jednotlivé uživatele. Další informace najdete v tématu [jak pro uživatele vyžadovat dvoustupňové ověřování](../authentication/howto-mfa-userstates.md).

## <a name="test-your-experience"></a>Vyzkoušejte si prostředí

Cílem tohoto kroku je získat dojem o prostředí bez zásad podmíněného přístupu.

**Postup při inicializaci prostředí:**

1. Přihlaste se k vašemu Azure Portal jako Isabella Simonsen.
1. Odhlaste se.

## <a name="create-your-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

V této části se dozvíte, jak vytvořit požadované zásady podmíněného přístupu. Scénář v tomto rychlém startu používá:

- Azure Portal jako zástupný symbol pro cloudovou aplikaci, která vyžaduje MFA. 
- Ukázkový uživatel, který otestuje zásady podmíněného přístupu.  

V zásadách nastavte:

| Nastavení | Hodnota |
| --- | --- |
| Uživatelé a skupiny | Isabella Simonsen |
| Cloudové aplikace | Správa Microsoft Azure |
| Udělení přístupu | Vyžadovat Multi-Factor Authentication |

![Rozšířené zásady podmíněného přístupu](./media/app-based-mfa/31.png)

**Konfigurace zásad podmíněného přístupu:**

1. Přihlaste se ke svému [Azure Portal](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. Na stránce **Azure Active Directory** v části **zabezpečení** klikněte na **podmíněný přístup**.

   ![Podmíněný přístup](./media/app-based-mfa/03.png)

1. Na stránce **podmíněný přístup** klikněte na panelu nástrojů v horní části na **nové zásady**.

   ![Přidejte](./media/app-based-mfa/04.png)

1. Na **nové** stránce zadejte do textového pole **název** **požadavek MFA pro přístup Azure Portal**.

   ![Název](./media/app-based-mfa/05.png)

1. V části **přiřazení** klikněte na **Uživatelé a skupiny**.

   ![Uživatelé a skupiny](./media/app-based-mfa/06.png)

1. Na stránce **Uživatelé a skupiny** proveďte následující kroky:

   ![Uživatelé a skupiny](./media/app-based-mfa/24.png)

   1. Klikněte na **Vybrat uživatele a skupiny**a pak vyberte **Uživatelé a skupiny**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **Vybrat** vyberte **Isabella Simonsen**a pak klikněte na **Vybrat**.
   1. Na stránce **Uživatelé a skupiny** klikněte na **Hotovo**.

1. Klikněte na **cloudové aplikace**.

   ![Cloudové aplikace](./media/app-based-mfa/08.png)

1. Na stránce **cloudové aplikace** proveďte následující kroky:

   ![Vybrat cloudové aplikace](./media/app-based-mfa/26.png)

   1. Klikněte na **vybrat aplikace**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **Vybrat** vyberte **Microsoft Azure Management**a pak klikněte na **Vybrat**.
   1. Na stránce **cloudové aplikace** klikněte na **Hotovo**.

1. V části **řízení přístupu** klikněte na **udělit**.

   ![Řízení přístupu](./media/app-based-mfa/10.png)

1. Na stránce **udělení** proveďte následující kroky:

   ![Udělit](./media/app-based-mfa/11.png)

   1. Vyberte **udělit přístup**.
   1. Vyberte **vyžadovat službu Multi-Factor Authentication**.
   1. Klikněte na **Vybrat**.

1. V části **Povolit zásady** klikněte **na zapnuto**.

   ![Povolení zásady](./media/app-based-mfa/18.png)

1. Klikněte na **Vytvořit**.

## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnotit simulované přihlašování

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít zjistit, jestli funguje podle očekávání. Jako první krok použijte nástroj Zásady podmíněného přístupu, který je v případě, že chcete simulovat přihlašování testovacího uživatele. Simulace odhadne dopad tohoto přihlášení na vaše zásady a vygeneruje sestavu simulace.  

Pro inicializaci nástroje pro vyhodnocování zásad **What If** nastavte:

- **Isabella Simonsen** jako uživatel
- **Správa Microsoft Azure** jako cloudová aplikace

Kliknutím na **What If** se vytvoří sestava simulace, která zobrazuje:

- **Vyžadovat vícefaktorové ověřování pro přístup k Azure Portal** v rámci **zásad, které se použijí**
- **Vyžadovat službu Multi-Factor Authentication** jako **udělení ovládacích prvků**.

![Nástroj Policy if](./media/app-based-mfa/23.png)

**Vyhodnocení zásad podmíněného přístupu:**

1. Na stránce [zásady podmíněného přístupu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce v horní části na **What If**.  

   ![What If](./media/app-based-mfa/14.png)

1. Klikněte na **Uživatelé**, vyberte **Isabella Simonsen**a pak klikněte na **Vybrat**.

   ![Uživatel](./media/app-based-mfa/15.png)

1. Pokud chcete vybrat cloudovou aplikaci, proveďte následující kroky:

   ![Cloudové aplikace](./media/app-based-mfa/16.png)

   1. Klikněte na **cloudové aplikace**.
   1. Na **stránce cloudové aplikace**klikněte na **vybrat aplikace**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **Vybrat** vyberte **Microsoft Azure Management**a pak klikněte na **Vybrat**.
   1. Na stránce cloudové aplikace klikněte na **Hotovo**.

1. Klikněte na **What If**.

## <a name="test-your-conditional-access-policy"></a>Testování zásad podmíněného přístupu

V předchozí části jste se dozvěděli, jak vyhodnotit simulované přihlašování. Kromě simulace byste měli také otestovat zásady podmíněného přístupu, aby se zajistilo, že funguje podle očekávání.

Pokud chcete zásady otestovat, zkuste se přihlásit ke svému [Azure Portal](https://portal.azure.com) pomocí účtu testování **Simonsen Isabella** . Mělo by se zobrazit dialogové okno, které vyžaduje, abyste si nastavili účet pro další ověření zabezpečení.

![Vícefaktorové ověřování](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte testovacího uživatele a zásadu podmíněného přístupu:

- Pokud nevíte, jak odstranit uživatele Azure AD, přečtěte si téma [odstranění uživatelů ze služby Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Pokud chcete zásadu odstranit, vyberte zásadu a pak na panelu nástrojů Rychlý přístup klikněte na **Odstranit** .

    ![Vícefaktorové ověřování](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyžadovat, aby byly podmínkami použití přijaty](require-tou.md)
> [blokovat přístup, když se zjistí riziko relace](app-sign-in-risk.md)
