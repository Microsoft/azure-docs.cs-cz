---
title: 'Kurz: Integrace služby Azure Active Directory se zscalerem One | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Zscaler One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: d23fc94fba9a9b750f2977fd9c5d8297e4da8d5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68825917"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Kurz: Integrace Služby Azure Active Directory se zscalerem One

V tomto kurzu se dozvíte, jak integrovat Zscaler One s Azure Active Directory (Azure AD).
Integrace Zscaler One s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zscaler One.
* Můžete povolit uživatelům, aby se automaticky přihlásili k Zscaler One (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí Zscaler One, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Zscaler Jedno přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zscaler One podporuje **sp** inicioval SSO

* Zscaler One podporuje zřizování uživatelů **Just In Time**

## <a name="adding-zscaler-one-from-the-gallery"></a>Přidání Zscaler One z galerie

Chcete-li nakonfigurovat integraci Zscaler One do Azure AD, musíte přidat Zscaler One z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Zscaler One z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler One**, z panelu výsledků vyberte **Zscaler One** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Zscaler One v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Zscaler One na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Zscaler One.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zscaler One, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace zscaleru jedno jednotnépřihlášení](#configure-zscaler-one-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte zscaler jeden testovací uživatel](#create-zscaler-one-test-user)** – mít protějšek Britta Simon v Zscaler One, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí zscaleru One, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Zscaler One** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Zscaler One Domain a adresy URL jednotné přihlašovací informace](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresy URL** zadejte adresu URL používanou uživateli k přihlášení k aplikaci Zscaler One.

    > [!NOTE]
    > Hodnotu aktualizujete skutečnou adresou URL pro přihlášení. Obraťte se na [tým podpory zscaler jeden klient](https://www.zscaler.com/company/contact) získat hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Vaše aplikace Zscaler One očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje seznam výchozích atributů. Kliknutím na ikonu **Upravit** otevřete dialogové okno **Atributy uživatele.**

    ![image](common/edit-attribute.png)

6. Kromě výše uvedeného aplikace Zscaler One očekává, že několik dalších atributů bude předáno zpět v odpovědi SAML. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** přidejte atribut tokenu SAML následujícím postupem, jak je znázorněno v následující tabulce:
    
    | Name (Název) | Atribut zdroje |
    | ---------| ------------ |
    | Memberof     | user.assignedroles |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.
    
    f. Klikněte na **Uložit**.

    > [!NOTE]
    > Chcete-li vědět, jak nakonfigurovat roli ve službě Azure AD, klikněte [sem.](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)

7. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavit Zscaler One** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-zscaler-one-single-sign-on"></a>Konfigurace zscaleru jedním jedním přihlášením

1. Chcete-li automatizovat konfiguraci v rámci zscaler one, musíte nainstalovat **rozšíření prohlížeče My Apps Secure Sign-in** kliknutím na nainstalovat **rozšíření**.

    ![Rozšíření Moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Zscaler One** vás nasměruje do aplikace Zscaler One. Odtud zadejte pověření správce pro přihlášení do Zscaler One. Rozšíření prohlížeče automaticky nakonfiguruje aplikaci pro vás a automatizuje kroky 3-6.

    ![Nastavení sso](common/setup-sso.png)

3. Chcete-li nastavit Zscaler One ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti Zscaler One jako správce a proveďte následující kroky:

4. Přejděte na **nastavení ověřování > > ověřování a** proveďte následující kroky:
   
    ![Správa](./media/zscaler-one-tutorial/ic800206.png "Správa")

    a. V části Typ ověřování zvolte **SAML**.

    b. Klepněte na **tlačítko Konfigurovat službu SAML**.

5. V okně **Upravit SAML** proveďte následující kroky: a klepněte na uložit.  
            
    ![Správa uživatelů & ověřování](./media/zscaler-one-tutorial/ic800208.png "Správa uživatelů & ověřování")
    
    a. V textovém poli **URL portálu SAML** vložte **přihlašovací adresu URL,** kterou jste zkopírovali z webu Azure Portal.

    b. Do textového pole **Atribut přihlašovacího jména** zadejte **NameID**.

    c. Kliknutím na **Tlačítko Nahrát**nahrajete podpisový certifikát Azure SAML, který jste stáhli z portálu Azure, do **veřejného certifikátu SSL**.

    d. Přepněte **možnost Povolit automatické zřizování SAML**.

    e. Do textového pole **Atribut uživatelského zobrazovacího jména** zadejte **displayName,** pokud chcete povolit automatické zřizování saml pro atributy displayName.

    f. Do textového pole **Atribut názvu skupiny** zadejte **memberOf,** pokud chcete povolit automatické zřizování SAML pro atributy memberOf.

    g. V **oddělení** **Název oddělení Zadejte,** pokud chcete povolit automatické zřizování SAML pro atributy oddělení.

    h. Klikněte na **Uložit**.

6. Na stránce **Konfigurovat ověřování uživatelů** proveďte následující kroky:

    ![Správa](./media/zscaler-one-tutorial/ic800207.png)

    a. Najeďte na nabídku **Aktivace** v levém dolním rohu.

    b. Klepněte na **tlačítko Aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení serveru proxy v aplikaci Internet Explorer

1. Spusťte **aplikaci Internet Explorer**.

2. Vyberte **Volby Internetu** z nabídky **Nástroje** pro otevření dialogového okna **Možnosti Internetu.**   
    
     ![Možnosti Internetu](./media/zscaler-one-tutorial/ic769492.png "Možnosti Internetu")

3. Klikněte na kartu **Připojení.**   
  
     ![Připojení](./media/zscaler-one-tutorial/ic769493.png "Připojení")

4. Klepnutím na **nastavení sítě LAN** otevřete dialogové okno Nastavení sítě **LAN.**

5. V části Proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-one-tutorial/ic769494.png "Proxy server")

    a. Vyberte **možnost Použít proxy server pro síť LAN**.

    b. Do textového pole Adresa zadejte **bránu. Zscaler One.net**.

    c. Do textového pole Port zadejte **80**.

    d. Vyberte **možnost Obejít proxy server pro místní adresy**.

    e. Klepnutím na **tlačítko OK** zavřete dialogové okno Nastavení místní **sítě (LAN).**

7. Klepnutím na **tlačítko OK** zavřete dialogové okno **Možnosti Internetu.**

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** brittasimon@yourcompanydomain.extensionjméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Zscaler One.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Zscaler One**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscaler One**.

    ![Odkaz Zscaler One v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte ze seznamu uživatele jako **Britta Simon** a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_users.png)

6. V dialogovém okně **Vybrat roli** zvolte příslušnou roli uživatele v seznamu a pak klepněte na tlačítko **Vybrat** v dolní části obrazovky.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_roles.png)

7. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **Přiřadit.**

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_assign.png)

### <a name="create-zscaler-one-test-user"></a>Vytvořit testovacího uživatele Zscaler One

V této části je vytvořen uživatel s názvem Britta Simon v Zscaler One. Zscaler One podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v Zscaler One, nový je vytvořen po ověření.

>[!Note]
>Pokud potřebujete vytvořit uživatele ručně, obraťte se [na tým podpory Zscaler One](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Zscaler One na přístupovém panelu, měli byste být automaticky přihlášeni k Zscaler One, pro které nastavíte přihlašování. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

