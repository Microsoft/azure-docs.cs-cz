---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Kendis-Scalingou agilní platformou | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kendis-Scaling agilní platformou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: e02ff4926897fafc72e1a5081366faad5d2f03ba
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509774"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis-scaling-agile-platform"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Kendis-Scaling agilní platformou

V tomto kurzu se naučíte, jak integrovat Kendis-Scaling agilní platformy pomocí Azure Active Directory (Azure AD). Když integrujete Kendis-Scaling agilní platformu s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Kendis-Scaling agilní platformě.
* Umožněte uživatelům, aby se automaticky přihlásili, aby Kendis-Scaling agilní platformu s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Kendis-Scaling předplatného jednotného přihlašování (SSO) pro agilní platformu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kendis-Scaling agilní platforma podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* Kendis-Scaling agilní platformy podporují zřizování uživatelů **jenom v čase** .


## <a name="adding-kendis-scaling-agile-platform-from-the-gallery"></a>Přidání Kendis-Scaling agilní platformy z Galerie

Pokud chcete nakonfigurovat integraci Kendis-Scalingch agilních platforem do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Kendis-Scaling agilní platformu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Kendis – velikost agilní platformy** .
1. Na panelu výsledků vyberte **Kendis-škálovat agilní platformu** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-kendis-scaling-agile-platform"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Kendis-Scaling agilní platformy

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Kendis-Scaling agilní platformy pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Kendis-Scaling agilní platformě.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Kendis-Scaling agilní platformy, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Kendis-Scaling jednotné PŘIhlašování pro agilní platformu](#configure-kendis-scaling-agile-platform-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Kendis-Scaling testovacího uživatele agilní platformy](#create-kendis-scaling-agile-platform-test-user)** – abyste měli protějšek B. Simon v Kendis-Scaling agilní platformě, která je propojená s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikací **agilní platformy Kendis** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.kendis.io`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [Kendis se škálováním týmu podpory pro agilní platformu](mailto:support@kendis.io) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit Kendis-Scaling agilní platforma** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Kendis-Scaling agilní platformě.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Kendis – škálování agilní platformy**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-kendis-scaling-agile-platform-sso"></a>Konfigurace Kendis-Scaling jednotného přihlašování k agilní platformě

1. V jiném okně webového prohlížeče se přihlaste ke svému Kendis-Scalingmu webu společnosti agilní platformy jako správce.

1. Přejít na **nastavení > konfigurace SAML**.

    ![nastavení pro konfigurace SAML](./media/kendis-scaling-agile-platform-tutorial/settings.png)

1. V dolní části stránky klikněte na tlačítko **Upravit** a proveďte následující kroky.

    ![Konfigurace SAML](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Zkopírujte hodnotu **adresy URL zpětného volání** , vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v části základní konfigurace SAML v Azure Portal.

    b. V textovém poli **Adresa URL jednotného přihlašování zprostředkovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c.  Do textového pole **vystavitele zprostředkovatele identity** vložte hodnotu **identifikátoru (ID entity) Azure AD** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu X. 509** .

    e. V seznamu možností **Vyberte možnost Výchozí skupina** . 

    f. Klikněte na **Uložit**.

### <a name="create-kendis-scaling-agile-platform-test-user"></a>Vytvořit Kendis-Scaling testovacího uživatele agilní platformy

V této části se v Kendis-Scaling agilní platforma vytvoří uživatel s názvem Britta Simon. Kendis-Scaling agilní platforma podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Kendis-Scaling agilní platformě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Kendis-Scaling adresa URL pro přihlašování agilní platformy, kde můžete spustit tok přihlášení.  

* Přejít na Kendis-Scaling adresu URL pro přihlašování agilní platformy přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Kendis-Scaling agilní platformě, pro kterou jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Kendis-Scaling agilní platformy v okně moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud jste nakonfigurovali v režimu IDP, měli byste se automaticky přihlásit k Kendis-Scaling agilní platformě, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Kendis-Scaling agilní platformu, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


