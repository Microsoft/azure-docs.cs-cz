---
title: 'Kurz: Azure Active Directory integrace se serverem Tableau | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tableau serverem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: f9ef179c1a93d8b2f97c47eb4c68d0312d55d3d1
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825980"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Kurz: Azure Active Directory integrace se serverem Tableau

V tomto kurzu se naučíte, jak integrovat Server Tableau s Azure Active Directory (Azure AD).
Integrace serveru Tableau s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Tableau serveru.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Tableau serveru (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se serverem Tableau potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Odběr povoleného jednotného přihlašování Tableau serveru

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Tableau Server podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-tableau-server-from-the-gallery"></a>Přidání serveru Tableau z Galerie

Pokud chcete nakonfigurovat integraci serveru Tableau do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Tableau Server z galerie.

**Pokud chcete přidat server Tableau z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Tableau Server**, vyberte **Tableau Server** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Server Tableau v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s Tableau serverem na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Tableau serveru.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí serveru Tableau, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte jednotné přihlašování serveru Tableau](#configure-tableau-server-single-sign-on)** a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořte testovacího uživatele serveru Tableau](#create-tableau-server-test-user)** , abyste měli protějšek Britta Simon na serveru Tableau, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí serveru Tableau, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **serveru Tableau** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně serveru Tableau a adresách URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://azure.<domain name>.link`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://azure.<domain name>.link`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Předchozí hodnoty nejsou reálné hodnoty. Aktualizujte hodnoty skutečnou adresou URL a identifikátorem na stránce konfigurace serveru Tableau, která je vysvětlena dále v tomto kurzu.

5. Serverová aplikace Tableau očekává vlastní **uživatelské jméno** deklarace identity, které je potřeba definovat níže. Používá se jako identifikátor uživatele místo deklarace jedinečného identifikátoru uživatele. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele & deklarace** na stránce integrace aplikací. Kliknutím na tlačítko **Upravit** otevřete **atributy uživatele &** dialogovém okně deklarace.

    ![image](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele & deklarací** NAKONFIGURUJTE atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name | Zdrojový atribut | Obor názvů |
    | ---------------| --------------- | ----------- |
    | username | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Zadejte hodnotu **oboru názvů** .

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

8. V části **nastavení serveru Tableau** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-tableau-server-single-sign-on"></a>Konfigurace jednotného přihlašování pro Tableau Server

1. Pokud chcete pro vaši aplikaci nakonfigurovat jednotné přihlašování, musíte se přihlásit k tenantovi serveru Tableau jako správce.

2. Na kartě **Konfigurace** vyberte **identita uživatele & přístup**a pak vyberte kartu metoda **ověřování** .

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na stránce **Konfigurace** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. V případě **metody ověřování**vyberte SAML.

    b. Zaškrtněte políčko **Povolit ověřování SAML pro server**.

    c. Tableau návratová adresa URL serveru: adresa URL, na kterou budou uživatelé serveru Tableau přístup <http://tableau_server>, například. Použití `http://localhost` se nedoporučuje. Použití adresy URL s koncovým lomítkem (například `http://tableau_server/`) není podporováno. Zkopírujte **návratovou adresu URL serveru Tableau** a vložte ji do TEXTOVÉHO pole **URL pro přihlášení** v **základní části Konfigurace SAML** v Azure Portal

    d. ID entity SAML – ID entity jednoznačně identifikuje vaši instalaci serveru Tableau na IdP. Sem můžete zadat adresu URL serveru Tableau, pokud se vám to líbí, ale nemusí se jednat o adresu URL vašeho serveru Tableau. Zkopírujte **ID entity SAML** a vložte je do textového pole **identifikátor** v **základní části Konfigurace SAML** v Azure Portal

    e. Klikněte na **Stáhnout soubor METADAT XML** a otevřete ho v aplikaci textový editor. Vyhledejte adresu URL služby pro příjemce kontrolního výrazu s http post a index 0 a zkopírujte adresu URL. Nyní jej vložte do pole Komu **Adresa URL odpovědi** v **základní části konfigurace SAML** v Azure Portal

    f. Vyhledejte soubor federačních metadat stažený z Azure Portal a pak ho nahrajte do **souboru metadat SAML IDP**.

    g. Zadejte názvy atributů, které IdP používá k ukládání uživatelských jmen, zobrazovaných jmen a e-mailových adres.

    h. Klikněte na **Uložit**.

    > [!NOTE]
    > Zákazník musí nahrávat všechny certifikáty v konfiguraci Tableau serveru SAML pro jednotné přihlašování a v toku jednotného přihlašování se bude ignorovat. Pokud potřebujete pomáhat s konfigurací SAML na serveru Tableau, přečtěte si článek [Konfigurace SAML](https://help.tableau.com/current/server/en-gb/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména**`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k serveru Tableau.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Server Tableau**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Tableau Server**.

    ![Odkaz na server Tableau v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-tableau-server-test-user"></a>Vytvořit testovacího uživatele serveru Tableau

Cílem této části je vytvořit uživatele s názvem Britta Simon na serveru Tableau. Musíte zřídit všechny uživatele na serveru Tableau.

Uživatelské jméno uživatele by se mělo shodovat s hodnotou, kterou jste nakonfigurovali ve vlastním atributu Azure AD **uživatelského jména**. Po správném mapování by integrace měla fungovat s konfigurací jednotného přihlašování Azure AD.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na správce serveru Tableau ve vaší organizaci.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Tableau Server na přístupovém panelu, měli byste se automaticky přihlásit k serveru Tableau, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

