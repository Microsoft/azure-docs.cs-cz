---
title: 'Kurz: Integrace Azure Active Directory s PureCloud podle Genesys | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PureCloud pomocí Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 92df57737b75ab2c9bb9992dd3f223f55dbc39bb
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826132"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>Kurz: Integrace Azure Active Directory s PureCloud podle Genesys

V tomto kurzu se naučíte, jak integrovat PureCloud pomocí Genesys s Azure Active Directory (Azure AD).
Integrace PureCloud by Genesys se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k PureCloud pomocí Genesys.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k PureCloud prostřednictvím Genesys (jednotné přihlašování) se svými účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s PureCloud pomocí Genesys potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* PureCloud podle povoleného předplatného jednotného přihlašování Genesys

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* PureCloud by Genesys podporuje **SP a IDP** INICIované SSO

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Přidání PureCloud pomocí Genesys z Galerie

Pokud chcete nakonfigurovat integraci PureCloud by Genesys do služby Azure AD, musíte přidat PureCloud od galerie Genesys z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat PureCloud podle Genesys z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **PureCloud by Genesys**, vyberte **PureCloud podle Genesys** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![PureCloud podle Genesys v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí PureCloud pomocí Genesys na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v PureCloud podle Genesys.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí PureCloud od Genesys, je nutné dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte PureCloud pomocí jednotného přihlašování Genesys](#configure-purecloud-by-genesys-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořte PureCloud pomocí Genesys testovacího uživatele](#create-purecloud-by-genesys-test-user)** – abyste měli protějšek Britta Simon v PureCloud pomocí Genesys, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí PureCloud pomocí Genesys, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce Application Integration **PureCloud podle Genesys** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![PureCloud podle informací o jednotném přihlašování v doméně Genesys a adresách URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL dle vaší oblasti:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL dle vaší oblasti:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![PureCloud podle informací o jednotném přihlašování v doméně Genesys a adresách URL](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL dle vaší oblasti:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. PureCloud by aplikace Genesys očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

7. Kromě výše uvedeného aplikace PureCloud podle Genesys očekává, že se v odpovědi SAML vrátí jenom několik atributů, které se mají vrátit zpátky. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Name | Zdrojový atribut|
    | ---------------| --------------- |
    | Email | User. userprinicipalname |
    | Název organizace | `Your organization name` |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. V části **Nastavení PureCloud podle Genesys** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>Konfigurace PureCloud pomocí jednotného přihlašování Genesys

1. V jiném okně webového prohlížeče se přihlaste k PureCloud pomocí Genesys jako správce.

2. V horní části klikněte na **správce** a v části **integrace**přejděte na **jednotné přihlašování** .

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure01.png)

3. Přepněte na kartu **ADFS/Azure AD (Premium)** a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Kliknutím na tlačítko **Procházet** Nahrajte certifikát s kódováním base-64, který jste stáhli z Azure Portal, do **certifikátu ADFS**.

    b. Do textového pole **identifikátor URI vystavitele ADFS** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    c. Do textového pole **cílový identifikátor URI** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Pro hodnotu **identifikátoru předávající strany** musíte přejít na Azure Portal, na stránce integrace aplikace **Genesys PureCloud podle** klikněte na kartu **vlastnosti** a zkopírujte hodnotu **ID aplikace** . Vložte ji do textového pole **identifikátoru předávající strany** . 

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Klikněte na **Uložit**.   

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k PureCloud Genesys.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **PureCloud podle Genesys**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **PureCloud podle Genesys**.

    ![Odkaz PureCloud podle Genesys v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-purecloud-by-genesys-test-user"></a>Vytvořit PureCloud podle Genesys testovacího uživatele

Aby se uživatelé Azure AD mohli přihlásit k PureCloud prostřednictvím Genesys, musí se zřídit v PureCloud pomocí Genesys. V PureCloud podle Genesys je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k PureCloud pomocí Genesys jako správce.

2. V horní části klikněte na **správce** a přejděte na **lidi** v části **lidé & oprávnění**.

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure03.png)

3. Na stránce Lidé klikněte na **Přidat osobu**.

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure04.png)

4. V místní nabídce **Přidat lidi k organizaci** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Do textového pole jméno a **příjmení** zadejte jméno uživatele, jako je **Brittasimon**.

    b. Do textového pole **e-mail** zadejte e-maily uživatele, jako je **brittasimon\@contoso.com**.
    
    c. Klikněte na možnost **Vytvořit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici PureCloud by Genesys, měli byste se k PureCloud automaticky přihlásit pomocí Genesys, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

