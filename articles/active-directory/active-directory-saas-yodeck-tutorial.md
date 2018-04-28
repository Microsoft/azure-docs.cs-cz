---
title: 'Kurz: Azure Active Directory integrace s Yodeck | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: 550eb3f25a1b17600a5c4eee51ff1867d2f1fb34
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Kurz: Azure Active Directory integrace s Yodeck

V tomto kurzu zjistěte, jak integrovat Yodeck s Azure Active Directory (Azure AD).

Integrace Yodeck s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Yodeck.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Yodeck (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Yodeck, potřebujete následující položky:

- Předplatné služby Azure AD
- Yodeck jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Yodeck z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-yodeck-from-the-gallery"></a>Přidání Yodeck z Galerie
Při konfiguraci integrace Yodeck do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Yodeck z galerie.

**Pokud chcete přidat Yodeck z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Yodeck**, vyberte **Yodeck** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Yodeck v seznamu výsledků](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Yodeck podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Yodeck je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Yodeck musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Yodeck, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Yodeck](#create-a-yodeck-test-user)**  – Pokud chcete mít protějšek Britta Simon v Yodeck propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Yodeck.

**Ke konfiguraci Azure AD jednotné přihlašování s Yodeck, proveďte následující kroky:**

1. Na portálu Azure na **Yodeck** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_samlbase.png)

3. Na **Yodeck domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Yodeck domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url.png)

    V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL: `https://app.yodeck.com/api/v1/account/metadata/`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Yodeck domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://app.yodeck.com/login`

5. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_certificate.png)

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-yodeck-tutorial/tutorial_general_400.png)
    
7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Yodeck jako správce.

8. Klikněte na **uživatelská nastavení** formuláře pravém horním rohu stránky a vyberte možnost **nastavení účtu**.

    ![Konfigurace Yodeck](./media/active-directory-saas-yodeck-tutorial/configure1.png)

9. Vyberte **SAML** a proveďte následující kroky:

    ![Konfigurace Yodeck](./media/active-directory-saas-yodeck-tutorial/configure2.png)

    a. Vyberte **Import z adresy URL**.

    b. V **URL** textovému poli, vložte **adresu Url aplikace federační Metadata** hodnotu, kterou jste zkopírovali z portálu Azure a klikněte na tlačítko **Import**.
    
    c. Po importu **adresu Url aplikace federační Metadata**, ostatní pole naplnit automaticky.

    d. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-yodeck-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-yodeck-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-yodeck-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-yodeck-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-yodeck-test-user"></a>Vytvoření zkušebního uživatele Yodeck

Pokud chcete povolit uživatelům Azure AD přihlášení k Yodeck, musí být zřízená do Yodeck.
V případě Yodeck zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Yodeck jako správce.

2. Klikněte na **uživatelská nastavení** formuláře pravém horním rohu stránky a vyberte možnost **uživatelé**.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-yodeck-tutorial/user1.png)

3. Klikněte na **+ uživatele** otevřete **podrobné informace o uživateli** kartě.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-yodeck-tutorial/user2.png)

4. Na **podrobné informace o uživateli** dialogové okno proveďte následující kroky:

    ![Můžete přidat zaměstnance](./media/active-directory-saas-yodeck-tutorial/user3.png)

    a. V **křestní jméno** jako typ křestní jméno uživatele k textovému poli, **Britta**.

    b. V **příjmení** jako typ příjmení uživatele k textovému poli, **Simon**.

    c. V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, **brittasimon@contoso.com**.

    d. Vyberte odpovídající **oprávnění účtu** možnost podle požadavků vaší organizace.
    
    e. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Yodeck.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon Yodeck, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Yodeck**.

    ![V seznamu aplikací na Yodeck odkaz](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Yodeck na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Yodeck.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_203.png

