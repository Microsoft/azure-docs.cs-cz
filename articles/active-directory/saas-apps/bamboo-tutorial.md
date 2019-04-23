---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro bambus podle rozlišení GmbH | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML pro bambus podle rozlišení GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c13469c9c70aec31314048b8971c66bae5cd49
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004716"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro bambus podle rozlišení GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování SAML pro bambus podle rozlišení GmbH se službou Azure Active Directory (Azure AD).
Integrace jednotného přihlašování SAML pro bambus podle rozlišení GmbH s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SAML SSO pro bambus podle rozlišení GmbH.
* Můžete povolit uživatelům být automaticky přihlášeni na jednotné přihlašování SAML pro bambus podle rozlišení GmbH (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním SAML pro bambus s rozlišení GmbH, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Jednotné přihlašování SAML pro bambus podle rozlišení GmbH jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Jednotné přihlašování SAML pro bambus tak řešení podporuje GmbH **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí
* Jednotné přihlašování SAML pro bambus tak řešení podporuje GmbH **JIT** zřizování uživatelů

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Přidání jednotného přihlašování SAML pro bambus podle rozlišení GmbH z Galerie

Pokud chcete nakonfigurovat integraci jednotného přihlašování SAML pro bambus podle rozlišení GmbH do služby Azure AD, budete muset přidat jednotné přihlašování SAML pro bambus rozlišení GmbH z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání jednotného přihlašování SAML pro bambus podle rozlišení GmbH z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **jednotné přihlašování SAML pro bambus podle rozlišení GmbH**vyberte **jednotné přihlašování SAML pro bambus podle rozlišení GmbH** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Jednotné přihlašování SAML pro bambus podle rozlišení GmbH v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML bambus podle rozlišení GmbH podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v jednotné přihlašování SAML pro bambus podle rozlišení GmbH musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro bambus podle rozlišení GmbH, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurovat jednotné přihlašování SAML pro bambus rozlišení GmbH Single Sign-On](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit jednotné přihlašování SAML pro bambus rozlišení GmbH testovací uživatel](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**  – Pokud chcete mít protějšek Britta Simon v jednotné přihlašování SAML pro bambus podle rozlišení GmbH, která souvisí s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro bambus podle rozlišení GmbH, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **jednotné přihlašování SAML pro bambus podle rozlišení GmbH** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Jednotné přihlašování SAML pro bambus podle rozlišení GmbH domény a adresy URL jednotné přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Jednotné přihlašování SAML pro bambus podle rozlišení GmbH domény a adresy URL jednotné přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory jednotného přihlašování SAML pro bambus podle rozlišení GmbH klienta](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavit jednotné přihlašování SAML pro bambus rozlišení GmbH** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Nakonfigurovat jednotné přihlašování SAML pro bambus rozlišení GmbH jednotného přihlašování

1. Přihlašování k SAML SSO pro bambus rozlišení GmbH společnosti lokalita jako správce.

1. Na pravé straně hlavního panelu nástrojů klikněte na tlačítko **nastavení** > **doplňky**.

    ![Nastavení](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Přejděte do části pro zabezpečení, klikněte na **SAML SingleSignOn** na řádku nabídek.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Na **stránka Konfigurace modulu plug-in SIngleSignOn SAML**, klikněte na tlačítko **přidat zprostředkovatele identity**.

    ![Přidání zprostředkovatele identity](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Na **zvolte zprostředkovatele Identity SAML** stránku, proveďte následující kroky:

    ![Zprostředkovatel identity](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Vyberte **typ zprostředkovatele identity** jako **AZURE AD**.

    b. V **název** textového pole zadejte název.

    c. V **popis** textového pole zadejte popis.

    d. Klikněte na **Další**.

1. Na **konfigurace zprostředkovatele Identity** stránce klikněte na **Další**.

    ![Konfigurace identity](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Na **Import metadat zprostředkovatele identity SAML** klikněte na stránce **načíst soubor** nahrát **soubor XML s METADATY** soubor, který jste si stáhli z webu Azure portal.

    ![Idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Klikněte na **Další**.

1. Klikněte na **Save settings** (Uložit nastavení).

    ![Uložení](./media/bamboo-tutorial/tutorial_bamboo_save.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup pro jednotné přihlašování SAML pro bambus podle rozlišení GmbH.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **jednotné přihlašování SAML pro bambus podle rozlišení GmbH**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **jednotné přihlašování SAML pro bambus podle rozlišení GmbH**.

    ![Jednotné přihlašování SAML pro bambus podle propojení GmbH řešení v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Vytvořit jednotné přihlašování SAML pro bambus podle rozlišení GmbH testovacího uživatele

Cílem této části je pro vytvoření uživatele volány Britta Simon v SAML SSO pro bambus rozlišení GmbH. Jednotné přihlašování SAML bambus podle rozlišení GmbH podporuje zřizování v čase a také uživatelům lze vytvořit ručně, obraťte se na [tým podpory jednotného přihlašování SAML pro bambus podle rozlišení GmbH klienta](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) podle vašich požadavků.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na jednotné přihlašování SAML pro bambus podle rozlišení GmbH dlaždici na přístupovém panelu, by měl být automaticky přihlásíte na jednotné přihlašování SAML pro bambus podle rozlišení GmbH, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
