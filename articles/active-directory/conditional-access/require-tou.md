---
title: Rychlý start – vyžadují podmínky použití přijmout před přístup ke cloudovým aplikacím, které se nechrání podmíněným přístupem Azure Active Directory | Dokumentace Microsoftu
description: V tomto rychlém startu zjistíte, jak můžete vyžadovat, že jsou vaše podmínky použití přijmout před udělením přístupu k vybrané cloudovým aplikacím tak podmíněný přístup Azure Active Directory.
services: active-directory
keywords: podmíněný přístup pro aplikace, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu, podmínky použití
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d89c887930b1ea1310e1579f97b8b3e76b7985a8
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530747"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Rychlý start: Vyžadovat podmínky použití přijmout před přístupem k cloudových aplikací 

Před použitím některých cloudových aplikací ve vašem prostředí, můžete chtít vyžadovat souhlas uživatele v podobě přijetí vaše podmínky použití (ToU). Podmíněný přístup služby Azure Active Directory (Azure AD) vám nabízí: 

- Jednoduchý způsob konfigurace podmínek použití
- Možnost vyžadovat přijetí vaše podmínky použití prostřednictvím zásad podmíněného přístupu  

Tento rychlý start ukazuje, jak nakonfigurovat [zásad podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md) , která vyžaduje podmínky použití přijmout pro vybranou cloudovou aplikaci ve vašem prostředí.

![Vytvoření zásad](./media/require-tou/5555.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.



## <a name="prerequisites"></a>Požadavky 

K dokončení tohoto scénáře v tomto rychlém startu budete potřebovat:

- **Přístup do Azure AD Premium edition** – podmíněný přístup Azure AD je funkce Azure AD Premium. 

- **Testovací účet s názvem Isabella Simonsen** – Pokud si nejste jisti, jak vytvořit testovací účet, najdete v článku [přidat cloudové uživatele](../fundamentals/add-users-azure-active-directory.md#add-cloud-based-users).


## <a name="test-your-sign-in"></a>Test přihlášení

Cílem tohoto kroku je získat dojem přihlašovací prostředí bez zásady podmíněného přístupu.

**K otestování vaše přihlášení:**

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com/) jako Isabella Simonsen.

2. Odhlaste se.




## <a name="create-your-terms-of-use"></a>Vytvořit vaše podmínky použití

Tato část obsahuje kroky k vytvoření ukázkové podmínky použití. Když vytvoříte podmínky použití, vyberte hodnotu pro **vynutit pomocí šablon zásad podmíněného přístupu**. Výběr **vlastní zásady pro** otevře dialogové okno Vytvořit novou zásadu podmíněného přístupu poté, co byla vytvořena vaše podmínky použití.


**Chcete-li vytvořit vaše podmínky použití:**

1. V aplikaci Microsoft Word vytvoříte nový textový dokument.

2. Typ **Moje podmínky použití**a potom uložte dokument na počítač jako **mytou.pdf**.

3. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.

4. Na webu Azure Portal, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory**. 

    ![Azure Active Directory](./media/require-tou/02.png)

5. Na **Azure Active Directory** stránku, **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/require-tou/03.png) 

6. V **spravovat** klikněte na tlačítko **podmínky použití**.

    ![Podmínky použití](./media/require-tou/04.png) 

7. V nabídce v horní části klikněte na tlačítko **nové podmínky**.

    ![Podmínky použití](./media/require-tou/05.png) 

8. Na **nové podmínky použití** stránky:

    ![Podmínky použití](./media/require-tou/112.png) 

    a. V **název** textové pole, typ **Moje TOU**.

    b. V **zobrazovaný název** textové pole, typ **Moje TOU**.

    c. Nahrajte vaše podmínky použití souboru PDF.

    d. Jako **jazyk**vyberte **Angličtina**.

    e. Jako **vyžadují, aby uživatelé rozbalili podmínky použití**vyberte **na**.

    f. Jako **vynutit pomocí šablon zásad podmíněného přístupu**vyberte **vlastní zásady pro**.

    g. Klikněte na možnost **Vytvořit**.
 


## <a name="create-your-conditional-access-policy"></a>Vytvořte zásadu podmíněného přístupu 

Tato část ukazuje, jak vytvořit zásady podmíněného přístupu vyžaduje. Scénář v tomto rychlém startu používá:

- Azure portal jako zástupný symbol pro cloudové aplikace, která vyžaduje vaše podmínky použití přijmout. 
- Ukázkového uživatele k otestování zásady podmíněného přístupu.  

V zásadách nastavte:

|Nastavení |Hodnota|
|---     | --- |
|Uživatelé a skupiny | Isabella Simonsen |
|Cloudové aplikace | Správa Microsoft Azure |
|Udělení přístupu | Moje podmínek použití |
 

![Vytvoření zásad](./media/require-tou/1234.png)

 


**Ke konfiguraci zásady podmíněného přístupu:**

1. Na **nový** stránku, **název** textové pole, typ **vyžadují podmínky použití pro Isabella**.

    ![Název](./media/require-tou/71.png)

2. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

    ![Uživatelé a skupiny](./media/require-tou/06.png)

3. Na **uživatelů a skupin** stránky:

    ![Uživatelé a skupiny](./media/require-tou/24.png)

    a. Klikněte na tlačítko **výběr uživatelů a skupin**a pak vyberte **uživatelů a skupin**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** stránce **Isabella Simonsen**a potom klikněte na tlačítko **vyberte**.

    d. Na **uživatelů a skupin** klikněte na **provádí**.

4. Klikněte na tlačítko **cloudové aplikace**.

    ![Cloudové aplikace](./media/require-tou/08.png)

5. Na **cloudové aplikace** stránky:

    ![Vyberte cloudové aplikace](./media/require-tou/26.png)

    a. Klikněte na tlačítko **vyberte aplikace**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** stránce **Microsoft Azure Management**a potom klikněte na tlačítko **vyberte**.

    d. Na **cloudové aplikace** klikněte na **provádí**.


6. V **ovládací prvky přístupu** klikněte na tlačítko **udělení**.

    ![Ovládací prvky přístupu](./media/require-tou/10.png)

7. Na **udělení** stránky:

    ![Udělení](./media/require-tou/111.png)

    a. Vyberte **udělit přístup**.

    a. Vyberte **Moje podmínek použití**.

    b. Klikněte na **Vybrat**.

8. V **povolit zásady** klikněte na tlačítko **na**.

    ![Povolit zásadu](./media/require-tou/18.png)

9. Klikněte na možnost **Vytvořit**.


## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení Simulovaná přihlášení

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít vědět, jestli funguje podle očekávání. Jako první krok pomocí podmíněného přístupu, nástroj pro co když zásady pro simulaci u přihlášení z testovacího uživatele. Simulace odhad dopadu tomto přihlášení na zásady a generuje sestavy simulace.  

K inicializaci co, pokud nástroj pro vyhodnocení zásad, nastavit:

- **Isabella Simonsen** jako uživatel 
- **Microsoft Azure Management** jako cloudové aplikace


Kliknutím na **co když** vytvoří sestavu simulace, která zobrazí:

- **Vyžadovat podmínky použití pro Isabella** pod **zásady, které budou platit** 
- **Moje podmínek použití** jako **udělení ovládacích prvků**.

![Co když nástroj zásad](./media/require-tou/79.png)



**Vyhodnotit své zásady podmíněného přístupu:**

1. Na [podmíněného přístupu – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce v horní části na stránce **co když**.  
 
    ![What If](./media/require-tou/14.png)

2. Klikněte na tlačítko **uživatelé**vyberte **Isabella Simonsen**a potom klikněte na tlačítko **vyberte**.

    ![Uživatel](./media/require-tou/15.png)

2. Vyberte cloudové aplikace:

    ![Cloudové aplikace](./media/require-tou/16.png)

    a. Klikněte na tlačítko **cloudové aplikace**.

    b. Na **stránky aplikací cloudu**, klikněte na tlačítko **vyberte aplikace**.

    c. Klikněte na **Vybrat**.

    d. Na **vyberte** stránce **Microsoft Azure Management**a potom klikněte na tlačítko **vyberte**.

    e. Na stránce cloudové aplikace, klikněte na tlačítko **provádí**.

3. Klikněte na tlačítko **co když**.


## <a name="test-your-conditional-access-policy"></a>Otestovat své zásady podmíněného přístupu

V předchozí části jste zjistili, jak vyhodnotit simulované přihlášení. Kromě simulaci byste měli také otestovat své zásady podmíněného přístupu k zajištění, že funguje podle očekávání. 

K otestování vašich zásad, zkuste pro přihlášení k vaší [webu Azure portal](https://portal.azure.com) pomocí vaší **Isabella Simonsen** testovací účet. Zobrazí se dialogové okno, které vyžaduje, abyste přijali vaše podmínky použití.

![Podmínky použití](./media/require-tou/57.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte testovacího uživatele a zásady podmíněného přístupu:

- Pokud si nejste jisti, jak odstranit uživatele služby Azure AD, přečtěte si téma [odstranit uživatele z Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Pokud chcete odstranit zásady, vyberte zásady a klikněte na **odstranit** v panelu nástrojů Rychlý přístup.

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/require-tou/33.png)

- Odstranění vašich podmínek použití, vyberte ho a pak klikněte na tlačítko **odstranit podmínky** na panelu nástrojů v horní části. 

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyžadovat vícefaktorové ověřování pro konkrétní aplikace](app-based-mfa.md)
> [blokování přístupu, když se zjistí ohrožení relace](app-sign-in-risk.md)

