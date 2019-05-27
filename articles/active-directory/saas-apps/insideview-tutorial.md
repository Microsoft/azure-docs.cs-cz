---
title: 'Kurz: Integrace Azure Active Directory s InsideView | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 0fdabd237fa128326673d84e889387d03f184b00
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236579"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Kurz: Integrace Azure Active Directory s InsideView

V tomto kurzu se dozvíte, jak integrovat InsideView s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Můžete řídit, kdo má přístup k InsideView Azure AD.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k InsideView (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s InsideView, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné InsideView obsahující single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* InsideView podporuje jednotné přihlašování zahájené pomocí IdP.

## <a name="add-insideview-from-the-gallery"></a>Přidání InsideView z Galerie

Nastavení integrace InsideView do služby Azure AD, budete muset přidat InsideView z Galerie na váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **všechny aplikace**:

    ![Okno aplikace organizace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

    ![Vyberte novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **InsideView**. Vyberte **InsideView** ve výsledcích hledání a pak vyberte **přidat**.

    ![Výsledky vyhledávání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a Azure AD jednotné přihlašování s InsideView test pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, budete muset vytvořit vztah mezi uživatele služby Azure AD a odpovídajícího uživatele v InsideView.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s InsideView, které potřebujete k dokončení těchto kroků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  k povolení této funkce pro vaše uživatele.
2. **[Konfigurace InsideView jednotného přihlašování](#configure-insideview-single-sign-on)**  na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotného přihlašování.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolení služby Azure AD jednotného přihlašování pro uživatele.
5. **[Vytvořit testovacího uživatele InsideView](#create-an-insideview-test-user)**  připojený k Azure AD zastoupení uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části budete povolení služby Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s InsideView, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com/), vyberte na stránce InsideView integrace aplikací, **jednotného přihlašování**:

    ![Vyberte jednotného přihlašování](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno:

    ![Upravit ikonu](common/edit-urls.png)

4. V **základní konfiguraci SAML** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno základní konfigurace SAML](common/idp-reply.png)

    V **adresy URL odpovědi** pole, zadejte adresu URL v tomto vzoru:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Tato hodnota je zástupný symbol. Budete muset použít adresy URL skutečné odpovědi. Obraťte se [tým podpory InsideView](mailto:support@insideview.com) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** dialogové okno na webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** odkaz **certifikát (Raw)** , podle požadavků vaší a uložte certifikát v počítači:

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. V **nastavení InsideView** tématu, zkopírujte příslušné adresy URL, na základě vašich požadavků:

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    1. **Adresa URL pro přihlášení**.

    1. **Identifikátor služby Azure AD**.

    1. **Odhlašovací adresa URL**.

### <a name="configure-insideview-single-sign-on"></a>Konfigurace InsideView jednotného přihlašování

1. V novém okně webového prohlížeče Přihlaste se k webu společnosti InsideView jako správce.

1. V horní části okna vyberte **správce**, **SingleSignOn nastavení**a potom **přidat SAML**.
   
   ![Jednotné přihlašování – nastavení SAML](./media/insideview-tutorial/ic794135.png "SAML jednotné přihlašování – nastavení")

1. V **přidat nové SAML** části, proveďte následující kroky.

    ![Přidejte novou SAML část](./media/insideview-tutorial/ic794136.png "přidat oddíl nové SAML")

    1. V **název služby tokenů zabezpečení** pole, zadejte název pro vaši konfiguraci.

    1. V **nevyžádané koncový bod SamlP/WS-Fed** pole, vložte **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. Otevřete nezpracovaná certifikát, který jste si stáhli z webu Azure portal. Zkopírujte obsah certifikátu do schránky a vložte obsah do **certifikátu STS** pole.

    1. V **mapování Id uživatele Crm** zadejte **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    1. V **Crm e-mailu mapování** zadejte **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    1. V **Crm křestní jméno mapování** zadejte **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    1. V **Crm lastName mapování** zadejte **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.  

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** v levém podokně vyberte **uživatelé**a pak vyberte **všichni uživatelé**:

    ![Vyberte možnost Všichni uživatelé](common/users.png)

2. Vyberte **nového uživatele** v horní části okna:

    ![Vyberte nového uživatele](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatelského](common/user-properties.png)

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **BrittaSimon @\<doména_společnosti >.\< Rozšíření >** . (Například BrittaSimon@contoso.com.)

    1. Vyberte **zobrazit heslo**a zapište si hodnotu, která je v **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části se budou moci používat jednotné přihlašování Azure tím, že udělíte přístup k InsideView Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **InsideView**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **InsideView**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **uživatelů a skupin**:

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části okna.

6. Pokud očekáváte, že hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte na tlačítko **vyberte** tlačítko v dolní části okna.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-an-insideview-test-user"></a>Vytvořit testovacího uživatele InsideView

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k InsideView, budete muset přidat je do InsideView. Musíte je přidat ručně.

Vytvoření uživatele nebo kontakty v InsideView, obraťte se [tým podpory InsideView](mailto:support@insideview.com).

> [!NOTE]
> Můžete použít libovolný nástroj pro vytváření účtu uživatele nebo rozhraní API poskytovaných InsideView zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Teď je potřeba otestovat vaši konfiguraci Azure AD jednotné přihlašování pomocí přístupového panelu.

Při výběru dlaždice InsideView na přístupovém panelu, vám by měl být automaticky přihlášeni InsideView instanci, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
