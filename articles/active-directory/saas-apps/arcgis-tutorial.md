---
title: 'Kurz: Azure Active Directory integrace s ArcGIS Online | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 28e15cb7e0b519932eeee3a6a6445449affe2f35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88551362"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Kurz: Azure Active Directory integrace s ArcGIS Online

V tomto kurzu se naučíte integrovat ArcGIS Online s Azure Active Directory (Azure AD).
Integrace ArcGISe online se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k ArcGIS Online.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k ArcGIS Online (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s ArcGIS Online potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné povoleného jednotného přihlašování ArcGIS Online

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ArcGIS Online podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-arcgis-online-from-the-gallery"></a>Přidání ArcGIS do online režimu z Galerie

Pokud chcete nakonfigurovat integraci ArcGIS Online do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat ArcGIS Online z galerie.

**Pokud chcete přidat ArcGIS Online z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ArcGIS Online**, vyberte **ArcGIS Online** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![ArcGIS Online v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ArcGIS Online na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ArcGISu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby ArcGIS Online, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování ArcGIS Online](#configure-arcgis-online-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte online testovacího uživatele ArcGIS](#create-arcgis-online-test-user)** , abyste měli protějšek Britta Simon v ArcGIS Online, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ArcGIS Online, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **online aplikací ArcGIS** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![ArcGIS Online domény a adresy URL jednotného přihlašování](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.maps.arcgis.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory online klientů ArcGIS](https://support.esri.com/en/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. Pokud chcete automatizovat konfiguraci v rámci služby **ArcGIS Online**, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Po přidání rozšíření do prohlížeče klikněte na **Nastavení ArcGIS Online** a přesměrujte vás na online aplikaci ArcGIS. Odtud zadejte přihlašovací údaje správce pro přihlášení k ArcGIS Online. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky v části **Konfigurace jednotného přihlašování ArcGIS Online**.

### <a name="configure-arcgis-online-single-sign-on"></a>Nakonfigurovat ArcGIS Online Single Sign-On

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

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ArcGIS Online.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **ArcGIS Online**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **ArcGIS Online**.

    ![Online odkaz ArcGIS v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

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

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici ArcGIS Online, měli byste se automaticky přihlásit k ArcGIS Online, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

