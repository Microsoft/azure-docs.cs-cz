---
title: 'Kurz: Integrace služby Azure Active Directory s konektorem Meta Networks | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a konektorem Meta Networks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: a09eda25e8c7cc087770210cdfbe7e2bc9832acf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160635"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Kurz: Integrace služby Azure Active Directory s konektorem Meta Networks

V tomto kurzu se dozvíte, jak integrovat meta sítě konektor s Azure Active Directory (Azure AD).
Integrace konektoru Meta Networks s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke konektoru Meta networks.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke konektoru Meta Networks Connector (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s konektorem Meta Networks, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením pomocí rozhraní Meta Networks

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Meta Networks Connector podporuje **sp** a **IDP** inicioval a so
 
* Meta Networks Connector podporuje zřizování uživatelů **Just In Time**

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Přidání konektoru Meta Networks z galerie

Chcete-li nakonfigurovat integraci konektoru Meta Networks do služby Azure AD, je třeba přidat konektor Meta Networks z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat konektor Meta Networks z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Meta Networks Connector**, z panelu výsledků vyberte Meta Networks **Connector** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Meta Networks Connector v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí konektoru Meta Networks connector na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v konektoru Meta Networks.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí konektoru Meta Networks Connector, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování konektoru Meta Networks](#configure-meta-networks-connector-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte meta sítě konektor testovací ho uživatele](#create-meta-networks-connector-test-user)** – mít protějšek Britta Simon v meta sítě konektor, který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí konektoru Meta Networks Connector, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Meta Networks Connector** vyberte Jedno **přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** Pokud chcete nakonfigurovat aplikaci v režimu iniciovaného **protokolem IDP,** proveďte následující kroky:

    ![Meta Networks Connector Doména a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Meta Networks Connector Doména a adresy URL jednotné přihlašovací informace](common/both-advanced-urls.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Do textového pole **Stav přenosu** zadejte adresu URL pomocí následujícího vzoru:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizace těchto hodnot s aktuální identifikátor, adresa URL odpovědi a přihlašovací URL jsou vysvětleny dále v kurzu.

6. Aplikace Meta Networks Connector očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání vlastních mapování atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje seznam výchozích atributů. Kliknutím na ikonu **Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)
    
7. Kromě výše uvedené aplikace Meta Networks Connector očekává několik dalších atributů, které mají být předány zpět v odpovědi SAML. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** přidejte atribut tokenu SAML následujícím postupem, jak je znázorněno v následující tabulce:
    
    | Name (Název) | Atribut zdroje | Obor názvů|
    | ---------------| --------------- | -------- |
    | Jméno | user.givenname | |
    | Lastname | user.příjmení | |
    | Emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | jméno | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | Android | user.phonenumber | |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Klikněte na **Ok.**

    g. Klikněte na **Uložit**.

8. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. V části **Nastavit konektor meta sítí** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-meta-networks-connector-single-sign-on"></a>Konfigurace jednotného přihlašování konektoru metasítí

1. Otevřete novou kartu v prohlížeči a přihlaste se ke svému účtu správce konektoru Meta Networks Connector.
    
    > [!NOTE]
    > Meta Networks Connector je zabezpečený systém. Takže před přístupem k jejich portálu musíte dostat svou veřejnou IP adresu přidándo seznamu povolených na jejich straně. Chcete-li získat veřejnou IP adresu, postupujte podle níže uvedeného odkazu [zde](https://whatismyipaddress.com/). Odešlete svou IP adresu [týmu podpory klienta Konektor meta networks,](mailto:support@metanetworks.com) aby vaše IP adresa byla přidána do seznamu povolených adres.
    
2. Přejděte na **Správce** a vyberte **Nastavení**.
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Ujistěte **se, že protokolovat internetový provoz** a **vynutit VPN MFA** jsou nastaveny na vypnuto.
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Přejděte na **správce** a vyberte **možnost SAML**.
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Na stránce **PODROBNOSTI** proveďte následující kroky:
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Zkopírujte hodnotu **adresy URL služby SSO** a vložte ji do textového pole **přihlašovací adresy URL** v části Domény **konektoru meta sítí a adresy URL.**
    
    b. Zkopírujte hodnotu **adresy URL příjemce** a vložte ji do textového pole Adresa URL pro **odpověď** v části **Domény konektoru meta sítí a adresy URL.**
    
    c. Zkopírujte hodnotu **URI cílové skupiny (SP Entity ID)** a vložte ji do textového pole **Identifikátor (ID entity)** v části **Domény konektoru meta sítí a adresy URL.**
    
    d. Povolení saml
    
6. Na kartě **OBECNÉ** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure5.png)

    a. V **adrese URL jednotného přihlášení zprostředkovatele identity**vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    b. V **issuer zprostředkovatele identity**vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali z portálu Azure.

    c. Otevřete stažený certifikát z portálu Azure v poznámkovém bloku a vložte ho do textového pole **Certifikát X.509.**

    d. Povolte **zřizování just-in-time**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** zadejte **\@brittasimon yourcompanydomain.extension .**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Meta networks konektor.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Meta **Networks Connector**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **meta networks konektor**.

    ![Propojení konektoru Meta-sítě v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-meta-networks-connector-test-user"></a>Vytvořit testovacího uživatele konektoru meta sítí

V této části je uživatel s názvem Britta Simon vytvořen v meta sítě konektoru. Meta Networks Connector podporuje zřizování za chvíli, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel v konektoru Meta Networks již neexistuje, vytvoří se při pokusu o přístup ke konektoru Meta Networks nový uživatel.

>[!Note]
>Pokud potřebujete vytvořit uživatele ručně, obraťte se [na tým podpory klienta Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Meta networks konektor na přístupovém panelu, měli byste být automaticky přihlášeni ke konektoru Meta sítě, pro které nastavíte přistupující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

