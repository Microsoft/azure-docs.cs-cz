---
title: 'Kurz: Integrace Azure Active Directory s Brightidea | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Brightidea.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3adae3e0-f43b-492f-b373-6a512d2d6046
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.openlocfilehash: d6138925c64ddcbd7732e52b7cff1459c5bdc7d9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55479186"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Kurz: Integrace Azure Active Directory s Brightidea

V tomto kurzu se dozvíte, jak integrovat Brightidea s Azure Active Directory (Azure AD).
Brightidea integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Brightidea.
* Můžete povolit uživatelům být automaticky přihlášeni k Brightidea (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Brightidea, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Brightidea jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.


* Podporuje Brightidea **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí
* Podporuje Brightidea **JIT** zřizování uživatelů


## <a name="adding-brightidea-from-the-gallery"></a>Přidání Brightidea z Galerie

Konfigurace integrace Brightidea do služby Azure AD, budete muset přidat Brightidea z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Brightidea z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Brightidea**vyberte **Brightidea** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Brightidea v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Brightidea podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Brightidea.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Brightidea, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Brightidea Single Sign-On](#configure-brightidea-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Brightidea](#create-brightidea-test-user)**  – Pokud chcete mít protějšek Britta Simon Brightidea, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Brightidea, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Brightidea** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb** a chcete nakonfigurovat v **IDP** intiated režimu proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Zvolte soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v textovém poli Brightidea části:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Pokud **identifikátor** a **adresy URL odpovědi** hodnoty nechcete získat polulated automaticky, a potom zadejte hodnoty ručně podle vašich požadavků.

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![image](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<SUBDOMAIN>.brightidea.com`

4. Na **nastavení jednotného přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení Brightidea** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-brightidea-single-sign-on"></a>Konfigurace Brightidea jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k Brightidea pomocí přihlašovacích údajů správce.

2. Získejte k funkci jednotného přihlašování služby Brightidea, přejděte na **podnikové instalace** -> **kartu ověřování**. Zde se zobrazí dvě dílčí karty: Výběr ověřování & profily SAML.

    ![Konfigurace Brightidea](./media/brightidea-tutorial/configure1.png)

3. Vyberte **Auth výběr**. Ve výchozím nastavení zobrazí pouze dva standardní metody: Brightidea přihlášení a registraci. Když metodu jednotného přihlašování přidali, se zobrazí v seznamu.

    ![Konfigurace Brightidea](./media/brightidea-tutorial/configure2.png)

4. Vyberte **SAML profily** a proveďte následující kroky:

    ![Konfigurace Brightidea](./media/brightidea-tutorial/configure3.png)

    a. Klikněte na **stáhnout Metadata** a nahrajte na **základní konfiguraci SAML** části webu Azure Portal.

    b. Klikněte na **přidat nový** tlačítko **nastavení poskytovatele Identity** a proveďte následující kroky:
    
    ![Konfigurace Brightidea](./media/brightidea-tutorial/configure4.png)
    
     * Zadejte **název profilu SAML** jako je např. `Azure Ad SSO`
    
     * Pro **nahrát metadat**, klikněte na tlačítko Vybrat soubor a nahrát soubor metadat stažené z webu Azure portal.

     > [!NOTE]
     > Po nahrání souboru metadat, zbývající pole **jednotné přihlašování, Vystavitel zprostředkovatele Identity, nahrát veřejný klíč služby** se vyplní automaticky.

     * V **e-mailu** textového pole zadejte hodnotu jako `mail`.
     
     * V **název obrazovky** textového pole zadejte hodnotu jako `givenName`.
     
     * Klikněte na tlačítko **uložit změny**.  

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

V této části je povolit Britta Simon k udělení přístupu k Brightidea použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Brightidea**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Brightidea**.

    ![Odkaz Brightidea v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-brightidea-test-user"></a>Vytvoření Brightidea testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Brightidea. Brightidea podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Brightidea, vytvoří se nový po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Brightidea na přístupovém panelu, můžete by měl být automaticky přihlášeni k Brightidea, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

