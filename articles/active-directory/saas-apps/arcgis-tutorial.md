---
title: 'Kurz: Integrace služby Azure Active Directory s ArcGIS Online | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a ArcGIS Online.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232090"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Kurz: Integrace služby Azure Active Directory s ArcGIS Online

V tomto kurzu se dozvíte, jak integrovat ArcGIS Online s Azure Active Directory (Azure AD).
Integrace ArcGIS Online s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ArcGIS Online.
* Můžete povolit uživatelům, aby se automaticky přihlásili k ArcGIS Online (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s ArcGIS Online, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením ArcGIS Online

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ArcGIS Online podporuje **sp** inicioval SSO

## <a name="adding-arcgis-online-from-the-gallery"></a>Přidání ArcGIS Online z galerie

Chcete-li nakonfigurovat integraci ArcGIS Online do Služby Azure AD, musíte přidat ArcGIS Online z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat ArcGIS Online z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ArcGIS Online**, z panelu výsledků vyberte **ArcGIS Online** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![ArcGIS Online v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ArcGIS Online na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v ArcGIS Online.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ArcGIS Online, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování ArcGIS Online](#configure-arcgis-online-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu ArcGIS Online](#create-arcgis-online-test-user)** – chcete-li mít protějšek Britta Simon v ArcGIS Online, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí ArcGIS Online, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **ArcGIS Online** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení k online doméně ArcGIS a adresURL](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.maps.arcgis.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se [na tým podpory arcgis online klienta](https://support.esri.com/en/) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Chcete-li automatizovat konfiguraci v rámci **arcgis online**, je třeba nainstalovat **my apps secure sign-in rozšíření prohlížeče** kliknutím na nainstalovat **rozšíření**.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Po přidání rozšíření do prohlížeče, klikněte na **nastavení ArcGIS Online** vás nasměruje do aplikace ArcGIS Online. Odtud zadejte přihlašovací údaje správce pro přihlášení do ArcGIS Online. Rozšíření prohlížeče automaticky nakonfiguruje aplikaci pro vás a automatizuje kroky v sekci **Konfigurace ArcGIS Online Jednotné přihlašování**.

### <a name="configure-arcgis-online-single-sign-on"></a>Konfigurace jednotného přihlašování ArcGIS Online

1. Chcete-li nastavit ArcGIS Online ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti ArcGIS jako správce a proveďte následující kroky:

2. Klepněte na **tlačítko UPRAVIT NASTAVENÍ**.

    ![Upravit nastavení](./media/arcgis-tutorial/ic784742.png "Upravit nastavení")

3. Klepněte na **položku Zabezpečení**.

    ![Zabezpečení](./media/arcgis-tutorial/ic784743.png "Zabezpečení")

4. V části **Enterprise Logins**klikněte na **SET IDENTITY PROVIDER**.

    ![Podnikové přihlášení](./media/arcgis-tutorial/ic784744.png "Podnikové přihlášení")

5. Na stránce **Konfigurace nastavit zprostředkovatele identity** proveďte následující kroky:

    ![Nastavit zprostředkovatele identity](./media/arcgis-tutorial/ic784745.png "Nastavit zprostředkovatele identity")

    a. Do textového pole **Název** zadejte název organizace.

    b. Pro **metadata pro zprostředkovatele podnikové identity budou dodány pomocí**, vyberte **soubor**.

    c. Chcete-li nahrát stažený soubor metadat, klepněte na **tlačítko Vybrat soubor**.

    d. Klikněte na **nastavit zprostředkovatele identity**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k ArcGIS Online.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **ArcGIS Online**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **ArcGIS Online**.

    ![Odkaz ArcGIS Online v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-arcgis-online-test-user"></a>Vytvořit testovacího uživatele ArcGIS Online

Aby bylo možné uživatelům Azure AD přihlásit se k ArcGIS Online, musí být zřízeny do ArcGIS Online.  
V případě ArcGIS Online zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **ArcGIS.**

2. Klepněte na tlačítko **POZVAT členy**.
   
    ![Pozvat členy](./media/arcgis-tutorial/ic784747.png "Pozvat členy")

3. Vyberte **Automaticky přidat členy bez odeslání e-mailu**a klepněte na tlačítko **DALŠÍ**.
   
    ![Automatické přidávání členů](./media/arcgis-tutorial/ic784748.png "Automatické přidávání členů")

4. Na stránce dialogového okna **Členové** proveďte následující kroky:
   
     ![Přidání a kontrola](./media/arcgis-tutorial/ic784749.png "Přidání a kontrola")
    
     a. Zadejte **e-mail**, **jméno**a **příjmení** platného účtu Azure AD, který chcete zřídit.
  
     b. Klepněte na tlačítko **PŘIDAT A ZKONTROLOVAT**.
5. Zkontrolujte zadaná data a klepněte na tlačítko **PŘIDAT ČLENY**.
   
    ![Přidat člena](./media/arcgis-tutorial/ic784750.png "Přidat člena")
        
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a poté potvrdí odkaz, který potvrdí jeho účet, než se stane aktivním.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici ArcGIS Online na přístupovém panelu, můžete by měl být automaticky přihlášeni k ArcGIS Online, pro které nastavíte přihlašující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

