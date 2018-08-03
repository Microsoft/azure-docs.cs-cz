---
title: 'Kurz: Integrace Azure Active Directory se službou Yodeck | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: b017efd2c170f543041dcb35a3a3d040389d1dac
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436790"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Kurz: Integrace Azure Active Directory se službou Yodeck

V tomto kurzu se dozvíte, jak integrovat Yodeck s Azure Active Directory (Azure AD).

Yodeck integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Yodeck.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Yodeck (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Yodeck, potřebujete následující položky:

- S předplatným služby Azure AD
- Yodeck jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Yodeck z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-yodeck-from-the-gallery"></a>Přidání Yodeck z Galerie
Konfigurace integrace Yodeck do služby Azure AD, budete muset přidat Yodeck z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Yodeck z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Yodeck**vyberte **Yodeck** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Yodeck v seznamu výsledků](./media/yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Yodeck podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Yodeck je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Yodeck potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Yodeck, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Yodeck](#create-a-yodeck-test-user)**  – Pokud chcete mít protějšek Britta Simon Yodeck, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Yodeck.

**Ke konfiguraci Azure AD jednotné přihlašování s Yodeck, proveďte následující kroky:**

1. Na webu Azure Portal na **Yodeck** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/yodeck-tutorial/tutorial_yodeck_samlbase.png)

1. Na **Yodeck domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Yodeck domény a adresy URL jednotného přihlašování – informace](./media/yodeck-tutorial/tutorial_yodeck_url.png)

    V **identifikátor (Entity ID)** textového pole zadejte adresu URL: `https://app.yodeck.com/api/v1/account/metadata/`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Yodeck domény a adresy URL jednotného přihlašování – informace](./media/yodeck-tutorial/tutorial_yodeck_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://app.yodeck.com/login`

1. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/yodeck-tutorial/tutorial_yodeck_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/yodeck-tutorial/tutorial_general_400.png)
    
1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Yodeck jako správce.

1. Klikněte na **uživatelská nastavení** formuláře pravém horním rohu stránky a vyberte možnost **nastavení účtu**.

    ![Konfigurace Yodeck](./media/yodeck-tutorial/configure1.png)

1. Vyberte **SAML** a proveďte následující kroky:

    ![Konfigurace Yodeck](./media/yodeck-tutorial/configure2.png)

    a. Vyberte **importovat z adresy URL**.

    b. V **URL** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, kterou jste zkopírovali z webu Azure portal a klikněte na tlačítko **Import**.
    
    c. Po importu **adresa Url federačních metadat aplikace**, automaticky vyplnit zbývající pole.

    d. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/yodeck-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/yodeck-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/yodeck-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/yodeck-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-yodeck-test-user"></a>Vytvoření zkušebního uživatele Yodeck

Přihlaste se k Yodeck Azure AD uživatelům umožnit, musí být poskytnuty do Yodeck.
V případě Yodeck zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti Yodeck jako správce.

1. Klikněte na **uživatelská nastavení** formuláře pravém horním rohu stránky a vyberte možnost **uživatelé**.

    ![Přidat zaměstnance](./media/yodeck-tutorial/user1.png)

1. Klikněte na **+ uživatel** otevřít **podrobnosti o uživateli** kartu.

    ![Přidat zaměstnance](./media/yodeck-tutorial/user2.png)

1. Na **podrobnosti o uživateli** dialogového okna stránky, proveďte následující kroky:

    ![Přidat zaměstnance](./media/yodeck-tutorial/user3.png)

    a. V **křestní jméno** , jako je textové pole, typ křestní jméno uživatele **Britta**.

    b. V **příjmení** , jako je textové pole, typ příjmení uživatele **Simon**.

    c. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele **brittasimon@contoso.com**.

    d. Vyberte odpovídající **oprávnění účtu** možnost podle požadavků vaší organizace.
    
    e. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Yodeck použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200]

**Přiřadit Yodeck Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **Yodeck**.

    ![Odkaz Yodeck v seznamu aplikací](./media/yodeck-tutorial/tutorial_yodeck_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Yodeck na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Yodeck.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yodeck-tutorial/tutorial_general_01.png
[2]: ./media/yodeck-tutorial/tutorial_general_02.png
[3]: ./media/yodeck-tutorial/tutorial_general_03.png
[4]: ./media/yodeck-tutorial/tutorial_general_04.png

[100]: ./media/yodeck-tutorial/tutorial_general_100.png

[200]: ./media/yodeck-tutorial/tutorial_general_200.png
[201]: ./media/yodeck-tutorial/tutorial_general_201.png
[202]: ./media/yodeck-tutorial/tutorial_general_202.png
[203]: ./media/yodeck-tutorial/tutorial_general_203.png

