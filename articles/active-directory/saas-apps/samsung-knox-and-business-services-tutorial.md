---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování se službami Samsung KNOX a Business | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Samsung KNOX a firemními službami.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 3c1ec38e792987f4bd7208c3bf57a882a05f4f46
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648046"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Kurz: Azure Active Directory integraci jednotného přihlašování se službami Samsung KNOX a Business

V tomto kurzu se dozvíte, jak integrovat služby Samsung KNOX a Business pomocí Azure Active Directory (Azure AD). Když integrujete služby Samsung KNOX a Business v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke službám Samsung KNOX a Business.
* Umožněte uživatelům, aby se automaticky přihlásili k Samsung KNOX a obchodním službám pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Účet Samsung KNOX.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Služba Samsung KNOX a Business Services podporuje jednotné přihlašování se systémem **SP**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Přidávání služeb Samsung KNOX a Business z Galerie

Pokud chcete nakonfigurovat integraci služeb Samsung KNOX a Business do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat služby Samsung KNOX a Business z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Samsung KNOX a Business Services** .
1. Na panelu výsledků vyberte **služby Samsung KNOX a Business** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Konfigurace a testování jednotného přihlašování Azure AD pro služby Samsung KNOX a Business

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Samsung KNOX a podnikových služeb pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v [SamsungKnox.com](https://samsungknox.com/).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služeb Samsung KNOX a Business, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Samsung KNOX a Business Services](#configure-samsung-knox-and-business-services-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele Samsung KNOX a Business Services](#create-samsung-knox-and-business-services-test-user)** – abyste měli protějšek B. Simon ve službách Samsung KNOX a Business, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Samsung KNOX a Business Services** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    * Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://www.samsungknox.com`
    * Do textového pole **Adresa URL odpovědi (adresa url assertion Consumer Service)** zadejte adresu URL: `https://central.samsungknox.com/ams/ad/saml/acs`
    
    ![Základní konfigurační hodnoty SAML](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke službám Samsung KNOX a Business.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Samsung KNOX a firemní služby**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-samsung-knox-and-business-services-sso"></a>Konfigurace jednotného přihlašování Samsung KNOX a Business Services

1. V jiném okně webového prohlížeče se přihlaste k [SamsungKnox.com](https://samsungknox.com/) jako správce.

1. Klikněte na **avatar** v pravém horním rohu.

    ![Miniatura Samsung KNOX](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. Na levém bočním panelu klikněte na **nastavení služby Active Directory** a proveďte následující kroky.

    ![NASTAVENÍ SLUŽBY ACTIVE DIRECTORY](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. Do textového pole **identifikátor (ID entity)** vložte hodnotu **identifikátoru** , kterou jste zadali v Azure Portal.

    b. V poli **Adresa URL federačních metadat aplikace** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    c. Klikněte na **připojit se k AD jednotné přihlašování**.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Vytvoření testovacího uživatele pro služby Samsung KNOX a firmy

V této části vytvoříte uživatele s názvem Britta Simon v Samsung KNOX a Business Services. Pokyny, jak pozvat správce nebo otestovat uživatele do vaší organizace Samsung KNOX, najdete v průvodci pro správce registrace pro [Knox nakonfigurované](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm) nebo [Knox](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na [SamsungKnox.com](https://samsungknox.com/), kde můžete spustit tok přihlášení. 

* Přejít na [SamsungKnox.com](https://samsungknox.com/) přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici Samsung KNOX and Business Services v části Moje aplikace se přesměruje na [SamsungKnox.com](https://samsungknox.com/). Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Samsung KNOX a obchodní služby, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).