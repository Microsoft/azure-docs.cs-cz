---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s prochází | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Projděte si.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/5/2021
ms.author: jeedes
ms.openlocfilehash: 53b4702bd4ac5b4fe608eb7a0e161f36d6cb4cb5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726928"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-upshotly"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s procházím

V tomto kurzu se naučíte, jak se dá integrovat s Azure Active Directory (Azure AD). Když provedete integraci s Azure AD, můžete:

* Řízení ve službě Azure AD, která má k přístup.
* Umožněte uživatelům, aby se automaticky přihlásili k účtu Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* **S podporou aktualizace SP a IDP** bylo zahájeno jednotné přihlašování.

## <a name="adding-upshotly-from-the-gallery"></a>Přidávání z galerie z Galerie

Pokud chcete nakonfigurovat integraci do služby Azure AD, musíte se z Galerie přidat z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole **Zadejte.**
1. Vyberte z panelu výsledků možnost **profotit** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-upshotly"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v provedenou spolupráci.

K nastavování a testování jednotného přihlašování služby Azure AD můžete použít následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace](#configure-upshotly-sso)** prochází SSO – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si testovacího uživatele](#create-upshotly-test-user)** , který bude mít protějšek B. Simon, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na **stránce pro integraci aplikací Najděte** část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/tužka pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , je v **základní části Konfigurace SAML** předem nakonfigurovaná aplikace a nezbytné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://app.upshotly.com/api/sso/login/<companyID>`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL. Hodnota **companyID** se vysvětluje později v tomto kurzu. Pro dotazy se obraťte na [tým podpory pro klienty](mailto:support@upshotly.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení propracovaného** kopírování zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k provedeně.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **profotit**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-upshotly-sso"></a>Konfigurace prosnímku jednotného přihlašování

1. Chcete-li automatizovat konfiguraci v rámci provedeně, je nutné nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na možnost **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče se kliknutím na **nastavit** provedený postup vás přesměruje na aplikaci, která je k. Odtud zadejte přihlašovací údaje správce, abyste se mohli přihlásit. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-4.

    ![Konfigurace instalace](common/setup-sso.png)

3. Chcete-li nastavit provedenou ruční instalaci v jiném okně webového prohlížeče, přihlaste se k webu, který si provedete jako správce.

1. Klikněte na **Profil uživatele** a přejděte na **správce > jednotné přihlašování** a proveďte následující kroky:

    ![Konfigurace, která je provržena](./media/upshotly-tutorial/config1.png)

    a. Zkopírujte hodnotu **ID společnosti** a pomocí této hodnoty **ID** společnosti nahraďte hodnotu **ID společnosti** , která se nachází v **přihlašovací adrese URL** v **základní části Konfigurace SAML** v Azure Portal.

    b. Otevřete stažené **federační metadata XML** z Azure Portal do poznámkového bloku, ZKOPÍRUJTE obsah XML metadat a vložte ho do textového pole **metadat XML** .

### <a name="create-upshotly-test-user"></a>Vytvořit protestovaný test uživatele

V této části vytvoříte uživatele s názvem B. Simon v provedený hraničním cloudu. Pokud chcete přidat uživatele na špičkové cloudové platformě, pracujte s [týmem podpory klientů](mailto:support@upshotly.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro místní přihlášení, kde můžete spustit tok přihlášení.

* Přejít přímo na adresu URL pro přihlášení a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Kliknutím na **otestovat tuto aplikaci** v Azure Portal a automaticky se přihlásíte, abyste se mohli automaticky přihlásili, abyste si nastavili jednotné přihlašování.

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když v okně moje aplikace kliknete na dlaždici, která je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro zahájení toku přihlášení a v případě, že se nakonfiguruje v režimu IDP, měli byste se automaticky přihlásit k profilaci, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).