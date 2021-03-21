---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s růstem operačního systému Synerise AI | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a růstovým operačním systémem pro Synerise AI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: jeedes
ms.openlocfilehash: 0ffc6543a2597e39470c0d4aff86e7d5a1631d69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736622"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-synerise-ai-growth-operating-system"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s růstovým operačním systémem Synerise AI

V tomto kurzu se dozvíte, jak integrovat Synerise s Azure Active Directory (Azure AD). Když integrujete Synerise s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Synerise.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Synerise svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Synerise odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Synerise podporuje jednotné přihlašování (SSO) **a IDP** .
* Synerise podporuje zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.


## <a name="adding-synerise-ai-growth-operating-system-from-the-gallery"></a>Přidání nárůstu operačního systému Synerise AI z Galerie

Pokud chcete nakonfigurovat integraci Synerise do služby Azure AD, musíte přidat Synerise z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Synerise AI růstový operační systém** .
1. Z panelu výsledků vyberte **SYNERISE AI** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-synerise-ai-growth-operating-system"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro růstový operační systém Synerise AI

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Synerise pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Synerise.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Synerise postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování k operačnímu systému SYNERISE AI](#configure-synerise-ai-growth-operating-system-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele operačního systému SYNERISE AI](#create-synerise-ai-growth-operating-system-test-user)** , aby bylo možné mít protějšek B. Simon v Synerise, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikace s **růstem operačního systému SYNERISE AI** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a přihlašovací adresy URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory Synerise](mailto:support@synerise.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Synerise** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Synerise.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **růstový operační systém SYNERISE AI**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-synerise-ai-growth-operating-system-sso"></a>Nakonfigurovat jednotné přihlašování Synerise AI pro operační systém

1. Přihlaste se k Synerise jako správce.

1. Přejít na **Access Control nastavení >**.

    ![Nastavení Synerise](./media/synerise-ai-growth-ecosystem-tutorial/settings.png)

1. Na stránce **Access Control** klikněte na tlačítko **Zobrazit** na kartě **jednotného přihlašování** .

    ![Synerise Access Control](./media/synerise-ai-growth-ecosystem-tutorial/single-sign-on.png)

1. Na následující stránce proveďte následující kroky.

    ![Konfigurace Synerise](./media/synerise-ai-growth-ecosystem-tutorial/configuration.png)

    a. Do textového pole **ID entity poskytovatele identifikátorů** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **SSO Endpoint (https)** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **ID aplikace zprostředkovatele identity** vložte hodnotu **ID aplikace** .

    d. Zkopírujte hodnotu **identifikátoru URI přesměrování poskytovatele služby** , vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v části základní konfigurace SAML v Azure Portal.

    e. Ve **vazbě požadavku** vyberte **Přesměrování protokolu HTTP** .

    f. Přepněte na **podpis žádosti**.

    například Nahrajte stažený soubor **certifikátu (Base64)** do **certifikátu podpisu zprostředkovatele identity**.

    i. Klikněte na **použít**.

### <a name="create-synerise-ai-growth-operating-system-test-user"></a>Vytvořit testovacího uživatele operačního systému Synerise AI

V této části se v Synerise vytvoří uživatel s názvem Britta Simon. Synerise podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Synerise neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Synerise, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Synerise přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Synerise, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Synerise v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Synerise, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Synerise můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).