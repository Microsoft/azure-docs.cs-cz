---
title: 'Kurz: Integrace Azure Active Directory s Proxyclick | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093496"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Kurz: Integrace Azure Active Directory s Proxyclick

V tomto kurzu se dozvíte, jak integrovat Proxyclick s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Můžete řídit, kdo má přístup k Proxyclick Azure AD.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Proxyclick (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Proxyclick, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete si zaregistrovat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Proxyclick předplatné, které obsahuje single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* Proxyclick podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování a zahájené pomocí IdP.

## <a name="add-proxyclick-from-the-gallery"></a>Přidání Proxyclick z Galerie

Nastavení integrace Proxyclick do služby Azure AD, budete muset přidat Proxyclick z Galerie na váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **všechny aplikace**:

    ![Okno aplikace organizace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

    ![Vyberte novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Proxyclick**. Vyberte **Proxyclick** ve výsledcích hledání a pak vyberte **přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a Azure AD jednotné přihlašování s Proxyclick test pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, budete muset vytvořit vztah mezi uživatele služby Azure AD a odpovídajícího uživatele v Proxyclick.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Proxyclick, které potřebujete k dokončení těchto kroků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  k povolení této funkce pro vaše uživatele.
2. **[Konfigurace Proxyclick jednotného přihlašování](#configure-proxyclick-single-sign-on)**  na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotného přihlašování.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolení služby Azure AD jednotného přihlašování pro uživatele.
5. **[Vytvoření zkušebního uživatele Proxyclick](#create-a-proxyclick-test-user)**  připojený k Azure AD zastoupení uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části budete povolení služby Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Proxyclick, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com/), vyberte na stránce Proxyclick integrace aplikací, **jednotného přihlašování**:

    ![Vyberte jednotného přihlašování](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování:

    ![Vyberte metodu jednotné přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno:

    ![Upravit ikonu](common/edit-urls.png)

4. V **základní konfiguraci SAML** dialogové okno, pokud chcete nakonfigurovat aplikace v režimu zahájené pomocí IdP, proveďte následující kroky.

    ![Dialogové okno základní konfigurace SAML](common/idp-intiated.png)

    1. V **identifikátor** pole, zadejte adresu URL v tomto vzoru:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. V **adresy URL odpovědi** pole, zadejte adresu URL v tomto vzoru:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Pokud chcete nakonfigurovat aplikace v režimu iniciovaného Zprostředkovatelem přihlašování, vyberte **nastavit další adresy URL**. V **přihlašovací adresa URL** pole, zadejte adresu URL v tomto vzoru:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Budete muset použít skutečné identifikátor, adresa URL pro odpověď a adresu URL přihlašování. Postup, jak získat tyto hodnoty jsou popsány dále v tomto kurzu.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** odkaz **certifikát (Base64)** , podle požadavků vaší a uložte certifikát v počítači:

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V **nastavení Proxyclick** tématu, zkopírujte příslušné adresy URL, na základě vašich požadavků:

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    1. **Adresa URL pro přihlášení**.

    1. **Identifikátor služby Azure AD**.

    1. **Odhlašovací adresa URL**.

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurace Proxyclick jednotného přihlašování

1. V novém okně webového prohlížeče Přihlaste se k webu společnosti Proxyclick jako správce.

2. Vyberte **účet a nastavení**:

    ![Vyberte účet a nastavení](./media/proxyclick-tutorial/configure1.png)

3. Přejděte dolů k položce **integrace** a vyberte **SAML**:

    ![Vyberte SAML](./media/proxyclick-tutorial/configure2.png)

4. V **SAML** části, proveďte následující kroky.

    ![Část SAML](./media/proxyclick-tutorial/configure3.png)

    1. Kopírovat **adresu URL příjemce SAML** hodnotu a vložte ho do **adresy URL odpovědi** pole **základní konfiguraci SAML** dialogové okno na webu Azure Portal.

    1. Kopírovat **přesměrovat adresu URL jednotného přihlašování SAML** hodnotu a vložte ho do **přihlašovací adresa URL** a **identifikátor** polích v **základní konfiguraci SAML** Dialogové okno na webu Azure Portal.

    1. V **metoda požadavku SAML** seznamu vyberte **přesměrování protokolu HTTP**.

    1. V **vystavitele** pole, vložte **Azure AD identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. V **URL koncového bodu služby SAML 2.0** pole, vložte **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. V poznámkovém bloku otevřete soubor certifikátu, který jste si stáhli z webu Azure portal. Vložte obsah tohoto souboru do **certifikát** pole.

    1. Vyberte **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** v levém podokně vyberte **uživatelé**a pak vyberte **všichni uživatelé**:

    ![Vyberte možnost Všichni uživatelé](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky:

    ![Vyberte nového uživatele](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatelského](common/user-properties.png)

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **BrittaSimon @\<doména_společnosti >.\< Rozšíření >** . (Například BrittaSimon@contoso.com.)

    1. Vyberte **zobrazit heslo**a zapište si hodnotu, která je v **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části se budou moci používat jednotné přihlašování Azure tím, že udělíte přístup k Proxyclick Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Proxyclick**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Proxyclick**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **uživatelů a skupin**:

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části okna.

6. Pokud očekáváte, že hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte na tlačítko **vyberte** tlačítko v dolní části okna.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-a-proxyclick-test-user"></a>Vytvoření zkušebního uživatele Proxyclick

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Proxyclick, budete muset přidat je do Proxyclick. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, proveďte tyto kroky:

1. Přihlaste se k webu společnosti Proxyclick jako správce.

1. Vyberte **kolegy** v horní části okna:

    ![Vyberte vaši kolegové](./media/proxyclick-tutorial/user1.png)

1. Vyberte **Přidat kolegy**:

    ![Výběr možnosti Přidat kolegy](./media/proxyclick-tutorial/user2.png)

1. V **přidat kolegu** části, proveďte následující kroky.

    ![Přidat oddíl kolegu](./media/proxyclick-tutorial/user3.png)

    1. V **e-mailu** zadejte e-mailovou adresu uživatele. V takovém případě **brittasimon\@contoso.com**.

    1. V **křestní jméno** zadejte jméno uživatele. V takovém případě **Britta**.

    1. V **příjmení** zadejte příjmení uživatele. V takovém případě **Simon**.

    1. Vyberte **přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat vaši konfiguraci Azure AD jednotné přihlašování pomocí přístupového panelu.

Při výběru dlaždice Proxyclick na přístupovém panelu, vám by měl být automaticky přihlášeni Proxyclick instanci, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

