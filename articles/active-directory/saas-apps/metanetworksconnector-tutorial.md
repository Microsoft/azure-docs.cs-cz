---
title: 'Kurz: Azure Active Directory integrace s konektorem meta Networks | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konektorem meta Networks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 5ef49cb2e245e4af7b5d7f7d250194761cf0b375
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826348"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Kurz: Azure Active Directory integrace s konektorem meta Networks

V tomto kurzu se naučíte, jak integrovat konektory meta Networks s Azure Active Directory (Azure AD).
Integrování konektorů meta Networks s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k konektoru meta Networks.
* Můžete uživatelům povolit, aby se automaticky přihlásili ke konektoru meta Networks Connector (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s konektorem meta Networks budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování v konektoru meta Networks

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Konektor meta Networks podporuje **SP** a **IDP** inicioval jednotné přihlašování.
 
* Konektor meta Networks podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Přidání konektoru meta Networks z Galerie

Pokud chcete nakonfigurovat integraci meta Network Connectoru do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat konektor meta Networks z galerie.

**K přidání konektoru meta Networks z Galerie proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte příkaz **meta Networks Connector**, vyberte **meta Networks Connector** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Konektor meta Networks v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí konektoru meta Networks na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v konektoru meta Networks Connector.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí konektoru meta Networks Connector, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování ke konektoru meta Networks](#configure-meta-networks-connector-single-sign-on)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele konektoru meta sítě](#create-meta-networks-connector-test-user)** – Pokud chcete mít protějšek Britta Simon v meta Networks Connectoru, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí konektoru meta Networks, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **konektoru meta sítě** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Informace o jednotném přihlašování domén a adres URL konektoru meta Networks](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Informace o jednotném přihlašování domén a adres URL konektoru meta Networks](common/both-advanced-urls.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL, které jsou vysvětleny dále v tomto kurzu.

6. Aplikace konektoru meta Networks očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)
    
7. Kromě výše očekává aplikace konektoru meta Networks, aby se v odpovědi SAML znovu předalo několik atributů. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:
    
    | Name | Zdrojový atribut | Obor názvů|
    | ---------------| --------------- | -------- |
    | FirstName | user.givenname | |
    | polím | user.surname | |
    | EmailAddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefon | user.telephonenumber | |

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

9. V části **Nastavení konektoru meta sítě** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-meta-networks-connector-single-sign-on"></a>Konfigurovat jednotné přihlašování pro konektory meta Networks

1. V prohlížeči otevřete novou kartu a přihlaste se ke svému účtu správce konektoru meta Networks.
    
    > [!NOTE]
    > Konektor meta Networks je zabezpečený systém. Takže před přístupem ke svému portálu potřebujete na své straně přidat veřejnou IP adresu do seznamu povolených adres. Pokud chcete získat veřejnou IP adresu, použijte níže uvedený odkaz. [](https://whatismyipaddress.com/) Pokud chcete přidat IP adresu do seznamu povolených adres, odešlete do [týmu podpory konektoru meta Networks konektoru pro podporu klientů](mailto:support@metanetworks.com) .
    
2. Otevřete **správce** a vyberte **Nastavení**.
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Zajistěte, aby **protokol internetového provozu** a **vynucení VÍCEFAKTOROVÉHO ověřování sítě VPN** byly vypnuté.
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Otevřete **správce** a vyberte **SAML**.
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Na stránce s podrobnostmi proveďte následující kroky:
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Zkopírujte hodnotu **URL jednotného přihlašování** a vložte ji do textového pole **přihlašovací adresa URL** v části **doména a adresy URL konektoru meta Networks** .
    
    b. Zkopírujte hodnotu **adresy URL příjemce** a vložte ji do textového pole **Adresa URL odpovědi** v části **doména a adresy URL konektoru meta Networks** .
    
    c. Zkopírujte hodnotu **identifikátoru URI cílové skupiny (ID entity SP)** a vložte ji do textového pole **identifikátor (ID entity)** v části **doména a adresy URL konektoru meta Networks** .
    
    d. Povolit SAML
    
6. Na kartě **Obecné** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure5.png)

    a. Do **adresy URL jednotného přihlašování zprostředkovatele identity**vložte hodnotu URL pro **přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. V vystaviteli **zprostředkovatele identity**vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete stažený certifikát z Azure Portal v programu Poznámkový blok, vložte ho do textového pole **certifikátu X. 509** .

    d. Povolte **zřizování za běhu**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k konektoru meta Networks.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **konektor meta sítě**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **meta sítě konektor**.

    ![Odkaz na konektor meta Networks v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-meta-networks-connector-test-user"></a>Vytvořit testovacího uživatele konektoru meta sítě

V této části se v konektoru meta Networks vytvoří uživatel s názvem Britta Simon. Konektor meta Networks podporuje zřizování za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v konektoru meta Networks ještě neexistuje, vytvoří se nový, když se pokusíte o přístup k konektoru meta Networks.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory klienta podpory meta Networks Connector](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici konektor meta Networks na přístupovém panelu byste měli být automaticky přihlášeni k síťovému konektoru meta Network, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

