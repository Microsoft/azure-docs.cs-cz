---
title: 'Kurz: Azure Active Directory integrace s Zscaler ZSCloud | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler ZSCloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 9e27d2950041c4b0b2c928eb3d85a3d041866508
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517474"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Kurz: Azure Active Directory integrace s Zscaler ZSCloud

V tomto kurzu se dozvíte, jak integrovat Zscaler ZSCloud s Azure Active Directory (Azure AD).
Integrace Zscaler ZSCloud se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k Zscaler ZSCloud.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Zscaler ZSCloud (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Zscaler ZSCloud potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné Zscaler ZSCloud s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zscaler ZSCloud podporuje jednotné přihlašování se spuštěným **SP**

* Zscaler ZSCloud podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Přidání Zscaler ZSCloud z Galerie

Pokud chcete nakonfigurovat integraci Zscaler ZSCloud do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Zscaler ZSCloud z galerie.

**Pokud chcete přidat Zscaler ZSCloud z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler ZSCloud**, vyberte **Zscaler ZSCloud** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Zscaler ZSCloud v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Zscaler ZSCloud na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler ZSCloud.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zscaler ZSCloud, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Zscaler ZSCloud](#configure-zscaler-zscloud-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte Zscaler ZSCloud Test User](#create-zscaler-zscloud-test-user)** – abyste měli protějšek Britta Simon v Zscaler ZSCloud, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Zscaler ZSCloud, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ZSCloud pro Zscaler** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Zscaler ZSCloud, informace o jednotném přihlašování v doméně a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL používanou vašimi uživateli, abyste se přihlásili do vaší aplikace ZScaler ZSCloud.

    > [!NOTE]
    > Je nutné aktualizovat hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, kontaktujte [tým podpory Zscaler ZSCloud pro klienta](https://help.zscaler.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Vaše aplikace Zscaler ZSCloud očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

6. Kromě toho aplikace Zscaler ZSCloud očekává, že se v odpovědi SAML zpátky vrátí několik atributů. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:
    
    | Name | Zdrojový atribut |
    | ---------| ------------ |
    | memberOf     | User. assignedroles |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.
    
    f. Klikněte na **Uložit**.

    > [!NOTE]
    > Chcete-li zjistit, jak nakonfigurovat roli v Azure AD, klikněte prosím [sem](../develop/active-directory-enterprise-app-role-management.md) .

7. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavení ZSCloud Zscaler** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-zscaler-zscloud-single-sign-on"></a>Konfigurace Zscaler ZSCloud Single Sign-On

1. Pokud chcete automatizovat konfiguraci v rámci Zscaler ZSCloud, je potřeba nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Nastavení Zscaler ZSCloud** vás přesměruje do aplikace Zscaler ZSCloud. Odtud zadejte přihlašovací údaje správce, které se budou přihlašovat k Zscaler ZSCloud. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Nastavení jednotného přihlašování](common/setup-sso.png)

3. Pokud chcete nastavit Zscaler ZSCloud ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Zscaler ZSCloud společnosti jako správce a proveďte následující kroky:

4. V části **správa > ověřování > nastavení ověřování** a proveďte následující kroky:
   
    ![Správa](./media/zscaler-zscloud-tutorial/ic800206.png "Správa")

    a. V části typ ověřování vyberte **SAML**.

    b. Klikněte na **Konfigurovat SAML**.

5. V okně **Upravit SAML** proveďte následující kroky: a klikněte na Uložit.  
            
    ![Správa uživatelů & ověřování](./media/zscaler-zscloud-tutorial/ic800208.png "Správa uživatelů & ověřování")
    
    a. Do textového pole **Adresa URL portálu SAML** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **atributu přihlašovací jméno** zadejte **NameId**.

    c. Klikněte na **nahrát**a nahrajte podpisový certifikát Azure SAML, který jste stáhli z Azure Portal ve **veřejném certifikátu SSL**.

    d. Přepněte **možnost povolit Automatické zřizování SAML**.

    e. Do textového pole **atribut zobrazovaného jména uživatele** zadejte **DisplayName** , pokud chcete povolit Automatické zřizování SAML pro atributy DisplayName.

    f. Do textového pole **atributu název skupiny** zadejte **memberOf** , pokud chcete povolit Automatické zřizování SAML pro atributy memberOf.

    například V **atributu název oddělení** zadejte **oddělení** , pokud chcete povolit Automatické zřizování SAML pro atributy oddělení.

    h. Klikněte na **Uložit**.

6. Na stránce **Konfigurovat ověření uživatele** proveďte následující kroky:

    ![Správa](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Najeďte myší na nabídku **Aktivace** v blízkosti levého dolního rohu.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v Internet Exploreru

1. Spusťte **aplikaci Internet Explorer**.

2. V nabídce **nástroje** vyberte **Možnosti Internetu** . otevře se dialogové okno **Možnosti Internetu** .   
    
     ![Možnosti Internetu](./media/zscaler-zscloud-tutorial/ic769492.png "Možnosti Internetu")

3. Klikněte na kartu **připojení** .   
  
     ![Připojení](./media/zscaler-zscloud-tutorial/ic769493.png "Připojení")

4. Kliknutím na **Nastavení místní sítě** otevřete dialogové okno **nastavení sítě LAN** .

5. V části proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-zscloud-tutorial/ic769494.png "Proxy server")

    a. Vyberte **použít proxy server pro vaši síť LAN**.

    b. Do textového pole Adresa zadejte **Gateway. Zscaler ZSCloud.net**.

    c. Do textového pole Port zadejte **80**.

    d. Vyberte možnost **obejít proxy server pro místní adresy**.

    e. Kliknutím na tlačítko **OK** zavřete dialogové okno **Nastavení místní sítě (LAN)** .

6. Kliknutím na tlačítko **OK** zavřete dialogové okno **Možnosti Internetu** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Zscaler ZSCloud.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Zscaler ZSCloud**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Zscaler ZSCloud**.

    ![Odkaz Zscaler ZSCloud v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatele, jako je **Britta Simon** , a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. V dialogu **Vybrat roli** vyberte příslušnou roli uživatele v seznamu a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >Výchozí role přístupu není podporována, protože tím dojde k přerušení zřizování, takže výchozí roli nelze vybrat při přiřazování uživatele.

### <a name="create-zscaler-zscloud-test-user"></a>Vytvořit testovacího uživatele Zscaler ZSCloud

V této části se v Zscaler ZSCloud vytvoří uživatel s názvem Britta Simon. Zscaler ZSCloud podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Zscaler ZSCloud neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Zscaler ZSCloud](https://help.zscaler.com/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Zscaler ZSCloud, měli byste být automaticky přihlášeni k Zscaler ZSCloud, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)