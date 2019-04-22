---
title: 'Kurz: Integrace Azure Active Directory s rozpoznávat | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a rozpoznání.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 94e0b365d159ef18d7c0e6216ac9f5babb0d6231
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59262801"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Kurz: Integrace Azure Active Directory s rozpoznávat

V tomto kurzu se dozvíte, jak můžete rozpoznávat integrovat s Azure Active Directory (Azure AD).
Rozpoznávat integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k rozpoznání.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k rozpoznání (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s rozpoznávat, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Rozpoznat jednotné přihlášení povolený předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Rozpoznat podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-recognize-from-the-gallery"></a>Přidání rozpoznávat z Galerie

Konfigurace integrace rozpoznávat do služby Azure AD, budete muset přidat rozpoznávat v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li rozpoznávat přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **rozpoznávat**vyberte **rozpoznávat** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Rozpoznat v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s rozpoznávat podle testu uživateli **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v rozpoznání.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se rozpoznávat, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurovat rozpoznání Single Sign-On](#configure-recognize-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele rozpoznávat](#create-recognize-test-user)**  – Pokud chcete mít protějšek Britta Simon rozpoznávat, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se rozpoznávat, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **rozpoznávat** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    >[!NOTE]
    >Zobrazí se **soubor metadat poskytovatele služeb** z **konfigurace rozpoznat Single Sign-On** části kurzu.

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Zvolte soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** hodnotu získat automaticky vyplní v části základní konfiguraci SAML.

    ![Rozpoznat domény a adresy URL jednotné přihlašování – informace](common/sp-identifier.png)

     V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Pokud **identifikátor** automaticky vyplní nelze získat hodnotu, zobrazí se hodnota identifikátoru tak, že otevřete adresu URL služby zprostředkovatele metadat z části Nastavení jednotného přihlašování, který je vysvětlen později v **konfigurace rozpoznat jeden Přihlašování** části kurzu. Hodnota přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory rozpoznat klienta](mailto:support@recognizeapp.com) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení rozpoznávat** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-recognize-single-sign-on"></a>Konfigurovat rozpoznání jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k tenantovi rozpoznávat jako správce.

2. V pravém horním rohu klikněte na **nabídky**. Přejděte na **společnosti správce**.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_000.png)

3. V levém navigačním podokně klikněte na tlačítko **nastavení**.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Následující postup proveďte **nastavení jednotného přihlašování** oddílu.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Jako **povolit jednotné přihlašování**vyberte **ON**.

    b. V **IDP Entity ID** textového pole vložte hodnotu **Azure AD identifikátor** zkopírovanou z webu Azure portal.
    
    c. V **jednotného přihlašování k cílové adrese url** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
    
    d. V **cílová adresa url cíle na úrovni služby** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal. 
    
    e. Otevřete váš stažené **certifikát (Base64)** v poznámkovém bloku soubor, zkopírujte obsah ho do schránky a vložte ho do **certifikát** textového pole.
    
    f. Klikněte na tlačítko **uložit nastavení** tlačítko. 

5. Vedle položky **nastavení jednotného přihlašování** tématu, zkopírujte adresu URL v části **adresa url služby zprostředkovatele metadat**.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Otevřít **odkazem na adresu URL metadat** pod prázdnou prohlížeče a stáhněte dokumentu metadat. Potom zkopírujte EntityDescriptor value(entityID) ze souboru a vložte ji **identifikátor** textového pole v **základní konfiguraci SAML** na portálu Azure portal.
    
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k rozpoznání.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **rozpoznávat**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **rozpoznávat**.

    ![Odkaz rozpoznávat v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-recognize-test-user"></a>Vytvoření rozpoznávat testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do rozpoznávat, musí být poskytnuty do rozpoznávat. V případě rozpoznání zřizování se ruční úlohy.

Tato aplikace nepodporuje zřizování SCIM ale má alternativní synchronizace, které zřizuje uživatele. 

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Váš web společnosti rozpoznávat se přihlaste jako správce.

2. V pravém horním rohu klikněte na **nabídky**. Přejděte na **společnosti správce**.

3. V levém navigačním podokně klikněte na tlačítko **nastavení**.

4. Následující postup proveďte **synchronizace uživatelů** oddílu.
   
    ![Nový uživatel](./media/recognize-tutorial/tutorial_recognize_005.png "nového uživatele")
   
    a. Jako **s povolenou synchronizací**vyberte **ON**.
   
    b. Jako **zprostředkovatele synchronizace zvolit**vyberte **Microsoft / Office 365**.
   
    c. Klikněte na tlačítko **spuštění synchronizace uživatelů**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici rozpoznávat na přístupovém panelu, můžete by měl být automaticky přihlášeni k rozpoznání, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

