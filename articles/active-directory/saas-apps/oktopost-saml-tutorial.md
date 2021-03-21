---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Oktopost SAML | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Oktopost SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: e71f991037c0d725a2a03ecd8c1ff4ebbd4d7810
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735924"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oktopost-saml"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Oktopost SAML

V tomto kurzu se dozvíte, jak integrovat Oktopost SAML pomocí Azure Active Directory (Azure AD). Když integrujete Oktopost SAML s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Oktopost SAML.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Oktopost SAML pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Oktopost předplatného jednotného přihlašování (SSO) s podporou SAML.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Oktopost SAML podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.


## <a name="adding-oktopost-saml-from-the-gallery"></a>Přidání Oktopost SAML z Galerie

Pokud chcete nakonfigurovat integraci Oktopost SAML do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Oktopost SAML z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Oktopost SAML** .
1. Na panelu výsledků vyberte **OKTOPOST SAML** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-oktopost-saml"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Oktopost SAML

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Oktopost SAML pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Oktopost SAML.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Oktopost SAML, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte OKTOPOST SSO pro SAML](#configure-oktopost-saml-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Oktopost testovacího uživatele SAML](#create-oktopost-saml-test-user)** – abyste měli protějšek B. Simon ve Oktopost SAML, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **OKTOPOST SAML** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.oktopost.com/auth/login`


1. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení OKTOPOST SAML** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Oktopost SAML.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **OKTOPOST SAML**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-oktopost-saml-sso"></a>Konfigurace Oktopost SSO jednotného přihlašování SAML

1. Přihlaste se k Oktopost SAML jako správce.

1. Klikněte na **ikonu uživatele > nastavení**.

    ![Oktopost nastavení SAML](./media/oktopost-saml-tutorial/settings.png)

1. V **Nastavení** přejdete na stránku **zabezpečení > jednotné přihlašování** a proveďte následující kroky.

    ![Oktopost konfigurace SAML](./media/oktopost-saml-tutorial/configure-sso.png)

    a. Pokud **Ano**, vyberte **Povolit jednotné přihlašování** .

    b. Do textového pole **koncového bodu SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu X. 509** .
    
    e. Klikněte na **Uložit**.

### <a name="create-oktopost-saml-test-user"></a>Vytvořit testovacího uživatele Oktopost SAML

1. Přihlaste se k Oktopost SAML jako správce.

1. Klikněte na **ikonu uživatele > nastavení**.

    ![Oktopost SAML test user1](./media/oktopost-saml-tutorial/settings.png)

1.  Přejít na uživatele **> správy uživatelů > přidat uživatele**

    ![Oktopost SAML – test uživatel2](./media/oktopost-saml-tutorial/add-user-1.png)

1. Vyplňte požadovaná pole v překryvném okně a klikněte na **Odeslat**.

    ![User3 testu Oktopost SAML](./media/oktopost-saml-tutorial/add-user-2.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Oktopost adresu URL pro přihlášení SAML, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Oktopost SAML přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Oktopost SAML, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Oktopost SAML v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Oktopost SAML, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Oktopost SAML můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).