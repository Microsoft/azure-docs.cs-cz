---
title: Rychlý start – vyžadovat vícefaktorové ověřování (MFA) pro konkrétní aplikace s Azure Active Directory podmíněným přístupem | Dokumentace Microsoftu
description: V tomto rychlém startu zjistíte, jak můžete navázat vaše požadavky na ověřování typu používaná cloudové aplikace pomocí podmíněného přístupu Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36cb3b1555a339249528e290e376454dd78f1e53
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509059"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Rychlý start: Vyžadovat vícefaktorové ověřování pro konkrétní aplikace pomocí Azure Active Directory podmíněného přístupu

Pro zjednodušení přihlašování uživatelů, můžete chtít umožnit přihlásit k vašim cloudovým aplikacím pomocí uživatelského jména a hesla. Mnoho prostředí však mít aspoň pár aplikací, pro které je vhodné tak, aby vyžadovala silnější formu ověření účtu, jako je například vícefaktorové ověřování (MFA). Tato zásada může platit pro přístup k vaší organizaci e-mailovému systému nebo aplikace HR. Ve službě Azure Active Directory (Azure AD) můžete dosažení tohoto cíle se zásadami podmíněného přístupu.

Tento rychlý start ukazuje, jak nakonfigurovat [zásady podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md) , který vyžaduje vícefaktorové ověřování pro vybranou cloudovou aplikaci ve vašem prostředí.

![Příklad zásady podmíněného přístupu na webu Azure Portal](./media/app-based-mfa/32.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto scénáře v tomto rychlém startu budete potřebovat:

- **Přístup do Azure AD Premium edition** – podmíněný přístup Azure AD je funkce Azure AD Premium.
- **Testovací účet s názvem Isabella Simonsen** – Pokud si nejste jisti, jak vytvořit testovací účet, najdete v článku [přidat cloudové uživatele](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Scénář v tomto rychlém startu vyžaduje, že na uživatele není zapnuté MFA. pro svůj zkušební účet. Další informace najdete v tématu [vyžadování dvoustupňového ověřování pro uživatele](../authentication/howto-mfa-userstates.md).

## <a name="test-your-experience"></a>Testovací prostředí

Cílem tohoto kroku je získat dojem prostředí bez zásady podmíněného přístupu.

**Inicializace prostředí:**

1. Přihlaste se k webu Azure portal jako Isabella Simonsen.
1. Odhlaste se.

## <a name="create-your-conditional-access-policy"></a>Vytvořte zásadu podmíněného přístupu

Tato část ukazuje, jak vytvořit požadované zásady podmíněného přístupu. Scénář v tomto rychlém startu používá:

- Azure portal jako zástupný symbol pro cloudové aplikace, která vyžaduje vícefaktorové ověřování. 
- Ukázkového uživatele k otestování zásady podmíněného přístupu.  

V zásadách nastavte:

| Nastavení | Hodnota |
| --- | --- |
| Uživatelé a skupiny | Isabella Simonsen |
| Cloudové aplikace | Správa Microsoft Azure |
| Udělení přístupu | Vyžadovat vícefaktorové ověřování |

![Rozšířené zásady podmíněného přístupu](./media/app-based-mfa/31.png)

**Ke konfiguraci zásady podmíněného přístupu:**

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Na webu Azure Portal, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory**.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. Na **Azure Active Directory** stránku, **zabezpečení** klikněte na tlačítko **podmíněného přístupu**.

   ![Podmíněný přístup](./media/app-based-mfa/03.png)

1. Na **podmíněného přístupu** klikněte na stránku, na panelu nástrojů v horní části **nové zásady**.

   ![Přidat](./media/app-based-mfa/04.png)

1. Na **nový** stránku, **název** textové pole, typ **vyžadovat vícefaktorové ověřování pro přístup na portál Azure**.

   ![Název](./media/app-based-mfa/05.png)

1. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

   ![Uživatelé a skupiny](./media/app-based-mfa/06.png)

1. Na **uživatelů a skupin** stránce, proveďte následující kroky:

   ![Uživatelé a skupiny](./media/app-based-mfa/24.png)

   1. Klikněte na tlačítko **výběr uživatelů a skupin**a pak vyberte **uživatelů a skupin**.
   1. Klikněte na **Vybrat**.
   1. Na **vyberte** stránce **Isabella Simonsen**a potom klikněte na tlačítko **vyberte**.
   1. Na **uživatelů a skupin** klikněte na **provádí**.

1. Klikněte na tlačítko **cloudové aplikace**.

   ![Cloudové aplikace](./media/app-based-mfa/08.png)

1. Na **cloudové aplikace** stránce, proveďte následující kroky:

   ![Vyberte cloudové aplikace](./media/app-based-mfa/26.png)

   1. Klikněte na tlačítko **vyberte aplikace**.
   1. Klikněte na **Vybrat**.
   1. Na **vyberte** stránce **Microsoft Azure Management**a potom klikněte na tlačítko **vyberte**.
   1. Na **cloudové aplikace** klikněte na **provádí**.

1. V **ovládací prvky přístupu** klikněte na tlačítko **udělení**.

   ![Řízení přístupu](./media/app-based-mfa/10.png)

1. Na **udělení** stránce, proveďte následující kroky:

   ![Udělení](./media/app-based-mfa/11.png)

   1. Vyberte **udělit přístup**.
   1. Vyberte **vyžadovat vícefaktorové ověřování**.
   1. Klikněte na **Vybrat**.

1. V **povolit zásady** klikněte na tlačítko **na**.

   ![Povolení zásady](./media/app-based-mfa/18.png)

1. Klikněte na možnost **Vytvořit**.

## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení Simulovaná přihlášení

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít vědět, jestli funguje podle očekávání. Jako první krok pomocí podmíněného přístupu, nástroj pro co když zásady pro simulaci přihlášení testovacího uživatele. Simulace odhad dopadu pro toto přihlášení na zásady a generuje sestavy simulace.  

Inicializovat **co když** nastavit nástroj pro vyhodnocení zásad:

- **Isabella Simonsen** jako uživatel
- **Microsoft Azure Management** jako cloudové aplikace

Kliknutím na **co když** vytvoří sestavu simulace, která zobrazí:

- **Vyžadovat vícefaktorové ověřování pro přístup na portál Azure** pod **zásady, které budou platit**
- **Vyžadovat vícefaktorové ověřování** jako **udělení ovládacích prvků**.

![Co když nástroj zásad](./media/app-based-mfa/23.png)

**Vyhodnotit své zásady podmíněného přístupu:**

1. Na [podmíněného přístupu – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce v horní části na stránce **co když**.  

   ![Co když](./media/app-based-mfa/14.png)

1. Klikněte na tlačítko **uživatelé**vyberte **Isabella Simonsen**a potom klikněte na tlačítko **vyberte**.

   ![Uživatel](./media/app-based-mfa/15.png)

1. Vyberte cloudové aplikace, proveďte následující kroky:

   ![Cloudové aplikace](./media/app-based-mfa/16.png)

   1. Klikněte na tlačítko **cloudové aplikace**.
   1. Na **stránky aplikací cloudu**, klikněte na tlačítko **vyberte aplikace**.
   1. Klikněte na **Vybrat**.
   1. Na **vyberte** stránce **Microsoft Azure Management**a potom klikněte na tlačítko **vyberte**.
   1. Na stránce cloudové aplikace, klikněte na tlačítko **provádí**.

1. Klikněte na tlačítko **co když**.

## <a name="test-your-conditional-access-policy"></a>Otestovat své zásady podmíněného přístupu

V předchozí části jste zjistili, jak vyhodnotit Simulovaná přihlášení. Kromě simulaci byste měli také otestovat své zásady podmíněného přístupu k zajištění, že funguje podle očekávání.

K otestování vašich zásad, zkuste se přihlásit k vaší [webu Azure portal](https://portal.azure.com) pomocí vaší **Isabella Simonsen** testovací účet. Zobrazí se dialogové okno, které vyžadují nastavení účtu pro dodatečné ověření zabezpečení.

![Ověřování pomocí služby Multi-Factor Authentication](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte testovacího uživatele a zásady podmíněného přístupu:

- Pokud si nejste jisti, jak odstranit uživatele služby Azure AD, přečtěte si téma [odstranit uživatele z Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Pokud chcete odstranit zásady, vyberte zásady a klikněte na **odstranit** v panelu nástrojů Rychlý přístup.

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyžadovat podmínky použití přijmout](require-tou.md)
> [blokování přístupu, když se zjistí ohrožení relace](app-sign-in-risk.md)
