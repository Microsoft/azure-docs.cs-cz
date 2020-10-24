---
title: 'Kurz: Azure Active Directory integrace s konektorem meta Networks | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konektorem meta Networks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: b14a75dba2860c9dee58e40673d3299fdde277e7
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516842"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Kurz: Azure Active Directory integrace s konektorem meta Networks

V tomto kurzu se naučíte, jak integrovat konektory meta Networks s Azure Active Directory (Azure AD).
Integrování konektorů meta Networks s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k konektoru meta Networks.
* Můžete uživatelům povolit, aby se automaticky přihlásili ke konektoru meta Networks Connector (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

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

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte příkaz **meta Networks Connector**, vyberte **meta Networks Connector** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Konektor meta Networks v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí konektoru meta Networks na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v konektoru meta Networks Connector.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí konektoru meta Networks Connector, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro konektory meta Networks](#configure-meta-networks-connector-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele konektoru meta sítě](#create-meta-networks-connector-test-user)** – Pokud chcete mít protějšek Britta Simon v meta Networks Connectoru, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí konektoru meta Networks, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **konektoru meta sítě** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/both-advanced-urls.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a Sign-On URL jsou vysvětleny dále v tomto kurzu.

6. Aplikace konektoru meta Networks očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)
    
7. Kromě výše očekává aplikace konektoru meta Networks, aby se v odpovědi SAML znovu předalo několik atributů. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:
    
    | Name | Zdrojový atribut | Obor názvů|
    | ---------------| --------------- | -------- |
    | FirstName | User. křestní jméno | |
    | polím | User. příjmení | |
    | EmailAddress| uživatel. pošta| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | User. userPrincipalName| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | Android | User. telephoneNumber | |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky zobrazuje deklarace identity uživatelů s možností přidání nové deklarace identity.](common/new-save-attribute.png)

    ![Snímek obrazovky se zobrazí dialogové okno Spravovat deklarace identity uživatelů, kde můžete zadat hodnoty, které jsou popsány.](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

8. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

9. V části **Nastavení konektoru meta sítě** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>Konfigurovat jeden Sign-On konektoru meta Networks

1. V prohlížeči otevřete novou kartu a přihlaste se ke svému účtu správce konektoru meta Networks.
    
    > [!NOTE]
    > Konektor meta Networks je zabezpečený systém. Takže před přístupem ke svému portálu potřebujete na své straně přidat veřejnou IP adresu do seznamu povolených adres. Pokud chcete získat veřejnou IP adresu, použijte [níže uvedený odkaz.](https://whatismyipaddress.com/) Pokud chcete přidat IP adresu do seznamu povolených adres, odešlete do [týmu podpory konektoru meta Networks konektoru pro podporu klientů](mailto:support@metanetworks.com) .
    
2. Otevřete **správce** a vyberte **Nastavení**.
    
    ![Snímek obrazovky zobrazuje nastavení vybraná v nabídce Správa.](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Zajistěte, aby **protokol internetového provozu** a **vynucení VÍCEFAKTOROVÉHO ověřování sítě VPN** byly vypnuté.
    
    ![Snímek obrazovky ukazuje vypnutí těchto nastavení.](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Otevřete **správce** a vyberte **SAML**.
    
    ![Snímek obrazovky zobrazuje v nabídce Správa možnost SAML.](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Na stránce s **podrobnostmi** proveďte následující kroky:
    
    ![Snímek obrazovky se zobrazí stránka s PODROBNOSTmi, kde můžete zadat hodnoty, které jsou popsány.](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Zkopírujte hodnotu **URL jednotného přihlašování** a vložte ji do textového pole **přihlašovací adresa URL** v části **doména a adresy URL konektoru meta Networks** .
    
    b. Zkopírujte hodnotu **adresy URL příjemce** a vložte ji do textového pole **Adresa URL odpovědi** v části **doména a adresy URL konektoru meta Networks** .
    
    c. Zkopírujte hodnotu **identifikátoru URI cílové skupiny (ID entity SP)** a vložte ji do textového pole **identifikátor (ID entity)** v části **doména a adresy URL konektoru meta Networks** .
    
    d. Povolit SAML
    
6. Na kartě **Obecné** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí stránka Obecné, kde můžete zadat hodnoty, které jsou popsány.](./media/metanetworksconnector-tutorial/configure5.png)

    a. Do **adresy URL jednoho Sign-On zprostředkovatele identity**vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. V **vystaviteli zprostředkovatele identity**vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete stažený certifikát z Azure Portal v programu Poznámkový blok, vložte ho do textového pole **certifikátu X. 509** .

    d. Povolte **zřizování za běhu**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k konektoru meta Networks.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **konektor meta sítě**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **meta sítě konektor**.

    ![Odkaz na konektor meta Networks v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-meta-networks-connector-test-user"></a>Vytvořit testovacího uživatele konektoru meta sítě

V této části se v konektoru meta Networks vytvoří uživatel s názvem Britta Simon. Konektor meta Networks podporuje zřizování za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v konektoru meta Networks ještě neexistuje, vytvoří se nový, když se pokusíte o přístup k konektoru meta Networks.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory klienta podpory meta Networks Connector](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici konektor meta Networks na přístupovém panelu byste měli být automaticky přihlášeni k síťovému konektoru meta Network, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)