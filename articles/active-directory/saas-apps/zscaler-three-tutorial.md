---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Zscaler tři | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler tři.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 75d95ff77b48e7b1102900bc103e6930282e21e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726212"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Zscaler tři

V tomto kurzu se dozvíte, jak integrovat Zscaler tři s Azure Active Directory (Azure AD). Když integrujete Zscaler tři s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Zscaler třem.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Zscaler třem pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.


## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zscaler tři odběry s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zscaler tři podporují jednotné přihlašování se spuštěnou **aktualizací SP**

* Zscaler tři podporují zřizování uživatelů **jenom v čase**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-zscaler-three-from-the-gallery"></a>Přidání Zscaler tři z Galerie

Pokud chcete nakonfigurovat integraci Zscaler tří do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Zscaler tři z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Zscaler tři** .
1. Z panelu výsledků vyberte **Zscaler tři** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-three"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Zscaler tři

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s Zscaler tři pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler tři.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zscaler tří, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Zscaler tři jednotné přihlašování](#configure-zscaler-three-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Zscaler tři testovací uživatele](#create-zscaler-three-test-user)** – abyste měli protějšek B. Simon v Zscaler třech, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace **tří aplikací Zscaler** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://login.zscalerthree.net/sfc_sso`

1. Vaše Zscaler tři aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

6. Kromě toho Zscaler tři aplikace očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut |
    | ---------| ------------ |
    | memberOf | User. assignedroles |

    > [!NOTE]
    > Pokud chcete zjistit, jak nakonfigurovat roli v Azure AD, klikněte prosím [sem](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit tři Zscaler** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Zscaler tři.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Zscaler tři**.
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatele, jako je **Britta Simon** , a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

    ![Snímek obrazovky se zobrazí v dialogovém okně Uživatelé a skupiny, kde můžete vybrat uživatele.](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. V dialogu **Vybrat roli** vyberte příslušnou roli uživatele v seznamu a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

    ![Snímek obrazovky se zobrazí dialogové okno vybrat roli, kde můžete zvolit roli uživatele.](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

    ![Snímek obrazovky se zobrazí dialogové okno Přidat přiřazení, kde můžete vybrat přiřadit.](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Konfigurace Zscaler tři jednotné přihlašování

1. Pokud chcete automatizovat konfiguraci v rámci Zscaler tří, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Nastavení Zscaler tři** a nasměrujte vás na Zscalerovou aplikaci. Odtud zadejte přihlašovací údaje správce, abyste se přihlásili k Zscaler třem. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Nastavení](common/setup-sso.png)

3. Pokud chcete nastavit Zscaler tři ručně, otevřete nové okno webového prohlížeče a přihlaste se ke svému Zscaler webu společnosti jako správce a proveďte následující kroky:

4. V části **správa > ověřování > nastavení ověřování** a proveďte následující kroky:

    ![Snímek obrazovky ukazuje Zscaler jednu lokalitu podle postupu popsaného v tématu.](./media/zscaler-three-tutorial/ic800206.png "Správa")

    a. V části typ ověřování vyberte **SAML**.

    b. Klikněte na **Konfigurovat SAML**.

5. V okně **Upravit SAML** proveďte následující kroky: a klikněte na Uložit.  

    ![Správa uživatelů & ověřování](./media/zscaler-three-tutorial/ic800208.png "Správa uživatelů & ověřování")

    a. Do textového pole **Adresa URL portálu SAML** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **atributu přihlašovací jméno** zadejte **NameId**.

    c. Klikněte na **nahrát** a nahrajte podpisový certifikát Azure SAML, který jste stáhli z Azure Portal ve **veřejném certifikátu SSL**.

    d. Přepněte **možnost povolit Automatické zřizování SAML**.

    e. Do textového pole **atribut zobrazovaného jména uživatele** zadejte **DisplayName** , pokud chcete povolit Automatické zřizování SAML pro atributy DisplayName.

    f. Do textového pole **atributu název skupiny** zadejte **memberOf** , pokud chcete povolit Automatické zřizování SAML pro atributy memberOf.

    například V **atributu název oddělení** zadejte **oddělení** , pokud chcete povolit Automatické zřizování SAML pro atributy oddělení.

    h. Klikněte na **Uložit**.

6. Na stránce **Konfigurovat ověření uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí dialogové okno Konfigurovat ověření uživatele s vybraným polem aktivovat.](./media/zscaler-three-tutorial/ic800207.png)

    a. Najeďte myší na nabídku **Aktivace** v blízkosti levého dolního rohu.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v Internet Exploreru

1. Spusťte **aplikaci Internet Explorer**.

2. V nabídce **nástroje** vyberte **Možnosti Internetu** . otevře se dialogové okno **Možnosti Internetu** .   

     ![Možnosti Internetu](./media/zscaler-three-tutorial/ic769492.png "Možnosti Internetu")

3. Klikněte na kartu **připojení** .   

     ![Připojení](./media/zscaler-three-tutorial/ic769493.png "Připojení")

4. Kliknutím na **Nastavení místní sítě** otevřete dialogové okno **nastavení sítě LAN** .

5. V části proxy server proveďte následující kroky:   

    ![Proxy server](./media/zscaler-three-tutorial/ic769494.png "Proxy server")

    a. Vyberte **použít proxy server pro vaši síť LAN**.

    b. Do textového pole Adresa zadejte **Gateway. Zscaler Three.net**.

    c. Do textového pole Port zadejte **80**.

    d. Vyberte možnost **obejít proxy server pro místní adresy**.

    e. Kliknutím na tlačítko **OK** zavřete dialogové okno **Nastavení místní sítě (LAN)** .

6. Kliknutím na tlačítko **OK** zavřete dialogové okno **Možnosti Internetu** .

### <a name="create-zscaler-three-test-user"></a>Vytvořit Zscaler tři testovací uživatele

V této části je uživatel nazvaný B. Simon vytvořen v Zscaler 3. Zscaler tři podporují zřizování za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Zscaler tři neexistuje, vytvoří se nový, když se pokusíte o přístup k Zscaler tři.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, kontaktujte [Zscaler tři tým podpory](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Zscaler tři přihlašovací adresy URL, kde můžete spustit tok přihlášení. 

* Přejít na Zscaler tři přihlašovací adresa URL přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Zscaler tři v nabídce Moje aplikace, přesměruje se na Zscaler tři přihlašovací adresy URL. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile Zscaler tři konfigurace, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
