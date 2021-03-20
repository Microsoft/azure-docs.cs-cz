---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s TIMU | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TIMU.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: 903150a199bac1189274cadc9755b38873cde73e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182100"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timu"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s TIMU

V tomto kurzu se dozvíte, jak integrovat TIMU s Azure Active Directory (Azure AD). Když integrujete TIMU s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k TIMU.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k TIMU svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* TIMU odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TIMU podporuje jednotné přihlašování iniciované v **SP**

* TIMU podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-timu-from-the-gallery"></a>Přidání TIMU z Galerie

Pokud chcete nakonfigurovat integraci TIMU do služby Azure AD, musíte přidat TIMU z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **TIMU** .
1. Na panelu výsledků vyberte **TIMU** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-timu"></a>Konfigurace a testování jednotného přihlašování Azure AD pro TIMU

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí TIMU pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TIMU.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí TIMU postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE TIMU SSO](#configure-timu-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte TIMU Test User](#create-timu-test-user)** -to, abyste měli protějšek B. Simon v TIMU, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **TIMU** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte jednu z těchto adres URL:

    | Přihlašovací adresa URL |
    |-------------|
    | `https://auth.timu.com` |
    | `https://auth.timu.life` |
    |

    b. Do textového pole **identifikátor (ID entity)** zadejte jeden z následujících vzorů:
    
    | Identifikátor |
    |-------------|
    | `https://<SUBDOMAIN>.timu.com/api/login/saml/callback` |
    | `https://<SUBDOMAIN>.timu.life/api/login/saml/callback`|
    |

    c. Do textového pole **Adresa URL odpovědi** zadejte jeden z následujících vzorů:
    
    | Adresa URL odpovědi |
    |-------------|
    | `https://<SUBDOMAIN>.timu.com/api/login/saml/callback` |
    | `https://<SUBDOMAIN>.timu.life/api/login/saml/callback`|
    |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta TIMU](mailto:support@timu.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TIMU.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **TIMU**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-timu-sso"></a>Konfigurace jednotného přihlašování TIMU

Pokud chcete nakonfigurovat jednotné přihlašování na straně **TIMU** , musíte odeslat **adresu URL federačních metadat aplikace** [týmu podpory TIMU](mailto:support@timu.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-timu-test-user"></a>Vytvořit testovacího uživatele TIMU

V této části se v TIMU vytvoří uživatel s názvem Britta Simon. TIMU podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v TIMU neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k TIMU, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k TIMU přímo a zahájit tok přihlášení.

* Můžete použít panel Microsoft Access. Po kliknutí na dlaždici TIMU na přístupovém panelu se tato akce přesměruje na přihlašovací adresu TIMU. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování TIMU můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).