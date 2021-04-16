---
title: 'Kurz: Azure Active Directory integrace s Zscaler dvě | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler dvou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: b4d39492a05ee90c0ba2e081336d7b04579a5c43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519031"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Kurz: Azure Active Directory integrace s Zscaler dvě

V tomto kurzu se dozvíte, jak integrovat Zscaler dvě s Azure Active Directory (Azure AD). Když integrujete Zscaler dvě s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Zscaler dvěma.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Zscaler dvěma účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí Zscaler dvou budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zscaler se dva odběry s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zscaler **Two podporuje jednotné** přihlašování (SSO).

* Zscaler dvě podporují zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-zscaler-two-from-the-gallery"></a>Přidání Zscaler 2 z Galerie

Pokud chcete nakonfigurovat integraci Zscalerch dvou do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Zscaler dvě z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Zscaler 2** .
1. Z panelu výsledků vyberte **Zscaler dvě** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-two"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Zscaler dvě

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s Zscaler dvěma pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler dvou.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zscaler dvou, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte dvě ZSCALER SSO](#configure-zscaler-two-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Zscaler dva testovací uživatele](#create-zscaler-two-test-user)** – abyste měli protějšek B. Simon v Zscaler dvou, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace **dvou aplikací Zscaler** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL používanou vašimi uživateli, abyste se přihlásili ke své ZScaler dvě aplikace.

    > [!NOTE]
    > Hodnotu aktualizujete skutečnou adresou URL Sign-On. Pokud chcete získat hodnotu, kontaktujte [Zscalerho týmu podpory klientů](https://www.zscaler.com/company/contact) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Vaše Zscaler dvě aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

6. Kromě výše uvedeného platí, že Zscaler dvě aplikace očekává, že se v odpovědi SAML zpátky vrátí několik atributů. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:
    
    | Name | Zdrojový atribut |
    | ---------| ------------ |
    | memberOf | User. assignedroles |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky zobrazuje deklarace identity uživatelů s možností přidání nové deklarace identity.](common/new-save-attribute.png)

    ![Snímek obrazovky se zobrazí dialogové okno Spravovat deklarace identity uživatelů, kde můžete zadat hodnoty, které jsou popsány.](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut** vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.
    
    f. Klikněte na **Uložit**.

    > [!NOTE]
    > Pokud chcete zjistit, jak nakonfigurovat roli v Azure AD, klikněte prosím [sem](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) .

7. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavení Zscaler dvě** zkopírujte příslušné adresy URL podle vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Zscaler dvěma.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Zscaler dvě**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-zscaler-two-sso"></a>Konfigurace Zscaler dvou jednotného přihlašování

1. Pokud chcete automatizovat konfiguraci v rámci Zscaler dvou, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Zscaler nastavení 2** a nasměrujte vás na Zscaler dvě aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení k Zscaler dvěma. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Nastavení jednotného přihlašování](common/setup-sso.png)

3. Pokud chcete nastavit Zscaler dvakrát, otevřete nové okno webového prohlížeče a přihlaste se ke svojí Zscaler firemnímu webu jako správce a proveďte následující kroky:

4. V části **správa > ověřování > nastavení ověřování** a proveďte následující kroky:
   
    ![Snímek obrazovky ukazuje Zscaler jednu lokalitu podle postupu popsaného v tématu.](./media/zscaler-two-tutorial/administrator.png "Správa")

    a. V části typ ověřování vyberte **SAML**.

    b. Klikněte na **Konfigurovat SAML**.

5. V okně **Upravit SAML** proveďte následující kroky: a klikněte na Uložit.  
            
    ![Správa uživatelů & ověřování](./media/zscaler-two-tutorial/authentication.png "Správa uživatelů & ověřování")
    
    a. Do textového pole **Adresa URL portálu SAML** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **atributu přihlašovací jméno** zadejte **NameId**.

    c. Klikněte na **nahrát** a nahrajte podpisový certifikát Azure SAML, který jste stáhli z Azure Portal ve **veřejném certifikátu SSL**.

    d. Přepněte **možnost povolit Automatické zřizování SAML**.

    e. Do textového pole **atribut zobrazovaného jména uživatele** zadejte **DisplayName** , pokud chcete povolit Automatické zřizování SAML pro atributy DisplayName.

    f. Do textového pole **atributu název skupiny** zadejte **memberOf** , pokud chcete povolit Automatické zřizování SAML pro atributy memberOf.

    například V **atributu název oddělení** zadejte **oddělení** , pokud chcete povolit Automatické zřizování SAML pro atributy oddělení.

    h. Klikněte na **Uložit**.

6. Na stránce **Konfigurovat ověření uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí dialogové okno Konfigurovat ověření uživatele s vybraným polem aktivovat.](./media/zscaler-two-tutorial/activation.png)

    a. Najeďte myší na nabídku **Aktivace** v blízkosti levého dolního rohu.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings&quot;></a>Konfigurace nastavení proxy serveru

### <a name=&quot;to-configure-the-proxy-settings-in-internet-explorer&quot;></a>Konfigurace nastavení proxy serveru v Internet Exploreru

1. Spusťte **aplikaci Internet Explorer**.

2. V nabídce **nástroje** vyberte **Možnosti Internetu** . otevře se dialogové okno **Možnosti Internetu** .   
    
     ![Možnosti Internetu](./media/zscaler-two-tutorial/internet.png &quot;Možnosti Internetu")

3. Klikněte na kartu **připojení** .   
  
     ![Připojení](./media/zscaler-two-tutorial/ic769493.png "Připojení")

4. Kliknutím na **Nastavení místní sítě** otevřete dialogové okno **nastavení sítě LAN** .

5. V části proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-two-tutorial/proxy.png "Proxy server")

    a. Vyberte **použít proxy server pro vaši síť LAN**.

    b. Do textového pole Adresa zadejte **Gateway. Zscaler Two.net**.

    c. Do textového pole Port zadejte **80**.

    d. Vyberte možnost **obejít proxy server pro místní adresy**.

    e. Kliknutím na tlačítko **OK** zavřete dialogové okno **Nastavení místní sítě (LAN)** .

6. Kliknutím na tlačítko **OK** zavřete dialogové okno **Možnosti Internetu** .


### <a name="create-zscaler-two-test-user"></a>Vytvořit Zscaler dva testovací uživatele

V této části se v Zscaler 2 vytvoří uživatel s názvem Britta Simon. Zscaler Two podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Zscaler dvakrát neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, kontaktujte [Zscaler dva týmy podpory](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Zscaler dvě přihlašovací adresy URL, kde můžete spustit tok přihlášení. 

* Přejít na Zscaler dvě přihlašovací adresa URL přímo a zahajte tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Zscaler dvě v části Moje aplikace, přesměruje se na Zscaler dvě přihlašovací adresa URL. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Zscaler dvou můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
