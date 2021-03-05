---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s integrací Kendis-Azure AD | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a integrací Kendis Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: jeedes
ms.openlocfilehash: 8409a4d897ea9b20528a5b30273819e6962774cb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184487"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis---azure-ad-integration"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s integrací Kendis-Azure AD

V tomto kurzu se dozvíte, jak integrovat integraci Kendis-Azure AD s Azure Active Directory (Azure AD). Když integruje integraci Kendis-Azure AD s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k integraci Kendis-Azure AD.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k integraci Kendis-Azure AD s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Kendis – odběr služby Azure AD Integration Single Signing (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kendis – integrace služby Azure AD podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování
* Kendis – integrace služby Azure AD podporuje zřizování uživatelů **jenom v čase**


## <a name="adding-kendis---azure-ad-integration-from-the-gallery"></a>Přidání integrace Kendis-Azure AD z Galerie

Pokud chcete nakonfigurovat integraci Kendis-Azure AD Integration do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Kendis integraci Azure AD z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **KENDIS-Azure AD Integration** .
1. Vyberte **Kendis-Integration Azure AD** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-kendis---azure-ad-integration"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Kendis – Integrace Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí integrace Kendis-Azure AD pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v integraci s Kendis Azure AD.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí integrace Kendis-Azure AD, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Kendis-Azure AD](#configure-kendis-azure-ad-integration-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit Kendis-Azure testování služby AD Integration User](#create-kendis-azure-ad-integration-test-user)** – aby bylo možné mít protějšek B. Simon v integraci Kendis-Azure AD, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **integrace Kendis-Azure AD** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.kendis.io`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, kontaktujte [Kendis – tým podpory pro klienta Azure AD Integration](mailto:support@kendis.io) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení integrace Kendis-Azure AD** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k integraci Kendis-Azure AD.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Kendis-integrace služby Azure AD**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-kendis-azure-ad-integration-sso"></a>Nakonfigurovat jednotné přihlašování pro integraci Kendis-Azure AD

1. Pokud chcete automatizovat konfiguraci v rámci integrace Kendis-Azure AD, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit Kendis – integrace služby Azure AD** vás přesměruje do integrační aplikace Kendis-Azure AD. Odtud zadejte přihlašovací údaje správce, které se přihlásí k integraci Kendis-Azure AD. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit integraci Kendis-Azure AD ručně, v jiném okně webového prohlížeče se přihlaste ke svému webu Azure AD Integration Company jako správce.

4. Přejít na **nastavení > konfigurace SAML**.

    ![nastavení pro konfigurace SAML](./media/kendis-scaling-agile-platform-tutorial/settings.png)

5. V dolní části stránky klikněte na tlačítko **Upravit** a proveďte následující kroky.

    ![Konfigurace SAML](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Zkopírujte hodnotu **adresy URL zpětného volání** , vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v části základní konfigurace SAML v Azure Portal.

    b. V textovém poli **Adresa URL jednotného přihlašování zprostředkovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c.  Do textového pole **vystavitele zprostředkovatele identity** vložte hodnotu **identifikátoru (ID entity) Azure AD** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu X. 509** .

    e. V seznamu možností **Vyberte možnost Výchozí skupina** . 

    f. Klikněte na **Uložit**.

### <a name="create-kendis-azure-ad-integration-test-user"></a>Vytvořit testovacího uživatele pro integraci s Kendis-Azure AD

V této části se v integraci Kendis-Azure AD vytvoří uživatel s názvem Britta Simon. Kendis – integrace služby Azure AD podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě neexistuje v integraci Kendis-Azure AD, vytvoří se nový, který se vytvoří po ověření.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL Kendis – přihlášení k integraci Azure AD, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlašování k integraci služby Azure AD a spustit tok přihlášení přímo z těchto Kendis.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k integraci Kendis-Azure AD, pro kterou jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici integrace Kendis-Azure AD v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k integraci Kendis-Azure AD, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete integraci Kendis-Azure AD, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).