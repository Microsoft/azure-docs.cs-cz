---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Samsara | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Samsara.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: jeedes
ms.openlocfilehash: b864f4204fa546fa1f06e50550376a8a899d5b8c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348400"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsara"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Samsara

V tomto kurzu se dozvíte, jak integrovat Samsara s Azure Active Directory (Azure AD). Když integrujete Samsara s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Samsara.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Samsara svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Samsara odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Samsara podporuje **jednotné** přihlašování (SSO) a **IDP** .
* Samsara podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-samsara-from-the-gallery"></a>Přidání Samsara z Galerie

Pokud chcete nakonfigurovat integraci Samsara do služby Azure AD, musíte přidat Samsara z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)
    
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Samsara** .

     ![OneTrust software pro správu ochrany osobních údajů v seznamu výsledků](common/search-new-app.png)

1. Na panelu výsledků vyberte **Samsara** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-samsara"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Samsara

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Samsara pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Samsara.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Samsara postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SAMSARA SSO](#configure-samsara-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Samsara Test User](#create-samsara-test-user)** -to, abyste měli protějšek B. Simon v Samsara, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Samsara** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://cloud.samsara.com/signin/<ORGID>` pro zákazníky v cloudu USA `https://cloud.eu.samsara.com/signin/<ORGID>` pro zákazníky v rámci EU.

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `urn:auth0:samsara-dev:samlp-orgid-<ORGID>`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://samsara-dev.auth0.com/login/callback?connection=samlp-orgid-<ORGID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, adresy URL odpovědi a identifikátoru. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta Samsara](mailto:support@samsara.com) , nebo v Samsara v části **Nastavení**  >  **jednotného přihlašování**pro  >  **nové připojení SAML** získáte \<ORGID\> . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Samsara** zkopírujte **adresu URL pro přihlášení** .

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)
    
### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Samsara.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Samsara**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-samsara-sso"></a>Konfigurace jednotného přihlašování Samsara

Ke konfiguraci jednotného přihlašování na straně **Samsara** je potřeba odeslat stažený **certifikát (Base64)** a **přihlašovací adresu URL** z Azure Portal do [týmu podpory Samsara](mailto:support@samsara.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-samsara-test-user"></a>Vytvořit testovacího uživatele Samsara

V této části se v Samsara vytvoří uživatel s názvem B. Simon. Samsara podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel už v Samsara neexistuje, vytvoří se nový a po ověření se vytvoří výchozí role standardního správce (bez přístupu k žádnému přerušovanému vačku) pro organizaci. Přístup uživatele se pak může v Samsara zvýšit nebo snížit podle potřeby.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Samsara, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL pro přihlášení k Samsara přímo a zahájit tok přihlášení.

3. Můžete použít panel Microsoft Access. Po kliknutí na dlaždici Samsara na přístupovém panelu se tato akce přesměruje na přihlašovací adresu Samsara. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Samsara můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


