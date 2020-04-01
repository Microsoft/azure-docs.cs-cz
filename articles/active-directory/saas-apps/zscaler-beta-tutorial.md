---
title: 'Kurz: Integrace služby Azure Active Directory s aplikací Zscaler Beta | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a zscalerovou betaverzí.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07b0f8112f724c857ffb46378f7aa7ef605b9bbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943296"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Kurz: Integrace Služby Azure Active Directory s betaverzí Zscaler

V tomto kurzu se dozvíte, jak integrovat Zscaler Beta s Azure Active Directory (Azure AD).
Když integrujete Zscaler Beta s Azure AD, můžete:

* Řízení ve službě Azure AD, který má přístup k Zscaler Beta.
* Umožněte uživatelům, aby se automaticky přihlásili k Zscaler Beta pomocí svých účtů Azure AD. Toto řízení přístupu se nazývá jednotné přihlašování (SSO).
* Spravujte své účty v jednom centrálním umístění pomocí portálu Azure.

Další informace o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Zscaler Beta, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Zscaler Beta, které používá jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zscaler Beta podporuje sp-inicioval SSO.
* Zscaler Beta podporuje zřizování uživatelů just-in-time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Přidání betaverze Zscaler z Azure Marketplace

Pokud chcete nakonfigurovat integraci Zscaler Beta do Azure AD, přidejte Zscaler Beta z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Zscaler Beta z Azure Marketplace, postupujte takto.

1. Na [webu Azure Portal](https://portal.azure.com)v levém navigačním podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace.**

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler Beta**. Z výsledkového panelu vyberte **Zscaler Beta** a pak vyberte **Přidat**.

     ![Zscaler Beta v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Zscaler Beta na základě testovacího uživatele Britta Simon.
Pro jednotné přihlašování k práci, vytvořit vztah propojení mezi uživatelem Azure AD a související uživatel v Zscaler Beta.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí zscaler beta, vyplňte následující stavební bloky:

- [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) tak, aby uživatelé mohli tuto funkci používat.
- [Nakonfigurujte jednotné přihlašování Zscaler Beta](#configure-zscaler-beta-single-sign-on) tak, aby bylo možné konfigurovat nastavení jednotného přihlášení na straně aplikace.
- [Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD s Brittou Simonovou.
- [Přiřaďte uživateli testu Azure AD,](#assign-the-azure-ad-test-user) aby britta Simon mohla používat jednotné přihlašování Azure AD.
- [Vytvořte zscaler beta test uživatele](#create-a-zscaler-beta-test-user) mít protějšek Britta Simon v Zscaler Beta, který je propojený s reprezentací Azure AD uživatele.
- [Otestujte jednotné přihlášení](#test-single-sign-on) a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Zscaler Beta, postupujte takto.

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Zscaler Beta** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlašování](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlášení** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit,** abyste otevřeli dialogové okno **Základní konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** postupujte takto:

    ![Zscaler Beta domény a adresy URL jednotné přihlašovací informace](common/sp-intiated.png)

    - Do pole **Přihlásit se na adresu URL** zadejte adresu URL používanou uživateli k přihlášení do aplikace Zscaler Beta.

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou hodnotou adresy URL přihlášení. Chcete-li získat hodnotu, obraťte se na [tým podpory klienta Zscaler Beta](https://www.zscaler.com/company/contact).

5. Aplikace Zscaler Beta očekává kontrolní výrazy SAML v určitém formátu. Do konfigurace atributů tokenu SAML je nutné přidat vlastní mapování atributů. Následující snímek obrazovky zobrazuje seznam výchozích atributů. Vyberte **Upravit,** chcete-li otevřít dialogové okno **Atributy uživatele.**

    ![Dialogové okno Atributy uživatele](common/edit-attribute.png)

6. Aplikace Zscaler Beta očekává několik dalších atributů, které mají být předány zpět v odpovědi SAML. V části **Nároky uživatele** v dialogovém okně **Atributy uživatele** přidejte atribut tokenu SAML následujícím postupem, jak je znázorněno v následující tabulce.
    
    | Name (Název) | Atribut zdroje | 
    | ---------------| --------------- |
    | Memberof  | user.assignedroles |

    a. Výběrem **možnosti Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![Dialogové okno Deklarace identity uživatelů](common/new-save-attribute.png)

    ![Dialogové okno Spravovat deklarace identity uživatelů](common/new-attribute-details.png)

    b. Do pole **Název** zadejte název atributu zobrazený pro tento řádek.

    c. Pole **Obor názvů** ponechejte prázdné.

    d. V **popřípadě Zdroj**vyberte **Atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Vyberte **OK**.

    g. Vyberte **Uložit**.

    > [!NOTE]
    > Informace o konfiguraci rolí ve službě Azure AD najdete v [tématu Konfigurace deklarace deklarace role](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Na stránce **Nastavit jednotné přihlašování pomocí saml** včásti **Podpisový certifikát SAML** vyberte **stáhnout** a stáhněte si **certifikát (Base64).** Uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavit Zscaler Beta** zkopírujte adresy URL, které potřebujete pro své požadavky:

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    - Přihlašovací adresa URL
    - Identifikátor azure reklamy
    - Adresa URL odhlášení

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurace jednotného přihlašování zscaler beta

1. Chcete-li automatizovat konfiguraci v rámci zscaler beta, nainstalujte **rozšíření prohlížeče My Apps Secure Sign-in** výběrem **možnosti Nainstalovat rozšíření**.

    ![Rozšíření Moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče vás výběrem **možnosti Nastavit Zscaler Beta** přesměrujete na aplikaci Zscaler Beta. Odtud zadejte přihlašovací údaje správce pro přihlášení k Zscaler Beta. Rozšíření prohlížeče automaticky konfiguruje aplikaci pro vás a automatizuje kroky 3 až 6.

    ![Konfigurace nastavení](common/setup-sso.png)

3. Chcete-li nastavit Zscaler Beta ručně, otevřete nové okno webového prohlížeče. Přihlaste se k webu společnosti Zscaler Beta jako správce a postupujte takto.

4. Přejděte na Nastavení ověřování **autentizace** > **Authentication** > **Authentication Settings**a postupujte takto.
   
    ![Správa](./media/zscaler-beta-tutorial/ic800206.png "Správa")

    a. V části **Typ ověřování**vyberte **možnost SAML**.

    b. Vyberte **konfigurovat saml**.

5. V okně **Upravit SAML** postupujte takto: 
            
    ![Správa uživatelů & ověřování](./media/zscaler-beta-tutorial/ic800208.png "Správa uživatelů & ověřování")
    
    a. Do pole **ADRESA URL portálu SAML** vložte **do přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    b. Do pole **Atribut přihlašovacího jména** zadejte **NameID**.

    c. V poli **Veřejný certifikát SSL** vyberte **Nahrát,** chcete-li nahrát podpisový certifikát Azure SAML, který jste stáhli z webu Azure Portal.

    d. Přepnout **povolit automatické zřizování SAML**.

    e. Do pole **Atribut uživatelského zobrazovacího jména** zadejte **displayName,** pokud chcete povolit automatické zřízení SAML pro atributy displayName.

    f. Do pole **Atribut názvu skupiny** zadejte **memberOf,** pokud chcete povolit saml autoprovisioning pro memberOf atributy.

    g. Do pole **Název oddělení** zadejte **oddělení,** pokud chcete povolit automatické zřizování SAML pro atributy oddělení.

    h. Vyberte **Uložit**.

6. Na stránce **Konfigurovat ověřování uživatelů** postupujte takto:

    ![Aktivační nabídka a tlačítko Aktivovat](./media/zscaler-beta-tutorial/ic800207.png)

    a. Najeďte na nabídku **Aktivace** v levém dolním rohu.

    b. Vyberte **Aktivovat**.

## <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
Chcete-li konfigurovat nastavení serveru proxy v aplikaci Internet Explorer, postupujte takto.

1. Spusťte **aplikaci Internet Explorer**.

2. Vyberte **Volby Internetu** z nabídky **Nástroje** a otevřete dialogové okno **Možnosti Internetu.** 
    
     ![Dialogové okno Možnosti Internetu](./media/zscaler-beta-tutorial/ic769492.png "Možnosti Internetu")

3. Vyberte kartu **Připojení.** 
  
     ![Karta Připojení](./media/zscaler-beta-tutorial/ic769493.png "Připojení")

4. Výběrem **nastavení sítě LAN** otevřete dialogové okno Nastavení místní sítě **(LAN).**

5. V části **Proxy server** postupujte takto: 
   
    ![Oddíl Proxy server](./media/zscaler-beta-tutorial/ic769494.png "Proxy server")

    a. Zaškrtněte **políčko Použít proxy server pro síť LAN.**

    b. Do pole **Adresa** zadejte **bránu. zscalerBeta.net**.

    c. Do pole **Port** zadejte **80**.

    d. Zaškrtněte políčko **Přecházet proxy server pro místní adresy.**

    e. Výběrem **možnosti OK** zavřete dialogové okno **Nastavení místní sítě (LAN).**

6. Výběrem **možnosti OK** zavřete dialogové okno **Možnosti Internetu.**

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Vytvořte testovacího uživatele na webu Azure s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte možnost**Uživatelé služby** >  **Azure Active Directory** > **Všichni uživatelé**.

    ![Odkazy pro uživatele a všechny uživatele](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko Nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** postupujte takto:

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension`. Příklad: BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo.** Poznamenejte si hodnotu, která se zobrazí v poli **Heslo.**

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

Umožněte Brittě Simonové používat jednotné přihlašování Azure udělením přístupu k betaverzi Zscaler.

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace** > **Zscaler Beta**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **Zscaler Beta**.

    ![Zscaler Beta odkaz v seznamu aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz Uživatelé a skupiny](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Možnost Uživatelé a skupiny**.

    ![Tlačítko Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte ze seznamu uživatele jako **Britta Simon.** Pak v dolní části obrazovky zvolte **Vybrat.**

    ![Dialogové okno Uživatelé a skupiny](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. V dialogovém okně **Vybrat roli** vyberte příslušnou roli uživatele v seznamu. Pak v dolní části obrazovky zvolte **Vybrat.**

    ![Dialogové okno Vybrat roli](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

    ![Dialogové okno Přidat přiřazení](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Vytvoření testovacího uživatele Zscaler Beta

V této části je uživatel Britta Simon vytvořen v Zscaler Beta. Zscaler Beta podporuje **zřizování uživatelů just-in-time**, které je ve výchozím nastavení povoleno. V téhle sekci není nic, co bys mohl dělat. Pokud uživatel v Beta verzi Zscaler ještě neexistuje, vytvoří se po ověření nový uživatel.

>[!Note]
>Chcete-li vytvořit uživatele ručně, obraťte se na [tým podpory Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

Otestujte konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když vyberete zscaler beta dlaždice na přístupovém panelu, měli byste být automaticky přihlášeni k Zscaler Beta, u kterého nastavíte sso. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

