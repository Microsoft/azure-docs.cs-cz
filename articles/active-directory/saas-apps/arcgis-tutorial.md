---
title: 'Kurz: Azure Active Directory integrace s ArcGIS Online | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74cf0c1e055570df7702465df79dcdfbd8ea9e9f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232090"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Kurz: Azure Active Directory integrace s ArcGIS Online

V tomto kurzu se naučíte integrovat ArcGIS Online s Azure Active Directory (Azure AD).
Integrace ArcGISe online se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k ArcGIS Online.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k ArcGIS Online (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s ArcGIS Online potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné povoleného jednotného přihlašování ArcGIS Online

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ArcGIS Online podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-arcgis-online-from-the-gallery"></a>Přidání ArcGIS do online režimu z Galerie

Pokud chcete nakonfigurovat integraci ArcGIS Online do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat ArcGIS Online z galerie.

**Pokud chcete přidat ArcGIS Online z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ArcGIS Online**, vyberte **ArcGIS Online** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![ArcGIS Online v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ArcGIS Online na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ArcGISu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby ArcGIS Online, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace jednotného přihlašování ArcGIS Online](#configure-arcgis-online-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte online testovacího uživatele ArcGIS](#create-arcgis-online-test-user)** , abyste měli protějšek Britta Simon v ArcGIS Online, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ArcGIS Online, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **online aplikací ArcGIS** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![ArcGIS Online domény a adresy URL jednotného přihlašování](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.maps.arcgis.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory online klientů ArcGIS](https://support.esri.com/en/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Pokud chcete automatizovat konfiguraci v rámci služby **ArcGIS Online**, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Po přidání rozšíření do prohlížeče klikněte na **Nastavení ArcGIS Online** a přesměrujte vás na online aplikaci ArcGIS. Odtud zadejte přihlašovací údaje správce pro přihlášení k ArcGIS Online. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky v části **Konfigurace jednotného přihlašování ArcGIS Online**.

### <a name="configure-arcgis-online-single-sign-on"></a>Konfigurace jednotného přihlašování v ArcGIS Online

1. Pokud chcete nastavit ArcGIS Online ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu ArcGIS společnosti jako správce a proveďte následující kroky:

2. Klikněte na **Upravit nastavení**.

    ![Upravit nastavení](./media/arcgis-tutorial/ic784742.png "Upravit nastavení")

3. Klikněte na **zabezpečení**.

    ![Zabezpečení](./media/arcgis-tutorial/ic784743.png "Zabezpečení")

4. V části **Podniková přihlášení**klikněte na **nastavit poskytovatele identity**.

    ![Podniková přihlášení](./media/arcgis-tutorial/ic784744.png "Podniková přihlášení")

5. Na stránce **nastavit konfiguraci zprostředkovatele identity** proveďte následující kroky:

    ![Nastavit zprostředkovatele identity](./media/arcgis-tutorial/ic784745.png "Nastavit zprostředkovatele identity")

    a. Do textového pole **název** zadejte název vaší organizace.

    b. **Metadata pro podnikového zprostředkovatele identity se doplní pomocí**, vyberte **soubor**.

    c. Pokud chcete nahrát stažený soubor metadat, klikněte na **zvolit soubor**.

    d. Klikněte na **nastavit poskytovatele identity**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ArcGIS Online.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **ArcGIS Online**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **ArcGIS Online**.

    ![Online odkaz ArcGIS v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-arcgis-online-test-user"></a>Vytvořit online testovacího uživatele ArcGIS

Aby se uživatelé Azure AD mohli přihlásit k ArcGIS Online, musí se zřídit do ArcGIS Online.  
V případě ArcGIS Online je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **ArcGIS** .

2. Klikněte na **pozvat členy**.
   
    ![Pozvat členy](./media/arcgis-tutorial/ic784747.png "Pozvat členy")

3. Vyberte možnost **přidat členy automaticky bez odeslání e-mailu**a potom klikněte na tlačítko **Další**.
   
    ![Přidat členy automaticky](./media/arcgis-tutorial/ic784748.png "Přidat členy automaticky")

4. Na stránce dialog **Členové** proveďte následující kroky:
   
     ![Přidat a zkontrolovat](./media/arcgis-tutorial/ic784749.png "Přidat a zkontrolovat")
    
     a. Zadejte **e-mail**, **jméno**a **příjmení** platného účtu Azure AD, který chcete zřídit.
  
     b. Klikněte na **Přidat a zkontrolovat**.
5. Zkontrolujte data, která jste zadali, a pak klikněte na **přidat členy**.
   
    ![Přidat člena](./media/arcgis-tutorial/ic784750.png "Přidat člena")
        
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz pro potvrzení, že účet ještě nebude aktivní.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici ArcGIS Online, měli byste se automaticky přihlásit k ArcGIS Online, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

