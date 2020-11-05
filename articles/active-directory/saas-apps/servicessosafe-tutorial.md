---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s Services@SoSafe | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Services@SoSafe .
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 3fe42dd37bda3bef549570c32018179b88d06957
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93364990"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicessosafe"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Services@SoSafe

V tomto kurzu se naučíte, jak integrovat Services@SoSafe s Azure Active Directory (Azure AD). Když provádíte integraci Services@SoSafe s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Services@SoSafe .
* Umožněte uživatelům, aby se automaticky přihlásili ke Services@SoSafe svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Services@SoSafe odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Services@SoSafe podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování
* Services@SoSafepodporuje zřizování uživatelů **jenom v čase**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.


## <a name="adding-servicessosafe-from-the-gallery"></a>Přidání Services@SoSafe z Galerie

Pokud chcete nakonfigurovat integraci Services@SoSafe do služby Azure AD, musíte přidat Services@SoSafe z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte Services@SoSafe do vyhledávacího pole.
1. Vyberte Services@SoSafe z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-servicessosafe"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Services@SoSafe

Nakonfigurujte a otestujte jednotné přihlašování Azure AD Services@SoSafe pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Services@SoSafe .

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Services@SoSafe , proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SERVICESSOSAFE SSO](#configure-servicessosafe-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele ServicesSoSafe](#create-servicessosafe-test-user)** , abyste měli protějšek B. Simon v Services@SoSafe , který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na Services@SoSafe stránce integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://api.sosafe.de/v1/auth/saml/login/<TENANT_ID>`

    > [!NOTE]
    > Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [ Services@SoSafe tým podpory klienta](mailto:support@sosafe.de) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V Services@SoSafe části nastavení zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Services@SoSafe .

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte Services@SoSafe .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-servicessosafe-sso"></a>Konfigurace jednotného přihlašování ServicesSoSafe

1. V jiném okně webového prohlížeče se přihlaste k Services@SoSafe webu jako správce.

1. Klikněte na **Rozšířená data** a na následující stránce proveďte následující kroky.

    ![Rozšířená data – stránka SAML](./media/servicessosafe-tutorial/saml-settings.png)

    a. Do textového pole **ID tenanta Azure** vložte hodnotu ID tenanta z Azure Portal.

    b. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu** .

    c. Do pole **Adresa URL pro přihlášení** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Do pole **identifikátor Azure AD** vložte hodnotu **ID entity** , kterou jste zkopírovali z Azure Portal.

    e. Do pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    f. Klikněte na **Uložit** .

### <a name="create-servicessosafe-test-user"></a>Vytvořit testovacího uživatele ServicesSoSafe

V této části se v nástroji vytvoří uživatel s názvem Britta Simon Services@SoSafe . Services@SoSafe podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v nástroji ještě neexistuje Services@SoSafe , vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Services@SoSafe adresu URL pro přihlášení, kde můžete spustit tok přihlášení.  

1. Přejít na Services@SoSafe adresu URL pro přihlášení přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste být automaticky přihlášeni ke službě, Services@SoSafe pro kterou jste NAstavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na Services@SoSafe dlaždici na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k portálu, Services@SoSafe pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Services@SoSafe , můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


