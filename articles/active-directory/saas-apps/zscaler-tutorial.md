---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Zscaler | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.openlocfilehash: 438537efc9922e681c6dd253c2f41a44c5f2fb92
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608897"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Zscaler

V tomto kurzu se dozvíte, jak integrovat Zscaler s Azure Active Directory (Azure AD). Když integrujete Zscaler s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Zscaler.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Zscaler svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zscaler odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zscaler podporuje jednotné přihlašování iniciované v **SP**
* Zscaler podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-zscaler-from-the-gallery"></a>Přidání Zscaler z Galerie

Pokud chcete nakonfigurovat integraci Zscaler do služby Azure AD, musíte přidat Zscaler z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Zscaler** .
1. Na panelu výsledků vyberte **Zscaler** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Zscaler

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Zscaler pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zscaler, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte ZSCALER SSO](#configure-zscaler-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvořte Zscaler Test User](#create-zscaler-test-user)** -to, abyste měli protějšek B. Simon v Zscaler, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Zscaler** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<companyname>.zscaler.net`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Zscaler](https://www.zscaler.com/company/contact) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Vaše aplikace Zscaler očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

1. Kromě výše očekává aplikace Zscaler několik dalších atributů, které se vrátí zpátky v odpovědi SAML. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Název | Zdrojový atribut |
    | ---------| ------------ |
    | memberOf | User. assignedroles |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut** vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **Uložit**.

    > [!NOTE]
    > Chcete-li zjistit, jak nakonfigurovat roli v Azure AD, klikněte prosím [sem](../develop/active-directory-enterprise-app-role-management.md) .

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Zscaler** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Zscaler.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Zscaler**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zscaler**.

    ![Odkaz Zscaler v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatele, jako je **Britta Simon** , a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

    ![Snímek obrazovky se zobrazí v dialogovém okně Uživatelé a skupiny, kde můžete vybrat uživatele.](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. V dialogu **Vybrat roli** vyberte příslušnou roli uživatele v seznamu a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

    ![Snímek obrazovky se zobrazí dialogové okno vybrat roli, kde můžete zvolit roli uživatele.](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

    ![Snímek obrazovky se zobrazí dialogové okno Přidat přiřazení, kde můžete vybrat přiřadit.](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

## <a name="configure-zscaler-sso"></a>Konfigurace jednotného přihlašování Zscaler

1. Pokud chcete automatizovat konfiguraci v rámci Zscaler, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **Zscaler nastavení** a nasměrujte vás na aplikaci Zscaler. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Zscaler. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Nastavení jednotného přihlašování](common/setup-sso.png)

1. Pokud chcete nastavit Zscaler ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Zscaler společnosti jako správce a proveďte následující kroky:

1. V části **správa > ověřování > nastavení ověřování** a proveďte následující kroky:

    ![Snímek obrazovky ukazuje Zscaler jednu lokalitu podle postupu popsaného v tématu.](./media/zscaler-tutorial/ic800206.png "Správa")

    a. V části typ ověřování vyberte **SAML**.

    b. Klikněte na **Konfigurovat SAML**.

1. V okně **Upravit SAML** proveďte následující kroky: a klikněte na Uložit.  

    ![Správa uživatelů & ověřování](./media/zscaler-tutorial/ic800208.png "Správa uživatelů & ověřování")

    a. Do textového pole **Adresa URL portálu SAML** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **atributu přihlašovací jméno** zadejte **NameId**.

    c. Klikněte na **nahrát** a nahrajte podpisový certifikát Azure SAML, který jste stáhli z Azure Portal ve **veřejném certifikátu SSL**.

    d. Přepněte **možnost povolit Automatické zřizování SAML**.

    e. Do textového pole **atribut zobrazovaného jména uživatele** zadejte **DisplayName** , pokud chcete povolit Automatické zřizování SAML pro atributy DisplayName.

    f. Do textového pole **atributu název skupiny** zadejte **memberOf** , pokud chcete povolit Automatické zřizování SAML pro atributy memberOf.

    například V **atributu název oddělení** zadejte **oddělení** , pokud chcete povolit Automatické zřizování SAML pro atributy oddělení.

    h. Klikněte na **Uložit**.

1. Na stránce **Konfigurovat ověření uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí dialogové okno Konfigurovat ověření uživatele s vybraným polem aktivovat.](./media/zscaler-tutorial/ic800207.png)

    a. Najeďte myší na nabídku **Aktivace** v blízkosti levého dolního rohu.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v Internet Exploreru

1. Spusťte **aplikaci Internet Explorer**.

1. V nabídce **nástroje** vyberte **Možnosti Internetu** . otevře se dialogové okno **Možnosti Internetu** .

    ![Možnosti Internetu](./media/zscaler-tutorial/ic769492.png "Možnosti Internetu")

1. Klikněte na kartu **připojení** .

    ![Připojení](./media/zscaler-tutorial/ic769493.png "Připojení")

1. Kliknutím na **Nastavení místní sítě** otevřete dialogové okno **nastavení sítě LAN** .

1. V části proxy server proveďte následující kroky:   

    ![Proxy server](./media/zscaler-tutorial/ic769494.png "Proxy server")

    a. Vyberte **použít proxy server pro vaši síť LAN**.

    b. Do textového pole Adresa zadejte **Gateway.Zscaler.NET**.

    c. Do textového pole Port zadejte **80**.

    d. Vyberte možnost **obejít proxy server pro místní adresy**.

    e. Kliknutím na tlačítko **OK** zavřete dialogové okno **Nastavení místní sítě (LAN)** .

1. Kliknutím na tlačítko **OK** zavřete dialogové okno **Možnosti Internetu** .

### <a name="create-zscaler-test-user"></a>Vytvořit testovacího uživatele Zscaler

V této části se v Zscaler vytvoří uživatel s názvem Britta Simon. Zscaler podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Zscaler neexistuje, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Zscaler](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Zscaler, měli byste se automaticky přihlásit k Zscaler, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Zscaler s Azure AD](https://aad.portal.azure.com/)