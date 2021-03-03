---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s využitím Parkalot pro zaparkování na automobil | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory Parkalot a správu parkování na automobilu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 0098d28d2b211ad9e4bbe60a44c5de4058f2b96b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651404"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s využitím systému správy zaparkování Parkalot-auta

V tomto kurzu se dozvíte, jak integrovat správu parkování Parkalot s využitím auta pomocí Azure Active Directory (Azure AD). Když integrujete službu Parkalot pro zaparkování pomocí služby Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke správě parkování Parkalot auta.
* Umožněte uživatelům, aby se do Parkalot zaparkování pomocí svých účtů Azure AD automaticky přihlásili.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Parkalot – odběr povolený pro jednotné přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Parkalot zaparking pro správu **auta podporuje jednotné** přihlašování.

* Správa zaparkování Parkalot na automobil podporuje **jenom při** zřizování uživatelů

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>Přidání správy parkování Parkalot-auta z Galerie

Pokud chcete nakonfigurovat integraci Parkalot správy zaparkování do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Parkalot správu parkování z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Parkalot-auto Management (zaparkování** ).
1. Vyberte **Parkalot-auto Management** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Parkalot zaparking pro správu auta

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Parkalot pro správu zaparkování pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve správě Parkalot parkování.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí správy zaparkování Parkalot-auta, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné PŘIhlašování pro správu Parkalot-Car pro zaparkování](#configure-parkalot-car-park-management-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele pro správu Parkalot-Car pro parky](#create-parkalot-car-park-management-test-user)** – můžete mít protějšek B. Simon ve správě parkování Parkalot, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **pro správu zaparkování Parkalot na automobilu** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Identifikátor (ID entity) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Adresa URL odpovědi |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Přihlašovací adresa URL |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory Parkalot pro správu automobilového parku](mailto:contact-us@parkalot.io) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení správy zaparkování Parkalot-auta** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup ke správě parkování Parkalot.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Parkalot-auto Management (zaparkování**).
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-parkalot-car-park-management-sso"></a>Konfigurace jednotného přihlašování Parkalot-Car pro správu parkování

1. V jiném okně webového prohlížeče se přihlaste ke svojí Parkalot společnosti pro správu parkování na automobilu jako správce.

1. Vyberte **Nastavení SAML** a klikněte na ikonu **Upravit** na kartě **Přidat novou** kartu.

    ![Přidat novou ikonu pro úpravy](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. Na následující stránce proveďte níže uvedené kroky.

    ![Nakonfigurujte jednotné přihlašování pro správu Parkalot auta.](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. Do textového pole **Zobrazovaný název** zadejte platný název.

    b. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **URL jednotného přihlašování** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu** .

    e. Klikněte na **Uložit**.

### <a name="create-parkalot-car-park-management-test-user"></a>Vytvořit testovacího uživatele pro správu služby Parkalot-Car Park

V této části se uživatel s názvem Britta Simon vytvoří ve správě Parkalotového parkování. Parkalot-auto Management podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě neexistuje ve správě parkování Parkalot, vytvoří se nový, který se vytvoří po ověření.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na přihlašovací adresu URL pro správu Parkalot auta, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro správu zaparkování Parkalot-auto a spustit tok přihlášení přímo z tohoto účtu.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici správy parkování Parkalot na automobilu v části Moje aplikace se přesměruje na přihlašovací adresu URL pro správu Parkalot auta. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete správu parkování Parkalot, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).