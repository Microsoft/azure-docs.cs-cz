---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s cloudem ABBYY FlexiCapturee | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cloudem ABBYY FlexiCapture.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 0fdcf3266893990572e5c44ad8bb03ff08b69305
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548648"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-abbyy-flexicapture-cloud"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s cloudem ABBYY FlexiCapture

V tomto kurzu se dozvíte, jak integrovat Cloud ABBYY FlexiCapture s Azure Active Directory (Azure AD). Když integrujete Cloud ABBYY FlexiCapture ve službě Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke cloudu ABBYY FlexiCapture
* Umožněte vašim uživatelům, aby se do cloudu ABBYY FlexiCapture automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ABBYY FlexiCapture Cloud s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloud ABBYY FlexiCapture podporuje jednotné přihlašování (SSO) **a IDP** .
* Cloud ABBYY FlexiCapture podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-abbyy-flexicapture-cloud-from-the-gallery"></a>Přidání cloudu ABBYY FlexiCapture z Galerie

Pokud chcete nakonfigurovat integraci cloudu ABBYY FlexiCapture do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Cloud ABBYY FlexiCapture z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace** .
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ABBYY FlexiCapture Cloud** .
1. Z panelu výsledků vyberte **ABBYY FlexiCapture Cloud** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-abbyy-flexicapture-cloud"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Cloud ABBYY FlexiCapture

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí služby ABBYY FlexiCapture Cloud s použitím testovacího uživatele s názvem **B. Simon** . Aby jednotné přihlašování fungovalo, musíte v cloudu ABBYY FlexiCapture vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudu ABBYY FlexiCapture, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace aplikace ABBYY FlexiCapture cloud SSO](#configure-abbyy-flexicapture-cloud-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte si uživatele cloudového testu ve službě ABBYY FlexiCapture](#create-abbyy-flexicapture-cloud-test-user)** , abyste měli protějšek B. Simon v cloudu společnosti ABBYY FlexiCapture, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace **cloudových aplikací ABBYY FlexiCapture** najděte část **Správa** a vyberte **jednotné přihlašování** .
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML** .
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.flexicapture.com/FlexiCapture12/Login/<TENANT_NAME>/AccessToken/Saml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.flexicapture.com/FlexiCapture12/Login/<TENANT_NAME>/AccessToken/Saml`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.flexicapture.com/FlexiCapture12/Login/<TENANT_NAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory cloudového klienta společnosti ABBYY FlexiCapture](mailto:support@abbyy.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení cloudu pro ABBYY FlexiCapture** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke cloudu služby ABBYY FlexiCapture.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. V seznamu aplikace vyberte **Cloud ABBYY FlexiCapture Cloud** .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny** .
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-abbyy-flexicapture-cloud-sso"></a>Konfigurace aplikace ABBYY FlexiCapture cloud SSO

Ke konfiguraci jednotného přihlašování na straně **cloudu společnosti ABBYY FlexiCapture** je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory ABBYY FlexiCapture Cloud Support](mailto:support@abbyy.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-abbyy-flexicapture-cloud-test-user"></a>Vytvořit uživatele cloudového testu pro ABBYY FlexiCapture

V této části se v cloudu ABBYY FlexiCapture vytvoří uživatel s názvem Britta Simon. Cloud ABBYY FlexiCapture podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v cloudu ABBYY FlexiCapture neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL cloudového přihlášení ABBYY FlexiCapture, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení do cloudu ABBYY FlexiCapture přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni ke cloudu ABBYY FlexiCapture, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici cloudu ABBYY FlexiCapture na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit ke cloudu FlexiCapture společnosti ABBYY, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování cloudu ABBYY FlexiCapture můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


