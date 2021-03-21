---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s RStudio serverem pro | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RStudio serverem pro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 490ecb201b91cdbdcdddceecdd2d145d2f9bb815
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390041"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rstudio-server-pro"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s RStudio serverem pro

V tomto kurzu se dozvíte, jak integrovat RStudio Server pro (RSP) s Azure Active Directory (Azure AD). Když integrujete RSP s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k RSP.
* Umožněte uživatelům, aby se automaticky přihlásili k RSP pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Instalace RSP (verze >= 1,4).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* RSP podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování

## <a name="adding-rstudio-server-pro-from-the-gallery"></a>Přidání serveru RStudio pro z Galerie

Chcete-li nakonfigurovat integraci RSP do služby Azure AD, je nutné přidat ověřování RStudio serveru pro SAML z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **RSTUDIO Server pro SAML Authentication** .
1. Na panelu výsledků vyberte **ověřování RStudio serveru pro SAML** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-server-pro"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro RStudio Server pro

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí RSP pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v RSP.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ověřování RStudio serveru pro SAML, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte RStudio Server pro SSO](#configure-rstudio-server-pro-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele serveru RStudio Server pro](#create-rstudio-server-pro-test-user)** , abyste měli protějšek B. Simon v RStudio serveru pro, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **ověřování SAML pro RStudio Server pro** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<RSP-SERVER>/<PATH>/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<RSP-SERVER>/<PATH>/saml/acs`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<RSP-SERVER>/<PATH>/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem URI instalace RSP. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k ověřování SAML RStudio serveru pro.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **ověřování RStudio Server pro SAML**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-rstudio-server-pro-sso"></a>Konfigurace jednotného přihlašování RStudio serveru pro

1. Aktualizujte konfigurační soubor RSP `/etc/rstudio/rserver.conf` následujícím způsobem:

    ```
    auth-saml=1
    auth-saml-metadata-url=<federation-metadata-URI>
    auth-saml-sp-name-id-format=emailaddress
    auth-saml-sp-attribute-username=NameID
    auth-saml-sp-base-uri=<RSP-Server-URI>
    ```

2. Restartujte RSP spuštěním následujícího:

    ```
    sudo rstudio-server restart
    ```

### <a name="create-rstudio-server-pro-test-user"></a>Vytvořit testovacího uživatele serveru RStudio Server pro

Všichni uživatelé, kteří mají použít RSP, musí být zřízeni na serveru. Uživatele můžete vytvořit pomocí `useradd` `adduser` příkazu nebo.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení ověřování RStudio serveru pro SAML, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení pro ověřování SAML na RStudio Server pro, přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k ověřování SAML serveru pro RStudio, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici ověřování RStudio serveru pro SAML na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k ověřování SAML serveru RStudio Server pro, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po konfiguraci ověřování RStudio serveru pro SAML můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
