---
title: Rychlý start – vyžadovat vícefaktorové ověřování (MFA) pro konkrétní aplikace s podmíněným přístupem Azure Active Directory | Dokumentace Microsoftu
description: V tomto rychlém startu zjistíte, jak můžete navázat vaše požadavky na ověřování typu používaná cloudové aplikace pomocí podmíněného přístupu Azure Active Directory (Azure AD).
services: active-directory
keywords: podmíněný přístup pro aplikace, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
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
ms.openlocfilehash: eac2211a643e6b297117788da90af6da6eaa1433
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530632"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Rychlý start: Vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory 

Pro zjednodušení přihlašování vašich uživatelů, můžete chtít umožnit přihlásit k vašim cloudovým aplikacím pomocí uživatelského jména a hesla. Mnoho prostředí však mít aspoň pár aplikací, pro které je vhodné tak, aby vyžadovala silnější formu ověření účtu, jako je například vícefaktorové ověřování (MFA). To může být pro příklad true pro přístup k vaší organizaci e-mailovému systému nebo aplikace HR. Ve službě Azure Active Directory (Azure AD) můžete dosažení tohoto cíle se zásadami podmíněného přístupu.    

Tento rychlý start ukazuje, jak nakonfigurovat [zásad podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md) , který vyžaduje vícefaktorové ověřování pro vybranou cloudovou aplikaci ve vašem prostředí.

![Vytvoření zásad](./media/app-based-mfa/32.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.



## <a name="prerequisites"></a>Požadavky 

K dokončení tohoto scénáře v tomto rychlém startu budete potřebovat:

- **Přístup do Azure AD Premium edition** – podmíněný přístup Azure AD je funkce Azure AD Premium. 

- **Testovací účet s názvem Isabella Simonsen** – Pokud si nejste jisti, jak vytvořit testovací účet, najdete v článku [přidat cloudové uživatele](../fundamentals/add-users-azure-active-directory.md#add-cloud-based-users).


## <a name="test-your-sign-in"></a>Test přihlášení

Cílem tohoto kroku je získat dojem přihlašovací prostředí bez zásady podmíněného přístupu.

**Inicializace prostředí:**

1. Přihlaste se k webu Azure portal jako Isabella Simonsen.

2. Odhlaste se.


## <a name="create-your-conditional-access-policy"></a>Vytvořte zásadu podmíněného přístupu 

Tato část ukazuje, jak vytvořit zásady podmíněného přístupu vyžaduje. Scénář v tomto rychlém startu používá:

- Azure portal jako zástupný symbol pro cloudové aplikace, která vyžaduje vícefaktorové ověřování. 
- Ukázkového uživatele k otestování zásady podmíněného přístupu.  

V zásadách nastavte:

|Nastavení |Hodnota|
|---     | --- |
|Uživatelé a skupiny | Isabella Simonsen |
|Cloudové aplikace | Správa Microsoft Azure |
|Udělení přístupu | Vyžadovat vícefaktorové ověřování |
 

![Vytvoření zásad](./media/app-based-mfa/31.png)

 


**Ke konfiguraci zásady podmíněného přístupu:**

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.

2. Na webu Azure Portal, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory**. 

    ![Azure Active Directory](./media/app-based-mfa/02.png)

3. Na **Azure Active Directory** stránku, **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/app-based-mfa/03.png)
 
4. Na **podmíněného přístupu** klikněte na stránku, na panelu nástrojů v horní části **přidat**.

    ![Přidat](./media/app-based-mfa/04.png)

5. Na **nový** stránku, **název** textové pole, typ **vyžadovat vícefaktorové ověřování pro přístup na portál Azure**.

    ![Název](./media/app-based-mfa/05.png)

6. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

    ![Uživatelé a skupiny](./media/app-based-mfa/06.png)

7. Na **uživatelů a skupin** stránce, proveďte následující kroky:

    ![Uživatelé a skupiny](./media/app-based-mfa/24.png)

    a. Klikněte na tlačítko **výběr uživatelů a skupin**a pak vyberte **uživatelů a skupin**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** stránce **Isabella Simonsen**a potom klikněte na tlačítko **vyberte**.

    d. Na **uživatelů a skupin** klikněte na **provádí**.

8. Klikněte na tlačítko **cloudové aplikace**.

    ![Cloudové aplikace](./media/app-based-mfa/08.png)

9. Na **cloudové aplikace** stránce, proveďte následující kroky:

    ![Vyberte cloudové aplikace](./media/app-based-mfa/26.png)

    a. Klikněte na tlačítko **vyberte aplikace**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** stránce **Microsoft Azure Management**a potom klikněte na tlačítko **vyberte**.

    d. Na **cloudové aplikace** klikněte na **provádí**.


10. V **ovládací prvky přístupu** klikněte na tlačítko **udělení**.

    ![Ovládací prvky přístupu](./media/app-based-mfa/10.png)

11. Na **udělení** stránce, proveďte následující kroky:

    ![Udělení](./media/app-based-mfa/11.png)

    a. Vyberte **udělit přístup**.

    a. Vyberte **vyžadovat vícefaktorové ověřování**.

    b. Klikněte na **Vybrat**.

12. V **povolit zásady** klikněte na tlačítko **na**.

    ![Povolit zásadu](./media/app-based-mfa/18.png)

13. Klikněte na možnost **Vytvořit**.


## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení Simulovaná přihlášení

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít vědět, jestli funguje podle očekávání. Jako první krok pomocí podmíněného přístupu, nástroj pro co když zásady pro simulaci u přihlášení z testovacího uživatele. Simulace odhad dopadu tomto přihlášení na zásady a generuje sestavy simulace.  

K inicializaci co, pokud nástroj pro vyhodnocení zásad, nastavit:

- **Isabella Simonsen** jako uživatel 
- **Microsoft Azure Management** jako cloudové aplikace

 Kliknutím na **co když** vytvoří sestavu simulace, která zobrazí:

- **Vyžadovat vícefaktorové ověřování pro přístup na portál Azure** pod **zásady, které budou platit** 
- **Vyžadovat vícefaktorové ověřování** jako **udělení ovládacích prvků**.

![Co když nástroj zásad](./media/app-based-mfa/23.png)



**Vyhodnotit své zásady podmíněného přístupu:**

1. Na [podmíněného přístupu – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce v horní části na stránce **co když**.  
 
    ![What If](./media/app-based-mfa/14.png)

2. Klikněte na tlačítko **uživatelé**vyberte **Isabella Simonsen**a potom klikněte na tlačítko **vyberte**.

    ![Uživatel](./media/app-based-mfa/15.png)

2. Vyberte cloudové aplikace, proveďte následující kroky:

    ![Cloudové aplikace](./media/app-based-mfa/16.png)

    a. Klikněte na tlačítko **cloudové aplikace**.

    b. Na **stránky aplikací cloudu**, klikněte na tlačítko **vyberte aplikace**.

    c. Klikněte na **Vybrat**.

    d. Na **vyberte** stránce **Microsoft Azure Management**a potom klikněte na tlačítko **vyberte**.

    e. Na stránce cloudové aplikace, klikněte na tlačítko **provádí**.

3. Klikněte na tlačítko **co když**.


## <a name="test-your-conditional-access-policy"></a>Otestovat své zásady podmíněného přístupu

V předchozí části jste zjistili, jak vyhodnotit simulované přihlášení. Kromě simulaci byste měli také otestovat své zásady podmíněného přístupu k zajištění, že funguje podle očekávání. 

K otestování vašich zásad, zkuste pro přihlášení k vaší [webu Azure portal](https://portal.azure.com) pomocí vaší **Isabella Simonsen** testovací účet. Zobrazí se dialogové okno, které vyžadují nastavení účtu pro dodatečné ověření zabezpečení.

![Ověřování pomocí služby Multi-Factor Authentication](./media/app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte testovacího uživatele a zásady podmíněného přístupu:

- Pokud si nejste jisti, jak odstranit uživatele služby Azure AD, přečtěte si téma [odstranit uživatele z Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Pokud chcete odstranit zásady, vyberte zásady a klikněte na **odstranit** v panelu nástrojů Rychlý přístup.

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/app-based-mfa/33.png)


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyžadovat podmínky použití přijmout](require-tou.md)
> [blokování přístupu, když se zjistí ohrožení relace](app-sign-in-risk.md)
