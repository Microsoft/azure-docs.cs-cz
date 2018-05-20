---
title: 'Kurz: Azure Active Directory integrace s Benefitsolver | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Benefitsolver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 333394c1-b5a7-489c-8f7b-d1a5b4e782ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: jeedes
ms.openlocfilehash: a254f1696d60d825f637b6aa37d05cf95b318a25
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Kurz: Azure Active Directory integrace s Benefitsolver

V tomto kurzu zjistěte, jak integrovat Benefitsolver s Azure Active Directory (Azure AD).

Integrace Benefitsolver s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Benefitsolver.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Benefitsolver (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Benefitsolver, potřebujete následující položky:

- Předplatné služby Azure AD
- Benefitsolver jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Benefitsolver z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-benefitsolver-from-the-gallery"></a>Přidání Benefitsolver z Galerie
Při konfiguraci integrace Benefitsolver do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Benefitsolver z galerie.

**Pokud chcete přidat Benefitsolver z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Benefitsolver**, vyberte **Benefitsolver** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Benefitsolver v seznamu výsledků](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Benefitsolver podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Benefitsolver je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Benefitsolver musí navázat.

V Benefitsolver, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Benefitsolver, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Benefitsolver](#create-a-benefitsolver-test-user)**  – Pokud chcete mít protějšek Britta Simon v Benefitsolver propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Benefitsolver.

**Ke konfiguraci Azure AD jednotné přihlašování s Benefitsolver, proveďte následující kroky:**

1. Na portálu Azure na **Benefitsolver** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_samlbase.png)

3. Na **Benefitsolver domény a adresy URL** část, proveďte následující kroky:

    ![Benefitsolver domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `http://<companyname>.benefitsolver.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.benefitsolver.com/saml20`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL: `https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory Benefitsolver klienta](https://www.businessolver.com/contact) k získání těchto hodnot.

4. Aplikace Benefitsolver očekává SAML kontrolní výrazy ve specifickém formátu, který můžete přidat mapování vlastní atribut vyžaduje vaše **atributy tokenu saml** konfigurace.

    ![Část Benefitsolver atribut](./media/active-directory-saas-benefitsolver-tutorial/tutorial_attribute.png)

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu| Hodnota atributu|
    |---------------|----------------|
    | ClientID | Je nutné získat tuto hodnotu z vaší [tým podpory Benefitsolver klienta](https://www.businessolver.com/contact).|
    | ClientKey | Je nutné získat tuto hodnotu z vaší [tým podpory Benefitsolver klienta](https://www.businessolver.com/contact).|
    | LogoutURL | Je nutné získat tuto hodnotu z vaší [tým podpory Benefitsolver klienta](https://www.businessolver.com/contact).|
    | Číslo zaměstnance | Je nutné získat tuto hodnotu z vaší [tým podpory Benefitsolver klienta](https://www.businessolver.com/contact).|

    a. Klikněte na tlačítko Přidat atribut tím otevřete dialogové okno Přidat atribut.

    ![Část Benefitsolver atribut](./media/active-directory-saas-benefitsolver-tutorial/tutorial_attribute_04.png)
    
    ![Část Benefitsolver atribut](./media/active-directory-saas-benefitsolver-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

6. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_400.png)

8. Konfigurace jednotného přihlašování na **Benefitsolver** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory Benefitsolver](https://www.businessolver.com/contact).

    > [!NOTE]
    > Váš tým podpory Benefitsolver musí provést konfiguraci skutečné jednotné přihlašování. Zobrazí se oznámení, když bylo povoleno jednotné přihlašování pro vaše předplatné.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-benefitsolver-test-user"></a>Vytvoření zkušebního uživatele Benefitsolver

Pokud chcete povolit uživatelům Azure AD přihlášení do Benefitsolver, musí být zřízená do Benefitsolver. V případě Benefitsolver zaměstnanec data jsou v aplikaci naplněno prostřednictvím soubor úplné zjišťování z vašeho systému HRIS (obvykle je každou noc).

> [!NOTE]
> Můžete použít všechny ostatní Benefitsolver uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Benefitsolver zřídit AAD uživatelské účty.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Benefitsolver.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Benefitsolver, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Benefitsolver**.

    ![V seznamu aplikací na Benefitsolver odkaz](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Benefitsolver na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Benefitsolver.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_203.png

