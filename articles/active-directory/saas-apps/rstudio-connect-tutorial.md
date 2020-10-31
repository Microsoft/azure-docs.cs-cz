---
title: 'Kurz: Azure Active Directory integrace s ověřováním SAML v RStudio Connect | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RStudio připojit ověřování SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 23ad4347dc898f713066ea1ff061490d3eefb55b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080477"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Kurz: Azure Active Directory integrace s ověřováním SAML v RStudio Connect

V tomto kurzu se dozvíte, jak pomocí Azure Active Directory (Azure AD) integrovat ověřování SAML RStudio Connect.
Integrace ověřování SAML RStudio Connect s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k ověřování SAML RStudio Connect.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k RStudio připojení SAML (jednotné přihlašování) ke svým účtům Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s ověřováním SAML RStudio Connect, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* RStudio připojit ověřování SAML. K dispozici je [45 dne bezplatného vyhodnocení.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* RStudio Connect, ověřování SAML podporuje **aktualizace SP a IDP,** iniciované jednotné přihlašování

* RStudio Connect pro ověřování SAML podporuje zřizování uživatelů **jen v čase**

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>Přidání ověřování SAML RStudio Connect z Galerie

Pokud chcete nakonfigurovat integraci ověřování SAML RStudio Connect do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat RStudio připojit ověřování SAML z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace** .
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **RSTUDIO připojit ověřování SAML** .
1. Na panelu výsledků vyberte **RStudio Connect Authentication SAML** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro RStudio Connect pro ověřování SAML

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí RStudio Connect s ověřováním SAML pomocí testovacího uživatele s názvem **B. Simon** . Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v RStudio Connect s ověřováním SAML.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ověřování RStudio Connect SAML, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte jednotné přihlašování RStudio Connect pro ověřování SAML](#configure-rstudio-connect-saml-authentication-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    * **[Vytvořte RStudio Connect test pro ověřování SAML – uživatel](#create-rstudio-connect-saml-authentication-test-user)** , který má protějšek Britta Simon v RStudio připojit ověřování SAML, které je propojené s reprezentací uživatele v Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **ověřování SAML pro RStudio Connect** , najděte část **Správa** a vyberte **jednotné přihlašování** .
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML** .
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu spuštěném v **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky, které nahradíte `<example.com>` adresou a portem ověřovacího serveru SAML RStudio Connect.

    ![RStudio Connect doména ověřování SAML a adresy URL jednotného přihlašování](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<example.com>/__login__/saml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<example.com>/__login__/saml/acs`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Nahrávání metadat ověřování SAML RStudio Connect](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<example.com>/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Jsou určeny z adresy ověřovacího serveru SAML RStudio Connect ( `https://example.com` v předchozích příkladech). Pokud máte problémy, obraťte se na [tým podpory pro ověřování SAML v RStudio Connect](mailto:support@rstudio.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Vaše aplikace ověřování RStudio Connect SAML očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName** . RStudio Connect pro ověřování SAML očekává, že **NameIdentifier** se namapují pomocí **User. mail** , takže potřebujete upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

7. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k ověřování SAML RStudio Connect.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. V seznamu aplikace vyberte **RStudio připojit ověřování SAML** .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny** .
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>Konfigurace jednotného přihlašování RStudio Connect pro ověřování SAML

Pokud chcete nakonfigurovat jednotné přihlašování pro **RStudio připojení SAML** , musíte použít **adresu URL federačních metadat aplikace** a **adresu serveru** použitou výše. To se provádí v souboru konfigurace ověřování SAML RStudio Connect na adrese `/etc/rstudio-connect.rstudio-connect.gcfg` .

Toto je ukázkový konfigurační soubor:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Uložte **adresu serveru** do `Server.Address` hodnoty a **adresu URL federačních metadat aplikace** v `SAML.IdPMetaData` hodnotě. Všimněte si, že tato Ukázková konfigurace používá nešifrované připojení HTTP, zatímco Azure AD vyžaduje použití šifrovaného připojení HTTPS. Můžete buď použít [reverzní proxy server](https://docs.rstudio.com/connect/admin/proxy/) před ověřováním SAML RStudio Connect, nebo nakonfigurovat ověřování RSTUDIO připojení SAML tak, aby [používalo protokol HTTPS přímo](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Pokud máte potíže s konfigurací, můžete si přečíst [příručku pro Správce ověřování SAML v RStudio Connect](https://docs.rstudio.com/connect/admin/authentication/saml/) nebo poslat e-mail [týmu podpory RStudio](mailto:support@rstudio.com) .

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>Vytvořit testovacího uživatele pro ověřování SAML v RStudio Connect

V této části se v RStudio Connect pro ověřování SAML vytvoří uživatel s názvem Britta Simon. Ověřování SAML RStudio Connect podporuje zřizování za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v RStudio Connect ověřování SAML neexistuje, vytvoří se nový, když se pokusíte o přístup k ověřování SAML RStudio Connect.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k ověřování SAML RStudio Connect, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení ověřování SAML na RStudio Connect přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k ověřování RSTUDIO Connect SAML, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici ověřování RStudio připojit SAML na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k ověřování SAML RStudio Connect, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po konfiguraci ověřování RStudio Connect SAML můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

