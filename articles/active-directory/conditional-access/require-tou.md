---
title: Podmíněný přístup vyžaduje podmínky použití – Služba Azure Active Directory
description: V tomto rychlém startu se dozvíte, jak můžete vyžadovat, aby byly vaše podmínky použití přijaty před udělením přístupu k vybraným cloudovým aplikacím podmíněným přístupem služby Azure Active Directory.
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
ms.openlocfilehash: 3dd1b4cf554e773f49a15ac5cedcbcc5b3e710b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74380107"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Úvodní příručka: Před přístupem ke cloudovým aplikacím vyžadovat přijetí podmínek použití.

Před přístupem k určitým cloudovým aplikacím ve vašem prostředí můžete chtít získat souhlas uživatelů ve formě přijetí vašich podmínek použití (ToU). Podmíněný přístup azure active directory (Azure AD) vám poskytuje:

- Jednoduchá metoda konfigurace ToU
- Možnost vyžadovat přijetí vašich podmínek použití prostřednictvím zásad podmíněného přístupu  

Tento rychlý start ukazuje, jak nakonfigurovat [zásady podmíněného přístupu Azure AD,](../active-directory-conditional-access-azure-portal.md) které vyžadují přijetí tou pro vybranou cloudovou aplikaci ve vašem prostředí.

![Vytvoření zásad](./media/require-tou/5555.png)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit scénář v tomto rychlém startu, potřebujete:

- **Přístup k edici Azure AD Premium** – podmíněný přístup Azure AD je funkce Azure AD Premium.
- **Testovací účet s názvem Isabella Simonsen** – Pokud nevíte, jak vytvořit testovací účet, přečtěte si informace [o přidání cloudových uživatelů](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Otestujte své přihlášení

Cílem tohoto kroku je získat představu o přihlašovacím prostředí bez zásad podmíněného přístupu.

**Chcete-li přihlásit:**

1. Přihlaste se na [svůj portál Azure](https://portal.azure.com/) jako Isabella Simonsenová.
1. Odhlaste se.

## <a name="create-your-terms-of-use"></a>Vytvořte si podmínky použití

Tato část obsahuje kroky k vytvoření ukázkového bodu ToU. Při vytváření tou, vyberete hodnotu **pro vynutit s šablony zásad podmíněného přístupu**. Výběrem **vlastní zásady** otevřete dialogové okno a vytvořte novou zásadu podmíněného přístupu, jakmile je vaše tou bylo vytvořeno.

**Jak vytvořit podmínky použití:**

1. V aplikaci Microsoft Word vytvořte nový dokument.
1. Zadejte **mé podmínky použití**a uložte dokument do počítače jako **mytou.pdf**.
1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Na webu Azure Portal klikněte na levém panelu na **Položku Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Na stránce **Azure Active Directory** klikněte v části **Zabezpečení** na **podmíněný přístup**.

   ![Podmíněný přístup](./media/require-tou/03.png)

1. V části **Spravovat** klikněte na **Podmínky použití**.

   ![Podmínky použití](./media/require-tou/04.png)

1. V nabídce nahoře klikněte na **Nové termíny**.

   ![Podmínky použití](./media/require-tou/05.png)

1. Na stránce **Nové podmínky použití:**

   ![Podmínky použití](./media/require-tou/112.png)

   1. Do textového pole **Název** zadejte **moje tou**.
   1. Do textového pole **Zobrazované jméno** zadejte **moje zadání typu TOU**.
   1. Nahrajte svůj soubor PDF s podmínkami použití.
   1. Jako **jazyk**vyberte **možnost Angličtina**.
   1. Jako **Vyžadovat, aby uživatelé rozbalili podmínky použití**, vyberte Možnost **Zapnuto**.
   1. Jako **Příkaz Enforce s šablonami zásad podmíněného přístupu**vyberte možnost Vlastní **zásady**.
   1. Klikněte na **Vytvořit**.

## <a name="create-your-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

Tato část ukazuje, jak vytvořit požadované zásady podmíněného přístupu. Scénář v tomto rychlém startu používá:

- Portál Azure jako zástupný symbol pro cloudovou aplikaci, která vyžaduje přijetí vašeho tou. 
- Ukázkový uživatel otestovat zásady podmíněného přístupu.  

V zásadách nastavte:

| Nastavení | Hodnota |
| --- | --- |
| Uživatelé a skupiny | Isabella Simonsenová |
| Cloudové aplikace | Správa Microsoft Azure |
| Udělení přístupu | Můj TOU |

![Vytvoření zásad](./media/require-tou/1234.png)

**Postup konfigurace zásad podmíněného přístupu:**

1. Na stránce **Nový** zadejte do textového pole **Název** **příkaz Vyžadovat zadání typu TOU pro isabella**.

   ![Name (Název)](./media/require-tou/71.png)

1. V části **Přiřazení** klikněte na **Položku Uživatelé a skupiny**.

   ![Uživatelé a skupiny](./media/require-tou/06.png)

1. Na stránce **Uživatelé a skupiny:**

   ![Uživatelé a skupiny](./media/require-tou/24.png)

   1. Klikněte na **Vybrat uživatele a skupiny**a potom vyberte **Uživatelé a skupiny**.
   1. Klepněte na **tlačítko Vybrat**.
   1. Na stránce **Select** vyberte **Položku Isabella Simonsenová**a klepněte na tlačítko **Vybrat**.
   1. Na stránce **Uživatelé a skupiny** klikněte na **Hotovo**.
1. Klikněte na **Cloudové aplikace**.

   ![Cloudové aplikace](./media/require-tou/08.png)

1. Na stránce **Cloudové aplikace:**

   ![Výběr cloudových aplikací](./media/require-tou/26.png)

   1. Klikněte na **Vybrat aplikace**.
   1. Klepněte na **tlačítko Vybrat**.
   1. Na stránce **Select** vyberte **Microsoft Azure Management**a klikněte na **Vybrat**.
   1. Na stránce **Cloudové aplikace** klikněte na **Hotovo**.
1. V části **Ovládací prvky aplikace Access** klepněte na tlačítko **Udělit**.

   ![Řízení přístupu](./media/require-tou/10.png)

1. Na stránce **Grant:**

   ![Oprávnění](./media/require-tou/111.png)

   1. Vyberte **možnost Udělit přístup**.
   1. Vyberte **možnost Můj TOU**.
   1. Klepněte na **tlačítko Vybrat**.
1. V části **Povolit zásady** klikněte **na Zapnuto**.

   ![Povolení zásady](./media/require-tou/18.png)

1. Klikněte na **Vytvořit**.

## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení simulovanéregistrace

Nyní, když jste nakonfigurovali zásady podmíněného přístupu, pravděpodobně budete chtít vědět, zda funguje podle očekávání. Jako první krok použijte nástroj zásad podmíněného přístupu co když k simulaci přihlášení testovacího uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace.  

Chcete-li inicializovat nástroj vyhodnocení zásad **Co když,** nastavte:

- **Isabella Simonsen** jako uživatel
- **Microsoft Azure Management** jako cloudová aplikace

Kliknutím na **Co když** vytvoříte simulační sestavu, která zobrazuje:

- **Vyžadovat TOU pro Isabella v** rámci **zásad, které budou platit**
- **Moje TOU** jako **Grant Controls**.

![Co když nástroj politiky](./media/require-tou/79.png)

**Vyhodnocení zásad podmíněného přístupu:**

1. Na stránce [Podmíněný přístup – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce nahoře na **Co kdyby**.  

   ![Citlivostní analýza](./media/require-tou/14.png)

1. Klepněte na položku **Uživatelé**, vyberte **položku Isabella Simonsen**a potom klepněte na tlačítko **Vybrat**.

   ![Uživatel](./media/require-tou/15.png)

1. Výběr cloudové aplikace:

   ![Cloudové aplikace](./media/require-tou/16.png)

   1. Klikněte na **Cloudové aplikace**.
   1. Na **stránce Cloudové aplikace**klikněte na **Vybrat aplikace**.
   1. Klepněte na **tlačítko Vybrat**.
   1. Na stránce **Select** vyberte **Microsoft Azure Management**a klikněte na **Vybrat**.
   1. Na stránce cloudových aplikací klikněte na **Hotovo**.
1. Klepněte na **položku Co-li**.

## <a name="test-your-conditional-access-policy"></a>Otestujte zásady podmíněného přístupu

V předchozí části jste se naučili, jak vyhodnotit simulované přihlášení. Kromě simulace byste měli také otestovat zásady podmíněného přístupu, abyste zajistili, že bude fungovat podle očekávání.

Chcete-li otestovat zásady, zkuste se přihlásit k [portálu Azure](https://portal.azure.com) pomocí testovacího účtu **Isabella Simonsen.** Měl by se zobrazit dialog, který vyžaduje, abyste přijali podmínky použití.

![Podmínky použití](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte testovacího uživatele a zásady podmíněného přístupu:

- Pokud nevíte, jak odstranit uživatele Azure AD, přečtěte si informace [o odstranění uživatelů z Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Pokud chcete zásadu odstranit, vyberte zásadu a na panelu nástrojů rychlý přístup klikněte na **Odstranit.**

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/require-tou/33.png)

- Pokud chcete podmínky použití odstranit, vyberte je a nahoře klikněte na **Odstranit termíny** na panelu nástrojů.

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyžadovat vícefaktorové informace pro konkrétní aplikace](app-based-mfa.md)
> [Blokovat přístup při zjištění rizika relace](app-sign-in-risk.md)
