---
title: 'Kurz: Integrace Azure Active Directory s konektorem sítě Meta | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Meta sítě konektoru.
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
ms.openlocfilehash: aa0d7828534c96d9bda85e80fb297c848cec6845
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887017"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Kurz: Integrace Azure Active Directory s konektorem Meta sítě

V tomto kurzu se dozvíte, jak integrovat Meta sítě konektor služby Azure Active Directory (Azure AD).
Integrace konektoru Meta sítí s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Meta sítě konektoru.
* Uživatelům se automaticky přihlášeni k Meta sítě konektoru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s konektorem Meta sítě, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Konektor sítě meta-jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Meta sítě konektor podporuje **SP** a **IDP** jednotné přihlašování zahájené pomocí
 
* Meta sítě konektor podporuje **JIT** zřizování uživatelů

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Přidání konektoru Meta sítě z Galerie

Ke konfiguraci integrace konektoru Meta sítě do služby Azure AD, budete muset přidat Meta sítě konektor z Galerie na váš seznam spravovaných aplikací SaaS.

**Meta sítě konektoru přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Meta sítě konektor**vyberte **Meta sítě konektor** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Meta sítě konektor v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s konektorem Meta sítě podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Meta sítě konektoru.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s konektorem Meta sítě, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Meta sítě konektor jednotného přihlašování](#configure-meta-networks-connector-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření konektoru sítě Meta testovacího uživatele](#create-meta-networks-connector-test-user)**  – Pokud chcete mít protějšek Britta Simon v Meta sítě konektor, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s konektorem Meta sítě, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Meta sítě konektor** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Meta sítě konektor domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Meta sítě konektor domény a adresy URL jednotného přihlašování – informace](common/both-advanced-urls.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. V **stav přenosu** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizovat s identifikátorem skutečná adresa URL odpovědi, a přihlašovací adresa URL jsou vysvětleny dále v tomto kurzu.

6. Aplikace sítě konektor meta očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikony otevřete **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)
    
7. Kromě toho výše aplikace Meta sítě konektor očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:
    
    | Název | Zdrojový atribut | Obor názvů|
    | ---------------| --------------- | -------- |
    | jméno | user.givenname | |
    | Příjmení | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | jméno | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefon | user.telephonenumber | |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. Na **nastavte konektor sítě Meta** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-meta-networks-connector-single-sign-on"></a>Konfigurace Meta sítě konektor jednotného přihlašování

1. Otevření nové záložky v prohlížeči a přihlaste se k vašemu účtu správce konektoru Meta sítě.
    
    > [!NOTE]
    > Konektor sítí meta je zabezpečení systému. Proto před přístupem k jejich portálu musíte získat vaše veřejné IP adresy přidat na seznam povolených na své straně. Chcete-li získat veřejnou IP adresu, postupujte níže uvedeném odkazu [tady](https://whatismyipaddress.com/). Poslat vaši IP adresu, které [tým podpory Meta sítě konektor klienta](mailto:support@metanetworks.com) zobrazíte vašeho seznamu povolených IP adres.
    
2. Přejděte na **správce** a vyberte **nastavení**.
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Ujistěte se, že **protokol internetové přenosy** a **platnost VPN MFA** jsou nastaveny na vypnuto.
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Přejděte na **správce** a vyberte **SAML**.
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Následující postup proveďte **podrobnosti** stránky:
    
    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Kopírování **adresu URL jednotného přihlašování** hodnotu a vložte ho do **přihlašovací adresa URL** textového pole v **Meta sítě konektor domény a adresy URL** oddílu.
    
    b. Kopírování **příjemce URL** hodnotu a vložte ho do **adresy URL odpovědi** textového pole v **Meta sítě konektor domény a adresy URL** oddílu.
    
    c. Kopírování **identifikátor URI cílové skupiny (SP Entity ID)** hodnotu a vložte ho do **identifikátor (Entity ID)** textového pole v **Meta sítě konektor domény a adresy URL** oddílu.
    
    d. Povolit SAML
    
6. Na **Obecné** kartu, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure5.png)

    a. V **jednotné přihlašování – adresa URL zprostředkovatele Identity**, vložte **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    b. V **Vystavitel zprostředkovatele Identity**, vložte **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. Otevřete stažený certifikát z webu Azure portal v programu Poznámkový blok, vložit do **certifikát X.509** textového pole.

    d. Povolit **Just-in-Time zřizování**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Meta sítě konektoru.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Meta sítě konektor**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Meta sítě konektor**.

    ![Odkaz Meta sítě konektor v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-meta-networks-connector-test-user"></a>Vytvoření konektoru sítě Meta testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Meta sítě konektoru. Meta sítě konektor podporuje zřizování just-in-time je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Meta sítě konektor, je vytvořen nový při pokusu o přístup k Meta sítě konektoru.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Meta sítě konektor klienta](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Meta sítě konektor na přístupovém panelu, vám by měl být automaticky přihlášeni ke konektoru Meta sítě, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

