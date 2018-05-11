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
ms.date: 05/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: b451ede984d3baa8331ec87575557f845686c01f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Rychlý úvod: Vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory 

Ke zjednodušení přihlašování uživatelů, můžete chtít povolit je pro přihlášení ke cloudové aplikace pomocí uživatelského jména a hesla. Mnoho prostředí však mít alespoň několik aplikací, pro které je třeba vyžadovat silnější formu ověření účtu, jako je vícefaktorové ověřování. To může být pro příklad true, pro přístup k vaší organizaci e-mailovému systému nebo aplikace pro oddělení lidských zdrojů. V Azure Active Directory můžete dosažení tohoto cíle se zásadami podmíněného přístupu.    

Tento rychlý start ukazuje, jak nakonfigurovat [zásady podmíněného přístupu Azure AD](active-directory-conditional-access-azure-portal.md) vyžadovat vícefaktorové ověřování pro sadu vybraných cloudových aplikací ve vašem prostředí.


## <a name="scenario-description"></a>Popis scénáře

Tento scénář v tomto článku používá portál Azure jako zástupný symbol pro cloudové aplikace, která vyžaduje službu Multi-Factor authentication pro konkrétního uživatele. Isabella Simonsen je uživatel ve vaší organizaci. Když se uživatel přihlásí k portálu Azure, budete chtít svá provést ještě další ověření svůj účet pomocí služby Multi-Factor authentication.

![Ověřování pomocí služby Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)



## <a name="prerequisites"></a>Požadované součásti 

Chcete-li dokončit tento scénář v tento rychlý start, je třeba:

- **Přístup k Azure AD Premium edice** -podmíněného přístupu Azure AD je funkce Azure AD Premium. Pokud nemáte přístup k edice Azure AD Premium, můžete [registrace pro bezplatnou zkušební verzí](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Testovací účet s názvem Isabella Simonsen** – Pokud nevíte jak vytvořit testovací účet, přečtěte si [tyto pokyny](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).



## <a name="create-your-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu 

V této části ukazuje, jak vytvořit zásady požadované podmíněného přístupu.  
V zásadách můžete nastavit:

|Nastavení |Hodnota|
|---     | --- |
|Uživatelé a skupiny | Isabella Simonsen |
|Cloudové aplikace | Správa Microsoft Azure |
|Udělení | Vyžadovat vícefaktorové ověřování |
 

![Vytvořit zásadu](./media/active-directory-conditional-access-app-based-mfa/21.png)




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


## <a name="evaluate-your-conditional-access-policy"></a>Vyhodnocení požadované zásady podmíněného přístupu

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít vědět, zda vše funguje podle očekávání. Jako první krok, můžete použít [podmíněný přístup, jaké Pokud zásada nástroj](active-directory-conditional-access-whatif.md) k simulaci přihlášení uživatele. Když nakonfigurujete nástroj s **Isabella Simonsen** jako uživatel a **Microsoft Azure Management** jako cloudové aplikace, zobrazí nástroj **vyžadovat vícefaktorové ověřování pro přístup k Azure portálu** v části **Zásady, které se použijí** a **vyžadovat vícefaktorové ověřování** jako **ovládací prvky Grant**.

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

V předchozí části v předchozí části, můžete 

K otestování vaše zásady, pokuste se přihlásit k vaší [portál Azure](https://portal.azure.com) pomocí vaší **Isabella Simonsen** testovací účet. Měli byste vidět dialog, který vyžaduje, abyste nastavili svůj účet do dalšího ověření zabezpečení.

![Ověřování pomocí služby Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="next-steps"></a>Další postup

Pokud chcete získat další informace o podmíněného přístupu, přečtěte si téma [podmíněného přístupu Azure Active Directory](active-directory-conditional-access-azure-portal.md).

