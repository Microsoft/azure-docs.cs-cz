---
title: Rychlý start – vyžadují podmínky použití přijmout před přístup ke cloudovým aplikacím, které jsou chráněné službou Azure Active Directory podmíněného přístupu | Dokumentace Microsoftu
description: V tomto rychlém startu zjistíte, jak můžete vyžadovat, že jsou vaše podmínky použití přijmout před udělením přístupu k vybrané cloudové aplikace pomocí Azure Active Directory podmíněného přístupu.
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
ms.openlocfilehash: 2a3523a050a021f3a98c144efe14d692704fba63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112219"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Rychlý start: Vyžadovat podmínky použití přijmout před přístupem k cloudových aplikací

Před použitím některých cloudových aplikací ve vašem prostředí, můžete chtít vyžadovat souhlas uživatele v podobě přijetí vaše podmínky použití (ToU). Podmíněný přístup služby Azure Active Directory (Azure AD) vám nabízí:

- Jednoduchý způsob konfigurace podmínek použití
- Možnost vyžadovat přijetí vaše podmínky použití prostřednictvím zásad podmíněného přístupu  

Tento rychlý start ukazuje, jak nakonfigurovat [zásady podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md) , která vyžaduje podmínky použití přijmout pro vybranou cloudovou aplikaci ve vašem prostředí.

![Vytvoření zásad](./media/require-tou/5555.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto scénáře v tomto rychlém startu budete potřebovat:

- **Přístup do Azure AD Premium edition** – podmíněný přístup Azure AD je funkce Azure AD Premium.
- **Testovací účet s názvem Isabella Simonsen** – Pokud si nejste jisti, jak vytvořit testovací účet, najdete v článku [přidat cloudové uživatele](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Test přihlášení

Cílem tohoto kroku je získat dojem přihlašovací prostředí bez zásady podmíněného přístupu.

**K otestování vaše přihlášení:**

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com/) jako Isabella Simonsen.
1. Odhlaste se.

## <a name="create-your-terms-of-use"></a>Vytvořit vaše podmínky použití

Tato část obsahuje kroky k vytvoření ukázkové podmínky použití. Když vytvoříte podmínky použití, vyberte hodnotu pro **vynutit pomocí šablon zásad podmíněného přístupu**. Výběr **vlastní zásady pro** otevře dialogové okno Vytvořit novou zásadu podmíněného přístupu poté, co byla vytvořena vaše podmínky použití.

**Chcete-li vytvořit vaše podmínky použití:**

1. V aplikaci Microsoft Word vytvoříte nový textový dokument.

1. Typ **Moje podmínky použití**a potom uložte dokument na počítač jako **mytou.pdf**.

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.

1. Na webu Azure Portal, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Na **Azure Active Directory** stránku, **zabezpečení** klikněte na tlačítko **podmíněného přístupu**.

   ![Podmíněný přístup](./media/require-tou/03.png)

1. V **spravovat** klikněte na tlačítko **podmínky použití**.

   ![Podmínky použití](./media/require-tou/04.png)

1. V nabídce v horní části klikněte na tlačítko **nové podmínky**.

   ![Podmínky použití](./media/require-tou/05.png)

1. Na **nové podmínky použití** stránky:

   ![Podmínky použití](./media/require-tou/112.png)

   1. V **název** textové pole, typ **Moje TOU**.

   1. V **zobrazovaný název** textové pole, typ **Moje TOU**.

   1. Nahrajte vaše podmínky použití souboru PDF.

   1. Jako **jazyk**vyberte **Angličtina**.

   1. Jako **vyžadují, aby uživatelé rozbalili podmínky použití**vyberte **na**.

   1. Jako **vynutit pomocí šablon zásad podmíněného přístupu**vyberte **vlastní zásady pro**.

   1. Klikněte na možnost **Vytvořit**.

## <a name="create-your-conditional-access-policy"></a>Vytvořte zásadu podmíněného přístupu

Tato část ukazuje, jak vytvořit požadované zásady podmíněného přístupu. Scénář v tomto rychlém startu používá:

- Azure portal jako zástupný symbol pro cloudové aplikace, která vyžaduje vaše podmínky použití přijmout. 
- Ukázkového uživatele k otestování zásady podmíněného přístupu.  

V zásadách nastavte:

| Nastavení | Hodnota |
| --- | --- |
| Uživatelé a skupiny | Isabella Simonsen |
| Cloudové aplikace | Správa Microsoft Azure |
| Udělení přístupu | Moje podmínek použití |

![Vytvoření zásad](./media/require-tou/1234.png)

**Ke konfiguraci zásady podmíněného přístupu:**

1. Na **nový** stránku, **název** textové pole, typ **vyžadují podmínky použití pro Isabella**.

   ![Name](./media/require-tou/71.png)

1. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

   ![Uživatelé a skupiny](./media/require-tou/06.png)

1. Na **uživatelů a skupin** stránky:

   ![Uživatelé a skupiny](./media/require-tou/24.png)

   1. Klikněte na tlačítko **výběr uživatelů a skupin**a pak vyberte **uživatelů a skupin**.

   1. Klikněte na **Vybrat**.

   1. Na **vyberte** stránce **Isabella Simonsen**a potom klikněte na tlačítko **vyberte**.

   1. Na **uživatelů a skupin** klikněte na **provádí**.

1. Klikněte na tlačítko **cloudové aplikace**.

   ![Cloudové aplikace](./media/require-tou/08.png)

1. Na **cloudové aplikace** stránky:

   ![Vyberte cloudové aplikace](./media/require-tou/26.png)

   1. Klikněte na tlačítko **vyberte aplikace**.

   1. Klikněte na **Vybrat**.

   1. Na **vyberte** stránce **Microsoft Azure Management**a potom klikněte na tlačítko **vyberte**.

   1. Na **cloudové aplikace** klikněte na **provádí**.

1. V **ovládací prvky přístupu** klikněte na tlačítko **udělení**.

   ![Řízení přístupu](./media/require-tou/10.png)

1. Na **udělení** stránky:

   ![Udělení](./media/require-tou/111.png)

   1. Vyberte **udělit přístup**.

   1. Vyberte **Moje podmínek použití**.

   1. Klikněte na **Vybrat**.

1. V **povolit zásady** klikněte na tlačítko **na**.

   ![Povolení zásady](./media/require-tou/18.png)

1. Klikněte na možnost **Vytvořit**.

## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení Simulovaná přihlášení

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít vědět, jestli funguje podle očekávání. Jako první krok pomocí podmíněného přístupu, nástroj pro co když zásady pro simulaci u přihlášení z testovacího uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace.  

K inicializaci co, pokud nástroj pro vyhodnocení zásad, nastavit:

- **Isabella Simonsen** jako uživatel
- **Microsoft Azure Management** jako cloudové aplikace

Kliknutím na **co když** vytvoří sestavu simulace, která zobrazí:

- **Vyžadovat podmínky použití pro Isabella** pod **zásady, které budou platit**
- **Moje podmínek použití** jako **udělení ovládacích prvků**.

![Co když nástroj zásad](./media/require-tou/79.png)

**Vyhodnotit své zásady podmíněného přístupu:**

1. Na [podmíněného přístupu – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce v horní části na stránce **co když**.  

   ![Co když](./media/require-tou/14.png)

1. Klikněte na tlačítko **uživatelé**vyberte **Isabella Simonsen**a potom klikněte na tlačítko **vyberte**.

   ![Uživatel](./media/require-tou/15.png)

1. Vyberte cloudové aplikace:

   ![Cloudové aplikace](./media/require-tou/16.png)

   1. Klikněte na tlačítko **cloudové aplikace**.

   1. Na **stránky aplikací cloudu**, klikněte na tlačítko **vyberte aplikace**.

   1. Klikněte na **Vybrat**.

   1. Na **vyberte** stránce **Microsoft Azure Management**a potom klikněte na tlačítko **vyberte**.

   1. Na stránce cloudové aplikace, klikněte na tlačítko **provádí**.

1. Klikněte na tlačítko **co když**.

## <a name="test-your-conditional-access-policy"></a>Otestovat své zásady podmíněného přístupu

V předchozí části jste zjistili, jak vyhodnotit simulované přihlášení. Kromě simulaci byste měli také otestovat své zásady podmíněného přístupu k zajištění, že funguje podle očekávání.

K otestování vašich zásad, zkuste pro přihlášení k vaší [webu Azure portal](https://portal.azure.com) pomocí vaší **Isabella Simonsen** testovací účet. Zobrazí se dialogové okno, které vyžaduje, abyste přijali vaše podmínky použití.

![Podmínky použití](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte testovacího uživatele a zásady podmíněného přístupu:

- Pokud si nejste jisti, jak odstranit uživatele služby Azure AD, přečtěte si téma [odstranit uživatele z Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Pokud chcete odstranit zásady, vyberte zásady a klikněte na **odstranit** v panelu nástrojů Rychlý přístup.

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/require-tou/33.png)

- Odstranění vašich podmínek použití, vyberte ho a pak klikněte na tlačítko **odstranit podmínky** na panelu nástrojů v horní části.

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyžadovat vícefaktorové ověřování pro konkrétní aplikace](app-based-mfa.md)
> [blokování přístupu, když se zjistí ohrožení relace](app-sign-in-risk.md)
