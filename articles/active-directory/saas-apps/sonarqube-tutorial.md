---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s SonarQube | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SonarQube.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: f756e738989775bf9c06b44a03f002c14f42e3ac
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182321"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SonarQube

V tomto kurzu se dozvíte, jak integrovat SonarQube s Azure Active Directory (Azure AD). Když integrujete SonarQube s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SonarQube.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SonarQube svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SonarQube odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SonarQube podporuje jednotné přihlašování iniciované v **SP**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-sonarqube-from-the-gallery"></a>Přidání SonarQube z Galerie

Pokud chcete nakonfigurovat integraci SonarQube do služby Azure AD, musíte přidat SonarQube z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SonarQube** .
1. Na panelu výsledků vyberte **SonarQube** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SonarQube

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SonarQube pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SonarQube.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí SonarQube postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SONARQUBE SSO](#configure-sonarqube-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte SonarQube Test User](#create-sonarqube-test-user)** -to, abyste měli protějšek B. Simon v SonarQube, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **SonarQube** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:

    * **Pro produkční prostředí**

        `https://servicessonar.corp.microsoft.com/`

    * **Pro vývojové prostředí**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení SonarQube** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SonarQube.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SonarQube**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.

1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sonarqube-sso"></a>Konfigurace jednotného přihlašování SonarQube

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu SonarQube společnosti jako správce.

1. Klikněte na **správa > konfigurace > zabezpečení** a přejděte na **modul plug-in SAML** proveďte následující kroky.

1. Zkopírujte následující podrobnosti z metadat IdP a vložte je do odpovídajících textových polí v modulu plug-in SonarQube.
    1. ID entity IdP
    2. Přihlašovací adresa URL
    3. Certifikát X. 509 
1. Uložte všechny podrobnosti.
    ![IDP modulu plug-in SAML](./media/sonarqube-tutorial/sso-idp-metadata.png)

1. Na stránce **SAML** proveďte následující kroky:

    ![Konfigurace SonarQube](./media/sonarqube-tutorial/config01.png)

    a. Přepínač **Enabled** nastavte na **Ano**.

    b. Do textového pole **ID aplikace** zadejte název jako **SonarQube**.

    c. Do textového pole **název poskytovatele** zadejte název jako **SAML**.

    d. Do textového pole **ID poskytovatele** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **Adresa URL pro přihlášení SAML** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    f. Otevřete v programu Poznámkový blok certifikát kódovaný jako base64, zkopírujte jeho obsah a vložte ho do textového pole **certifikát poskytovatele** .

    například Do textového pole **atribut přihlášení uživatele SAML** zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    h. Do textového pole **atribut uživatelského jména SAML** zadejte hodnotu `http://schemas.microsoft.com/identity/claims/displayname` .

    i. Do textového pole **atribut e-mailu uživatele SAML** zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    j. Klikněte na **Uložit**.

### <a name="create-sonarqube-test-user"></a>Vytvořit testovacího uživatele SonarQube

V této části vytvoříte uživatele s názvem B. Simon v SonarQube. Pokud chcete přidat uživatele na platformě SonarQube, pracujte s [týmem podpory klienta SonarQube](https://www.sonarsource.com/support/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. 

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k SonarQube, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL pro přihlášení k SonarQube přímo a zahájit tok přihlášení.

3. Můžete použít panel Microsoft Access. Po kliknutí na dlaždici SonarQube na přístupovém panelu se tato akce přesměruje na přihlašovací adresu SonarQube. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

* Po nakonfigurování SonarQube můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace se rozšíří z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).