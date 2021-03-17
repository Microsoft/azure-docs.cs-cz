---
title: 'Kurz: Azure Active Directory integrace s nástrojem sjednocuje | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a sjednocuje.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 1fa1c40c96694b71bc762050998257102b687c18
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650615"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Kurz: Azure Active Directory integrace s nástrojem sjednocuje

V tomto kurzu se dozvíte, jak integrovat: sjednocuje s Azure Active Directory (Azure AD). Při integraci nástroje sjednocuje s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k prvku sjednocuje.
* Umožněte uživatelům, aby se přihlásili k vašemu účtu služby Azure AD automaticky.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SJEDNOCUJE podporuje **SP a IDP** iniciované jednotné přihlašování.
* SJEDNOCUJE podporuje **automatizované** zřizování uživatelů.

## <a name="add-unifi-from-the-gallery"></a>Přidání pole sjednocuje z Galerie

Pokud chcete nakonfigurovat integraci nástroje sjednocuje do Azure AD, musíte z Galerie přidat do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **sjednocuje** .
1. Vyberte možnost **sjednocuje** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>Konfigurace a testování jednotného přihlašování Azure AD pro sjednocuje

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí webu Simon pomocí testovacího uživatele s názvem **B.**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v prvku sjednocuje.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí webu sjednocuje, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace aplikace sjednocuje SSO](#configure-unifi-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele sjednocuje](#create-unifi-test-user)** , abyste měli protějšek B. Simon v sjednocuje, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **sjednocuje** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    Do textového pole **identifikátor** zadejte adresu URL: `INVIEWlabs`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.discoverunifi.com/login`

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **nastavit sjednocuje** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k sjednocuje.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **sjednocuje**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-unifi-sso"></a>Konfigurace jednotného přihlašování k webu sjednocuje

1. V jiném okně webového prohlížeče se přihlaste k webu svého společnosti **sjednocuje** jako správce.

2. Klikněte na **uživatele**.

    ![Snímek obrazovky se zobrazí uživatelé vybraní z webu sjednocuje.](./media/unifi-tutorial/app-1.png)

3. Klikněte na **Přidat nového zprostředkovatele identity**.

    ![Snímek obrazovky zobrazuje vybraný Nový zprostředkovatel identity.](./media/unifi-tutorial/app-2.png)

4. V části **Přidat zprostředkovatele identity** proveďte následující kroky:

    ![Snímek obrazovky s informacemi o přidání zprostředkovatele identity, kde můžete zadat hodnoty, které jsou popsány.](./media/unifi-tutorial/app-3.png) 

    a. Do textového pole **název poskytovatele** zadejte název zprostředkovatele identity.

    b. Do textového pole **Adresa URL poskytovatele** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete certifikát, který jste stáhli z Azure Portal v programu Poznámkový blok, odeberte **---zahajte---certifikátu** a **------** a vložte zbývající obsah do textového pole **certifikátu** .

    d. Zaškrtněte políčko **je výchozím zprostředkovatelem** .

### <a name="create-unifi-test-user"></a>Vytvořit testovacího uživatele |

V této části vytvoříte uživatele s názvem Britta Simon. Funkce **sjednocuje** podporuje automatické zřizování uživatelů, takže není nutné provádět žádné ruční kroky. Uživatelé se automaticky vytvoří po úspěšném ověření z Azure AD.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k adrese URL, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k webu sjednocuje přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste být automaticky přihlášeni k vlastnosti sjednocuje, pro kterou jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici sjednocuje v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k webu sjednocuje, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování prvku sjednocuje můžete vyhovět řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).