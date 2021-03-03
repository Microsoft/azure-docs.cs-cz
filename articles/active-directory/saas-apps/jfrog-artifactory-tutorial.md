---
title: 'Kurz: Azure Active Directory integrace s JFrog Artifactory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a JFrog Artifactory.
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
ms.openlocfilehash: b943be684d84e1e193d9318e9f1c6423dcd38795
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648913"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Kurz: integrace JFrog Artifactory s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat JFrog Artifactory s Azure Active Directory (Azure AD). Když integrujete JFrog Artifactory s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k JFrog Artifactory.
* Umožněte uživatelům, aby se automaticky přihlásili k JFrog Artifactory s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné JFrog Artifactory s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* JFrog Artifactory podporuje **SP a IDP** iniciované jednotného přihlašování.
* JFrog Artifactory podporuje **jenom při** zřizování uživatelů.

## <a name="add-jfrog-artifactory-from-the-gallery"></a>Přidání JFrog Artifactory z Galerie

Pokud chcete nakonfigurovat integraci JFrog Artifactory do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat JFrog Artifactory z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **JFrog Artifactory** .
1. Z panelu výsledků vyberte **JFrog Artifactory** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>Konfigurace a testování jednotného přihlašování Azure AD pro JFrog Artifactory

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí JFrog Artifactory s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v JFrog Artifactory.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí JFrog Artifactory, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte JFrog ARTIFACTORY SSO](#configure-jfrog-artifactory-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit JFrog Artifactory Test User](#create-jfrog-artifactory-test-user)** -to znamená, že má protějšek B. Simon v JFrog Artifactory, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Artifactory JFrog** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<servername>.jfrog.io`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:
    
    - Pro Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Pro Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:
    - Pro Artifactory 6. x: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Pro Artifactory 7. x: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta podpory JFrog Artifactory](https://support.jfrog.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace JFrog Artifactory očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na ikonu **Upravit** otevřete dialogové okno atributy uživatele.

    ![Snímek obrazovky ukazuje atributy uživatele s ovládacím prvkem pro úpravy, který se vyvolal.](common/edit-attribute.png)

1. Kromě výše uvedeného JFrog Artifactory očekává, že se v odpovědi SAML vrátí několik dalších atributů. V dialogovém okně deklarace identity v části **atributy uživatele & deklarace** v dialogu **deklarace skupiny (Preview)** proveďte následující kroky:

    a. Klikněte na **pero** vedle **skupin vrácených v deklaraci identity**.

    ![Snímek obrazovky zobrazuje atributy uživatele & deklarací identity s vybranou ikonou pro úpravy.](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![Snímek obrazovky ukazuje oddíl deklarace skupiny se všemi vybranými skupinami.](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. V seznamu přepínačů vyberte **všechny skupiny** .

    c. Klikněte na **Uložit**.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyhledejte v části **podpisový certifikát SAML** **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Nakonfigurujte Artifactory (název poskytovatele služeb SAML) pomocí pole identifikátor (viz krok 4). V části **Nastavení Artifactory JFrog** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   - Pro Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Pro Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k JFrog Artifactory.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **JFrog Artifactory**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-jfrog-artifactory-sso"></a>Konfigurace jednotného přihlašování JFrog Artifactory

Ke konfiguraci jednotného přihlašování na straně **JFrog Artifactory** je potřeba odeslat stažený **certifikát (RAW)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory JFrog Artifactory](https://support.jfrog.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-jfrog-artifactory-test-user"></a>Vytvořit testovacího uživatele JFrog Artifactory

V této části se na JFrog Artifactory vytvoří uživatel s názvem B. Simon. JFrog Artifactory podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v JFrog Artifactory neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL JFrog Artifactory, kde můžete spustit tok přihlášení.  

* Přejít přímo na adresu URL pro přihlášení k JFrog Artifactory a zahajte tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Artifactoryu JFrog, pro kterou jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici JFrog Artifactory v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Artifactory JFrog, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování JFrog Artifactory můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).