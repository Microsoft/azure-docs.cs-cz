---
title: 'Kurz: Integrace Azure Active Directory s Freedcamp | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39aeaa3edd3682272d63f6edca1dd2a341c00ba5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927055"
---
# <a name="tutorial-azure-active-directory-integration-with-freedcamp"></a>Kurz: Integrace Azure Active Directory s Freedcamp

V tomto kurzu se dozvíte, jak integrovat Freedcamp s Azure Active Directory (Azure AD).
Freedcamp integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Freedcamp.
* Můžete povolit uživatelům být automaticky přihlášeni k Freedcamp (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Freedcamp, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Freedcamp jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Freedcamp **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-freedcamp-from-the-gallery"></a>Přidání Freedcamp z Galerie

Konfigurace integrace Freedcamp do služby Azure AD, budete muset přidat Freedcamp z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Freedcamp z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Freedcamp**vyberte **Freedcamp** z panelu výsledek a pak klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Freedcamp v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Freedcamp podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Freedcamp.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Freedcamp, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Freedcamp Single Sign-On](#configure-freedcamp-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Freedcamp](#create-freedcamp-test-user)**  – Pokud chcete mít protějšek Britta Simon Freedcamp, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Freedcamp, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Freedcamp** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na tlačítko **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Freedcamp domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Freedcamp domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Uživatele můžete také zadat hodnoty adresy url s ohledem na své vlastní domény, zákazníků a nemusí být nutně vzoru `freedcamp.com`, mohou zadat jakékoli domény konkrétní hodnotu zákazníka, specifické pro jejich instance aplikace. Můžete také kontaktovat [tým podpory Freedcamp klienta](mailto:devops@freedcamp.com) pro další informace o vzorech adres url.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Freedcamp** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-freedcamp-single-sign-on"></a>Konfigurace Freedcamp jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k Freedcamp jako správce zabezpečení.

2. V pravém horním rohu stránky, klikněte na **profilu** a přejděte na **Můj účet**.

    ![Konfigurace Freedcamp](./media/freedcamp-tutorial/config01.png)

3. V levé části řádku nabídek klikněte na **jednotného přihlašování** a na **si jednotné přihlašování připojení** stránku, proveďte následující kroky:

    ![Konfigurace Freedcamp](./media/freedcamp-tutorial/config02.png)

    a. V **název** textové pole, zadejte název.

    b. V **Entity ID** textového pole, Vložit **Azure AD identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    c. V **přihlašovací adresa URL** textového pole, Vložit **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    d. V poznámkovém bloku otevřete certifikát kódovaný v Base64, zkopírujte jeho obsah a vložte ho do **certifikát** textového pole.

    e. Klikněte na **Submit** (Odeslat).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Freedcamp použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Freedcamp**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Freedcamp**.

    ![Odkaz Freedcamp v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-freedcamp-test-user"></a>Vytvoření Freedcamp testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Freedcamp, musí být poskytnuty do Freedcamp. Zřizování v Freedcamp, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. V okně jiné webové prohlížeče Přihlaste se k Freedcamp jako správce zabezpečení.

2. V horní části toright horním rohu stránky, klikněte na **profilu** a přejděte na **spravovat systém**.

    ![Konfigurace Freedcamp](./media/freedcamp-tutorial/config03.png)

3. Na pravé straně stránky Správa systému proveďte následující kroky:

    ![Konfigurace Freedcamp](./media/freedcamp-tutorial/config04.png)

    a. Klikněte na **přidat nebo pozvání uživatelů**.

    b. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je `Brittasimon@contoso.com`.

    c. Klikněte na tlačítko **přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Freedcamp na přístupovém panelu, můžete by měl být automaticky přihlášeni k Freedcamp, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

