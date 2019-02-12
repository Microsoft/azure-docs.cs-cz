---
title: 'Kurz: Integrace Azure Active Directory s Envi MMIS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 0eb6e5d45b0e530bbc38a3a420e0419641f43c12
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992105"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Kurz: Integrace Azure Active Directory s Envi MMIS

V tomto kurzu se dozvíte, jak integrovat Envi MMIS s Azure Active Directory (Azure AD).
Envi MMIS integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Envi MMIS.
* Uživatelům se automaticky přihlášeni k Envi MMIS (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Envi MMIS, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Envi MMIS jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Envi MMIS **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-envi-mmis-from-the-gallery"></a>Přidání Envi MMIS z Galerie

Konfigurace integrace Envi MMIS do služby Azure AD, budete muset přidat Envi MMIS z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Envi MMIS z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Envi MMIS**vyberte **Envi MMIS** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Envi MMIS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Envi MMIS podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Envi MMIS.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Envi MMIS, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Envi MMIS Single Sign-On](#configure-envi-mmis-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Envi MMIS](#create-envi-mmis-test-user)**  – Pokud chcete mít protějšek Britta Simon Envi MMIS, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Envi MMIS, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Envi MMIS** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavení jednotného přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Envi MMIS domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Envi MMIS domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory Envi MMIS klienta](mailto:support@ioscorp.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavení jednotného přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavení Envi MMIS** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-envi-mmis-single-sign-on"></a>Konfigurace Envi MMIS jednotného přihlašování

1. V okně jiné webové prohlížeče přihlaste jako správce webu Envi MMIS.

2. Klikněte na **Moje doména** kartu.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure1.png)

3. Klikněte na **Upravit**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure2.png)

4. Vyberte **použít vzdálené ověření** zaškrtávací políčko a potom vyberte **přesměrování protokolu HTTP** z **typ ověřování** rozevíracího seznamu.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure3.png)

5. Vyberte **prostředky** kartu a potom klikněte na tlačítko **nahrát metadat**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure4.png)

6. V **nahrát metadat** automaticky otevírané okno, proveďte následující kroky:

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure5.png)

    a. Vyberte **souboru** možnost **nahrát z** rozevíracího seznamu.

    b. Nahrát soubor metadat stažené z webu Azure portal tak, že vyberete **zvolte ikonu souboru**.

    c. Klikněte na tlačítko **OK**.

7. Po nahrání souboru staženého metadat, budou obsazeny pole, který automaticky. Klikněte na tlačítko **aktualizace**

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Envi MMIS.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Envi MMIS**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Envi MMIS**.

    ![Odkaz Envi MMIS v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-envi-mmis-test-user"></a>Vytvoření Envi MMIS testovacího uživatele

Povolení služby Azure AD uživatelům umožní přihlásit k Envi MMIS, musí být poskytnuty do Envi MMIS. V případě Envi MMIS zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k webu společnosti Envi MMIS jako správce.

2. Klikněte na **seznamu uživatelů** kartu.

    ![Přidat zaměstnance](./media/envimmis-tutorial/user1.png)

3. Klikněte na tlačítko **přidat uživatele** tlačítko.

    ![Přidat zaměstnance](./media/envimmis-tutorial/user2.png)

4. V **přidat uživatele** části, proveďte následující kroky:

    ![Přidat zaměstnance](./media/envimmis-tutorial/user3.png)

    a. V **uživatelské jméno** , jako je textové pole, zadejte uživatelské jméno účtu Britta Simon **brittasimon@contoso.com**.
    
    b. V **křestní jméno** , jako je textové pole, typ křestní jméno BrittaSimon **Britta**.

    c. V **příjmení** , jako je textové pole, typ příjmení BrittaSimon **Simon**.

    d. Zadejte název uživatele v **název** textového pole.
    
    e. V **e-mailovou adresu** , jako je textové pole, typ e-mailovou adresu účtu Britta Simon **brittasimon@contoso.com**.

    f. V **jednotné uživatelské jméno** , jako je textové pole, zadejte uživatelské jméno účtu Britta Simon **brittasimon@contoso.com**.

    g. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Envi MMIS na přístupovém panelu, vám by měl být automaticky přihlášeni ke Envi MMIS, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

