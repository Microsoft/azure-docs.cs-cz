---
title: 'Kurz: Integrace Azure Active Directory s Zoho | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.openlocfilehash: bae52a16a73048355a327408bfb58b04cb5e4a55
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976694"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Kurz: Integrace Azure Active Directory s Zoho

V tomto kurzu se dozvíte, jak integrovat Zoho s Azure Active Directory (Azure AD).
Zoho integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zoho.
* Můžete povolit uživatelům být automaticky přihlášeni k Zoho (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zoho, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Zoho jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Zoho **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-zoho-from-the-gallery"></a>Přidání Zoho z Galerie

Konfigurace integrace Zoho do služby Azure AD, budete muset přidat Zoho z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zoho z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zoho**vyberte **Zoho** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Zoho v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Zoho podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zoho.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zoho, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Zoho Single Sign-On](#configure-zoho-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Zoho](#create-zoho-test-user)**  – Pokud chcete mít protějšek Britta Simon Zoho, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Zoho, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Zoho** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Zoho domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<company name>.zohomail.com`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Zoho klienta](https://www.zoho.com/mail/contact.html) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Zoho** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-zoho-single-sign-on"></a>Konfigurace Zoho jednotné přihlašování

1. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Zoho e-mailu.

2. Přejděte **ovládací panely**.
   
    ![Ovládací panely](./media/zoho-mail-tutorial/ic789607.png "ovládací panely")

3. Klikněte na tlačítko **ověřování SAML** kartu.
   
    ![Ověřování SAML](./media/zoho-mail-tutorial/ic789608.png "ověřování SAML")

4. V **podrobnosti o ověřování SAML** části, proveďte následující kroky:
   
    ![Podrobnosti ověřování SAML](./media/zoho-mail-tutorial/ic789609.png "podrobnosti o ověřování SAML")
   
    a. V **přihlašovací adresa URL** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
   
    b. V **odhlašovací adresa URL** vložit do textového pole **odhlašovací adresa URL** zkopírovanou z webu Azure portal.
   
    c. V **heslo změnit adresu URL** vložit do textového pole **heslo změnit adresu URL** zkopírovanou z webu Azure portal.
       
    d. Otevření certifikátu kódováním base-64 stáhnout z webu Azure portal v programu Poznámkový blok, zkopírujte obsah ho do schránky a a vložte ho do **PublicKey** textového pole.
   
    e. Jako **algoritmus**vyberte **RSA**.
   
    f. Klikněte na **OK**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Zoho použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Zoho**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Zoho**.

    ![Zoho odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-zoho-test-user"></a>Vytvoření Zoho testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do e-mailu Zoho, musí být poskytnuty do Zoho e-mailu. V případě Zoho e-mailu zřizování se ruční úlohy.

> [!NOTE]
> Můžete použít jakékoli jiné e-mailu Zoho uživatele účtu nástrojů pro vytváření nebo rozhraní API k dispozici prostřednictvím e-mailu Zoho uživatelským účtům, zřídit AAD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, postupujte následovně:

1. Přihlaste se k vaší **e-mailu Zoho** společnosti serveru jako správce.

1. Přejděte na **ovládací panely \> e-mailu a dokumentace**.

1. Přejděte na **podrobnosti o uživateli \> přidat uživatele**.
   
    ![Přidání uživatele](./media/zoho-mail-tutorial/ic789611.png "přidat uživatele")

1. Na **přidat uživatele** dialogového okna, proveďte následující kroky:
   
    ![Přidání uživatele](./media/zoho-mail-tutorial/ic789612.png "přidat uživatele")
   
    a. V **křestní jméno** , jako je textové pole, typ křestní jméno uživatele **Britta**.

    b. V **příjmení** , jako je textové pole, typ příjmení uživatele **Simon**.

    c. V **ID e-mailu** , jako je textové pole, typ id e-mailu uživatele **brittasimon@contoso.com**.

    d. V **heslo** textového pole zadejte heslo uživatele.
   
    e. Klikněte na **OK**.  
      
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail s odkazem pro potvrzení účtu, pak se změní na aktivní.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zoho na přístupovém panelu, můžete by měl být automaticky přihlášeni k Zoho, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

