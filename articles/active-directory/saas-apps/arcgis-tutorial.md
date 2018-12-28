---
title: 'Kurz: Integrace Azure Active Directory s ArcGIS Online | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: fd850cd39522d49cdce955712eaaa76d65279af8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788748"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Kurz: Integrace Azure Active Directory s ArcGIS Online

V tomto kurzu se dozvíte, jak integrovat ArcGIS Online se službou Azure Active Directory (Azure AD).
Integrace ArcGIS Online s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ArcGIS Online.
* Uživatelům se automaticky přihlášeni k ArcGIS Online (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ArcGIS Online, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* ArcGIS Online jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* ArcGIS Online podporuje **SP** jednotné přihlašování zahájené pomocí





## <a name="adding-arcgis-online-from-the-gallery"></a>Přidání ArcGIS Online z Galerie

Pokud chcete nakonfigurovat integraci ArcGIS Online do služby Azure AD, budete muset přidat ArcGIS Online z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ArcGIS Online z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ArcGIS Online**vyberte **ArcGIS Online** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![ArcGIS Online v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s ArcGIS Online na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské ArcGIS online.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ArcGIS Online, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace ArcGIS Online Single Sign-On](#configure-arcgis-online-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvořit testovacího uživatele ArcGIS Online](#create-arcgis-online-test-user)**  – Pokud chcete mít protějšek Britta Simon ArcGIS online, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ArcGIS Online, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **ArcGIS Online** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![ArcGIS Online domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.maps.arcgis.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory ArcGIS Online klienta](https://support.esri.com/en/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. K automatizaci konfigurace v rámci **ArcGIS Online**, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Po přidání rozšíření do prohlížeče, klikněte na **nastavení ArcGIS Online** nasměruje na ArcGIS Online aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do ArcGIS Online. Rozšíření prohlížeče automaticky nakonfiguruje aplikaci za vás a Automatizace kroků v části **konfigurace ArcGIS Online Single Sign-On**.

### <a name="configure-arcgis-online-single-sign-on"></a>Konfigurace ArcGIS Online jednotného přihlašování

1. Pokud chcete nastavit ArcGIS Online ručně, otevřete nové okno webového prohlížeče a přihlaste se jako správce ArcGIS společnosti lokalitu a proveďte následující kroky:

2. Klikněte na tlačítko **upravovat nastavení**.

    ![Upravit nastavení](./media/arcgis-tutorial/ic784742.png "upravit nastavení")

3. Klikněte na tlačítko **zabezpečení**.

    ![Zabezpečení](./media/arcgis-tutorial/ic784743.png "zabezpečení")

4. V části **podnikové přihlašovací údaje**, klikněte na tlačítko **nastavit zprostředkovatele IDENTITY**.

    ![Podnikové přihlašovací údaje](./media/arcgis-tutorial/ic784744.png "podnikové přihlašovací údaje")

5. Na **nastavit zprostředkovatele Identity** konfigurační stránce, proveďte následující kroky:

    ![Nastavit zprostředkovatele Identity](./media/arcgis-tutorial/ic784745.png "nastavit zprostředkovatele Identity")

    a. V **název** textového pole zadejte název vaší organizace.

    b. Pro **Metadata pro zprostředkovatele Identity organizace bude zadán pomocí**vyberte **soubor**.

    c. Nahrát soubor stažený metadat, klikněte na tlačítko **zvolte soubor**.

    d. Klikněte na tlačítko **zprostředkovatele IDENTITY sada**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k ArcGIS Online.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ArcGIS Online**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **ArcGIS Online**.

    ![ArcGIS Online odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-arcgis-online-test-user"></a>Vytvoření ArcGIS Online testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do ArcGIS Online, musí být poskytnuty do ArcGIS Online.  
V případě ArcGIS Online zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **ArcGIS** tenanta.

2. Klikněte na tlačítko **pozvání členů**.
   
    ![Pozvání členů](./media/arcgis-tutorial/ic784747.png "zvát členy")

3. Vyberte **automaticky přidat členy bez odeslání e-mailu**a potom klikněte na tlačítko **Další**.
   
    ![Automaticky přidat členy](./media/arcgis-tutorial/ic784748.png "automaticky přidat členy")

4. Na **členy** dialogového okna stránky, proveďte následující kroky:
   
     ![Přidat a zkontrolujte](./media/arcgis-tutorial/ic784749.png "přidat a revize")
    
     a. Zadejte **e-mailu**, **křestní jméno**, a **příjmení** platného účtu AAD, které chcete zřídit.
  
     b. Klikněte na tlačítko **přidat a kontrola**.
5. Zkontrolujte data, která jste zadali a potom klikněte na tlačítko **přidat členy**.
   
    ![Přidat člena](./media/arcgis-tutorial/ic784750.png "přidat člena")
        
    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici ArcGIS Online na přístupovém panelu, můžete by měl být automaticky přihlášeni u kterého nastavíte jednotné přihlašování online ArcGIS. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

