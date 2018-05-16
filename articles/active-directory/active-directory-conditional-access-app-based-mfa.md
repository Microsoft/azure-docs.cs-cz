---
title: Rychlý start - vyžadovat vícefaktorové ověřování (MFA) pro konkrétní aplikace s Azure Active Directory podmíněným přístupem | Microsoft Docs
description: V tento rychlý start zjistíte, jak dokáže spojit požadavků ověřování typu používaná cloudové aplikace pomocí podmíněného přístupu Azure Active Directory (Azure AD).
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 475b0229b9e627a56b02d2299ee2e5400aa0ede1
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Rychlý úvod: Vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory 

Ke zjednodušení přihlašování uživatelů, můžete chtít povolit je pro přihlášení ke cloudové aplikace pomocí uživatelského jména a hesla. Mnoho prostředí však mít alespoň několik aplikací, pro které je třeba vyžadovat silnější formu ověření účtu, jako je vícefaktorové ověřování (MFA). To může být pro příklad true, pro přístup k vaší organizaci e-mailovému systému nebo aplikace pro oddělení lidských zdrojů. V Azure Active Directory (Azure AD) můžete dosažení tohoto cíle se zásadami podmíněného přístupu.    

Tento rychlý start ukazuje, jak nakonfigurovat [zásady podmíněného přístupu Azure AD](active-directory-conditional-access-azure-portal.md) , vyžaduje vícefaktorové ověřování pro vybraný cloud aplikaci ve vašem prostředí.

![Vytvoření zásad](./media/active-directory-conditional-access-app-based-mfa/32.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.



## <a name="prerequisites"></a>Požadavky 

Chcete-li dokončit tento scénář v tento rychlý start, je třeba:

- **Přístup k Azure AD Premium edice** -podmíněného přístupu Azure AD je funkce Azure AD Premium. 

- **Testovací účet s názvem Isabella Simonsen** – Pokud nevíte jak vytvořit testovací účet, najdete v části [přidání cloudových uživatelů](add-users-azure-active-directory.md#add-cloud-based-users).



## <a name="create-your-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu 

V této části ukazuje, jak vytvořit zásady požadované podmíněného přístupu. Scénář v tento rychlý start používá:

- Portál Azure jako zástupný symbol pro cloudové aplikace, která vyžaduje vícefaktorové ověřování. 
- Ukázkového uživatele k testování zásad podmíněného přístupu.  

V zásadách nastavte:

|Nastavení |Hodnota|
|---     | --- |
|Uživatelé a skupiny | Isabella Simonsen |
|Cloudové aplikace | Správa Microsoft Azure |
|Udělení přístupu | Vyžadovat vícefaktorové ověřování |
 

![Vytvoření zásad](./media/active-directory-conditional-access-app-based-mfa/31.png)

 


**Konfigurace zásady podmíněného přístupu:**

1. Přihlaste se k vaší [portál Azure](https://portal.azure.com) jako globální správce.

2. Na portálu Azure v levé navigační panel, klikněte na tlačítko **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. Na **Azure Active Directory** stránky v **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. Na **podmíněného přístupu** stránky, na panelu nástrojů v horní části klikněte na tlačítko **přidat**.

    ![Přidat](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. Na **nový** stránky v **název** textovému poli, typ **vyžadovat vícefaktorové ověřování pro přístup k Azure portálu**.

    ![Název](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

    ![Uživatelé a skupiny](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. Na **uživatelů a skupin** proveďte následující kroky:

    ![Uživatelé a skupiny](./media/active-directory-conditional-access-app-based-mfa/24.png)

    a. Klikněte na tlačítko **vyberte uživatele a skupiny**a potom vyberte **uživatelů a skupin**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** vyberte **Isabella Simonsen**a potom klikněte na **vyberte**.

    d. Na **uživatelů a skupin** klikněte na tlačítko **provést**.

8. Klikněte na tlačítko **cloudových aplikací**.

    ![Cloudové aplikace](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. Na **cloudových aplikací** proveďte následující kroky:

    ![Vyberte cloudové aplikace](./media/active-directory-conditional-access-app-based-mfa/26.png)

    a. Klikněte na tlačítko **vybrat aplikace**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** vyberte **Microsoft Azure Management**a potom klikněte na **vyberte**.

    d. Na **cloudových aplikací** klikněte na tlačítko **provádí**.


10. V **přístup k ovládacím prvkům** klikněte na tlačítko **Grant**.

    ![Ovládací prvky přístupu](./media/active-directory-conditional-access-app-based-mfa/10.png)

11. Na **Grant** proveďte následující kroky:

    ![Udělení](./media/active-directory-conditional-access-app-based-mfa/11.png)

    a. Vyberte **udělit přístup**.

    a. Vyberte **vyžadovat vícefaktorové ověřování**.

    b. Klikněte na **Vybrat**.

12. V **povolit zásady** klikněte na tlačítko **na**.

    ![Povolit zásadu](./media/active-directory-conditional-access-app-based-mfa/18.png)

13. Klikněte na možnost **Vytvořit**.


## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení simulované přihlášení

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít vědět, zda vše funguje podle očekávání. Jako první krok použijte podmíněný přístup, co v případě zásady nástroje pro simulaci přihlášení z testovacího uživatele. Simulace odhadne dopad na tomto přihlášení má na vaše zásady a generuje sestavy simulace.  

K chybě při inicializaci co, pokud se nástroj hodnocení zásady, nastavit:

- **Isabella Simonsen** jako uživatel 
- **Microsoft Azure Management** jako cloudové aplikace

 Kliknutím na tlačítko **co když** vytvoří sestavu simulace, která obsahuje:

- **Vícefaktorové ověřování vyžadovat pro přístup k Azure portálu** pod **zásady, které budou platit** 
- **Vyžadovat vícefaktorové ověřování** jako **ovládací prvky Grant**.

![Co když zásada – nástroj](./media/active-directory-conditional-access-app-based-mfa/23.png)



**Vyhodnocení požadované zásady podmíněného přístupu:**

1. Na [podmíněný přístup – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte na stránce v nabídce v horní části **co když**.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. Klikněte na tlačítko **uživatelé**, vyberte **Isabella Simonsen**a potom klikněte na **vyberte**.

    ![Uživatel](./media/active-directory-conditional-access-app-based-mfa/15.png)

2. Pokud chcete vybrat cloudové aplikace, proveďte následující kroky:

    ![Cloudové aplikace](./media/active-directory-conditional-access-app-based-mfa/16.png)

    a. Klikněte na tlačítko **cloudových aplikací**.

    b. Na **stránka cloudové aplikace**, klikněte na tlačítko **vybrat aplikace,**.

    c. Klikněte na **Vybrat**.

    d. Na **vyberte** vyberte Microsoft Azure Management ** a pak klikněte na tlačítko **vyberte**.

    e. Na stránce cloudové aplikace, klikněte na **provádí**.

3. Klikněte na tlačítko **co když**.


## <a name="test-your-conditional-access-policy"></a>Testování zásady podmíněného přístupu

V předchozí části jste se naučili, jak vyhodnotit simulované přihlášení. Kromě simulace byste měli také otestovat zásady podmíněného přístupu k zajištění, že funguje podle očekávání. 

K otestování vaše zásady, pokuste se přihlásit k vaší [portál Azure](https://portal.azure.com) pomocí vaší **Isabella Simonsen** testovací účet. Měli byste vidět dialog, který vyžaduje, abyste nastavili svůj účet do dalšího ověření zabezpečení.

![Ověřování pomocí služby Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte testovacího uživatele a zásady podmíněného přístupu:

- Pokud nevíte jak odstranit uživatele Azure AD, najdete v části [odstranit uživatele z Azure AD](add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Pokud chcete odstranit vaše zásady, vyberte zásady a pak klikněte na tlačítko **odstranit** na panelu nástrojů Rychlý přístup.

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/33.png)


## <a name="next-steps"></a>Další postup

Pokud chcete získat další informace o podmíněného přístupu, přečtěte si téma [podmíněného přístupu Azure Active Directory](active-directory-conditional-access-azure-portal.md).

