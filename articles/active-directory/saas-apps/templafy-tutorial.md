---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s typu SAML2em Templafy | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Templafy typu Saml2.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: d83bf1b6e986f867a3781b5da7d2784e65815520
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515814"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-templafy-saml2"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s typu SAML2em Templafy

V tomto kurzu se dozvíte, jak integrovat Templafy typu Saml2 s Azure Active Directory (Azure AD). Když integrujete Templafy typu Saml2 s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Templafy typu Saml2.
* Umožněte uživatelům, aby se automaticky přihlásili k Templafy typu Saml2 s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Templafy typu Saml2 s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Templafy typu Saml2 podporuje jednotné přihlašování se spuštěným **SP**
* Templafy typu Saml2 podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-templafy-saml2-from-the-gallery"></a>Přidání Templafy typu Saml2 z Galerie

Pokud chcete nakonfigurovat integraci Templafy typu Saml2 do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Templafy typu Saml2 z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Templafy typu Saml2** .
1. Z panelu výsledků vyberte **TEMPLAFY typu Saml2** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-templafy-saml2"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Templafy typu Saml2

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Templafy typu Saml2 s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Templafy typu Saml2.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Templafy typu Saml2, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte TEMPLAFY typu Saml2 SSO](#configure-templafy-saml2-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořit TEMPLAFY typu Saml2 Test User](#create-templafy-saml2-test-user)** -to znamená, že má protějšek B. Simon v Templafy typu Saml2, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **typu Saml2 Templafy** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<CLIENTSUBDOMAIN>.templafy.com`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, kontaktujte [tým podpory TEMPLAFY typu Saml2 pro klienta](mailto:support@templafy.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace Templafy typu Saml2 očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho aplikace Templafy typu Saml2 očekává, že se v odpovědi SAML vrátí zpátky několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut| Obor názvů  |
    | ---------------| --------------- | --------- |
    | givenname | User. křestní jméno | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | surname | User. příjmení | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | EmailAddress | uživatel. pošta | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`
    | streetaddress | User. StreetAddress | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | city | User. City | `http://schemas.templafy.com/2016/06/identity/claims`|
    | ovládacím | User. PostalCode | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | stateorprovince | User. State | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | country | uživatel. Country | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | jobtitle | User. jobtitle | `http://schemas.templafy.com/2016/06/identity/claims`|
    | Oddělení | User. Department | `http://schemas.templafy.com/2016/06/identity/claims`|
    | PhoneNumber | User. telephoneNumber | `http://schemas.templafy.com/2016/06/identity/claims` |
    | facsimilenumber | User. facsimiletelephonenumber | `http://schemas.templafy.com/2016/06/identity/claims`|
    | názvu | User. userPrincipalName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | nameidentifier | uživatel. pošta | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Templafy typu Saml2.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **TEMPLAFY typu Saml2**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-templafy-saml2-sso"></a>Konfigurace jednotného přihlašování Templafy typu Saml2

Ke konfiguraci jednotného přihlašování na straně **TEMPLAFY typu Saml2** je potřeba odeslat **adresu URL federačních metadat aplikace** [týmu podpory Templafy typu Saml2](mailto:support@templafy.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-templafy-saml2-test-user"></a>Vytvořit testovacího uživatele Templafy typu Saml2

V této části se na Templafy typu Saml2 vytvoří uživatel s názvem B. Simon. Templafy typu Saml2 podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Templafy typu Saml2 neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Templafy typu Saml2, měli byste být automaticky přihlášeni k Templafy typu Saml2, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Templafy typu Saml2 s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)