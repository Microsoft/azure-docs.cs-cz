---
title: 'Kurz: Azure Active Directory integrace s Zscaler Beta | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler Beta.
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
ms.openlocfilehash: 4e8dce970c9e7cfb2aa7887cf1fb3ed6ef9030a1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735574"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Kurz: Azure Active Directory integrace s Zscaler Beta

V tomto kurzu se dozvíte, jak integrovat Zscaler Beta s Azure Active Directory (Azure AD).
Když integrujete Zscaler Beta s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Zscaler Beta.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Zscaler Beta pomocí svých účtů Azure AD. Toto řízení přístupu se nazývá jednotné přihlašování (SSO).
* Spravujte své účty v jednom centrálním umístění pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Zscaler Beta budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Zscaler Beta, které používá jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zscaler Beta podporuje jednotné přihlašování, které iniciuje **SP** .
* Zscaler Beta podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-zscaler-beta-from-the-gallery"></a>Přidání beta verze Zscaler z Galerie

Pokud chcete nakonfigurovat integraci Zscaler Beta na Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Zscaler Beta z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Zscaler Beta** .
1. Z panelu výsledků vyberte **Zscaler Beta** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Zscaler Beta

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Zscaler Beta pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler Beta.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Zscaler Beta, proveďte následující kroky:


1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování Zscaler Beta verze](#configure-zscaler-beta-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořte testovacího uživatele Zscaler Beta verze](#create-zscaler-beta-test-user)** , abyste měli protějšek B. Simon ve Zscaler Beta, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **Zscaler Beta** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do pole **přihlašovací adresa URL** zadejte adresu URL, kterou vaši uživatelé používají pro přihlášení do aplikace Zscaler Beta beta.

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu pomocí hodnoty skutečná adresa URL pro přihlášení. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Zscaler Beta](https://www.zscaler.com/company/contact).

5. Aplikace Zscaler Beta očekává kontrolní výrazy SAML v určitém formátu. Do konfigurace atributů tokenu SAML musíte přidat mapování vlastních atributů. Následující snímek obrazovky ukazuje seznam výchozích atributů. Výběrem **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![Uživatelské atributy – dialogové okno](common/edit-attribute.png)

6. Aplikace Zscaler Beta očekává, že se v odpovědi SAML vrátí několik atributů zpátky. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** pomocí následujících kroků přidejte atribut tokenu SAML, jak je znázorněno v následující tabulce.
    
    | Name | Zdrojový atribut | 
    | ---------------| --------------- |
    | memberOf  | User. assignedroles |

    a. Výběrem možnosti **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    b. Do pole **název** zadejte název atributu zobrazený pro tento řádek.

    c. Pole **obor názvů** nechejte prázdné.

    d. Jako **zdroj** vyberte **atribut**.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Vyberte **OK**.

    například Vyberte **Uložit**.

    > [!NOTE]
    > Pokud chcete zjistit, jak nakonfigurovat roli v Azure AD, klikněte prosím [sem](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) .

7. Pokud chcete stáhnout **certifikát (Base64)**, na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** . Uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

8. V části **nastavení beta verze Zscaler** zkopírujte adresy URL, které potřebujete pro vaše požadavky:

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Zscaler Beta.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Zscaler Beta**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud jste nastavili role, jak je vysvětleno výše, můžete je vybrat v rozevíracím seznamu **Vybrat roli** .
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-zscaler-beta-sso"></a>Konfigurace jednotného přihlašování Zscaler Beta

1. Pokud chcete automatizovat konfiguraci v rámci Zscaler Beta, nainstalujte pomocí možnosti **nainstalovat rozšíření** možnost **zabezpečení prohlížeče zabezpečené přihlašování pro aplikace** .

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče vyberte **nastavit Zscaler Beta verze** přímo na aplikaci Zscaler Beta. Odtud zadejte přihlašovací údaje správce pro přihlášení ke službě Zscaler Beta. Rozšíření prohlížeče aplikaci automaticky nakonfiguruje za vás a automatizuje kroky 3 až 6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Zscaler Beta ručně, otevřete nové okno webového prohlížeče. Přihlaste se k webu Zscaler Beta společnosti jako správce a postupujte podle těchto kroků.

4. Přejděte na **Správa**  >    >  **nastavení ověřování** ověřování a postupujte podle těchto kroků.
   
    ![Správa](./media/zscaler-beta-tutorial/ic800206.png "Správa")

    a. V části **typ ověřování** vyberte **SAML**.

    b. Vyberte **Konfigurovat SAML**.

5. V okně **Upravit SAML** proveďte tyto kroky: 
            
    ![Správa uživatelů & ověřování](./media/zscaler-beta-tutorial/ic800208.png "Správa uživatelů & ověřování")
    
    a. Do pole **Adresa URL portálu SAML** vložte **adresu URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do pole **atribut přihlašovací jméno** zadejte **NameId**.

    c. V poli **veřejný certifikát SSL** vyberte **Odeslat** a odešlete podpisový certifikát Azure SAML, který jste stáhli z Azure Portal.

    d. Přepněte na **zapnout automatické zřizování SAML**.

    e. Do pole **Zobrazovaný název uživatele** zadejte **DisplayName** , pokud chcete povolit autozřizování SAML pro atributy DisplayName.

    f. Do pole **atribut název skupiny** zadejte **memberOf** , pokud chcete povolit autozřizování SAML pro atributy memberOf.

    například Do pole **atribut název oddělení** zadejte **oddělení** , pokud chcete povolit autozřizování SAML pro atributy oddělení.

    h. Vyberte **Uložit**.

6. Na stránce **Konfigurovat ověřování uživatele** proveďte následující kroky:

    ![Nabídka aktivace a tlačítko aktivovat](./media/zscaler-beta-tutorial/ic800207.png)

    a. Najeďte myší na nabídku **Aktivace** v levém dolním rohu.

    b. Vyberte **aktivovat**.

## <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru
Pokud chcete nakonfigurovat nastavení proxy serveru v Internet Exploreru, postupujte podle těchto kroků.

1. Spusťte **aplikaci Internet Explorer**.

2. V nabídce **nástroje** vyberte **Možnosti Internetu** a otevřete tak dialogové okno **Možnosti Internetu** . 
    
     ![Dialogové okno Možnosti Internetu](./media/zscaler-beta-tutorial/ic769492.png "Možnosti Internetu")

3. Vyberte kartu **připojení** . 
  
     ![Karta Připojení](./media/zscaler-beta-tutorial/ic769493.png "Připojení")

4. Výběrem **Možnosti nastavení sítě LAN** otevřete dialogové okno **Nastavení místní sítě (LAN)** .

5. V části **proxy server** použijte následující postup: 
   
    ![Část proxy serveru](./media/zscaler-beta-tutorial/ic769494.png "Proxy server")

    a. Zaškrtněte políčko **použít proxy server pro vaši síť LAN** .

    b. Do pole **adresa** zadejte **Gateway. Zscaler Beta.net**.

    c. Do pole **port** zadejte **80**.

    d. Zaškrtněte políčko **obejít proxy server pro místní adresy** .

    e. Kliknutím na **tlačítko OK** zavřete dialogové okno **Nastavení místní sítě (LAN)** .

6. Kliknutím na **tlačítko OK** zavřete dialogové okno **Možnosti Internetu** .

### <a name="create-zscaler-beta-test-user"></a>Vytvořit testovacího uživatele pro Zscaler Beta

V této části se uživatel Britta Simon vytvoří v Zscaler Beta. Zscaler Beta podporuje **zřizování uživatelů za běhu**, což je ve výchozím nastavení povolené. V této části nemusíte nic dělat. Pokud uživatel ještě v Zscaler Beta neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud chcete uživatele vytvořit ručně, obraťte se na [tým podpory Zscaler Beta](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení Zscaler Beta, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení ke službě Zscaler Beta přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Zscaler Beta v části Moje aplikace, přesměruje se na adresu URL pro přihlášení Zscaler Beta. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Zscaler Beta můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
