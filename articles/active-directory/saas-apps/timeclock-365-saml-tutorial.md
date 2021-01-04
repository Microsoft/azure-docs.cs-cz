---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s timeclock 365 SAML | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a timeclock 365 SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: a3b1db5c0587156e0b966a36627f0d754725d901
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97730062"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s timeclock 365 SAML

V tomto kurzu se dozvíte, jak integrovat timeclock 365 SAML s Azure Active Directory (Azure AD). Když integrujete timeclock 365 SAML s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k timeclock 365 SAML.
* Umožněte uživatelům, aby se automaticky přihlásili k timeclock 365 SAML pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné timeclock 365 SAML s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Timeclock 365 SAML podporuje jednotné přihlašování (SSO) iniciované **SP**

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>Přidání timeclock 365 SAML z Galerie

Pokud chcete nakonfigurovat integraci timeclock 365 SAML do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat timeclock 365 SAML z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **timeclock 365 SAML** .
1. Z panelu výsledků vyberte **Timeclock 365 SAML** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Konfigurace a testování jednotného přihlašování Azure AD pro timeclock 365 SAML

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí timeclock 365 SAML pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v timeclock 365 SAML.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s timeclock 365 SAML, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování **[Timeclock 365 SAML](#configure-timeclock-365-saml-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele Timeclock 365 SAML](#create-timeclock-365-saml-test-user)** – abyste měli protějšek B. Simon v TIMECLOCK 365 SAML, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **Timeclock 365 SAML** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://live.timeclock365.com/login`


1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k timeclock 365 SAML.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Timeclock 365 SAML**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-timeclock-365-saml-sso"></a>Konfigurace jednotného přihlašování SAML v timeclock 365

1. V prohlížeči otevřete novou kartu a přihlaste se k webu timeclock 365 SAML jako správce.

1. Proveďte níže uvedené kroky.

    ![Konfigurace timeclock](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. Na kartě nastavení **> nastavení vyberte > profil společnosti** .

    b. V **cestě k metadatům IDP** vložte **adresu URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    c. Pak klikněte na **aktualizovat**.

### <a name="create-timeclock-365-saml-test-user"></a>Vytvořit testovacího uživatele timeclock 365 SAML

1. V prohlížeči otevřete novou kartu a přihlaste se k webu timeclock 365 SAML jako správce.

1. Přejít na **uživatele > přidat nového uživatele**.

    ![Vytvořit test user1 ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. Zadejte všechny požadované informace na stránce **informace o uživateli** a klikněte na **Uložit**.

    ![Vytvořit test uživatel2 ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. Kliknutím na tlačítko **vytvořit** vytvořte testovacího uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení timeclock 365 SAML, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k timeclock 365 SAML přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici timeclock 365 SAML v nabídce Moje aplikace, přesměruje se na adresu URL pro přihlášení timeclock 365 SAML. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete timeclock 365 SAML, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


