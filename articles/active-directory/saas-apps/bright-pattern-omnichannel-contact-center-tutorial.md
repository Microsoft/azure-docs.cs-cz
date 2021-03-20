---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s jasným vzorem kontaktního centra Omnichannel | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jasným vzorem Omnichannel kontaktní centrum.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.openlocfilehash: 9929dc3dbe5870fbe1a5047fbc3fc552e32aba59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456687"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bright-pattern-omnichannel-contact-center"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s jasným vzorem Omnichannel kontaktního centra

V tomto kurzu se dozvíte, jak pomocí Azure Active Directory (Azure AD) integrovat "jasného vzoru Omnichannel Contact Center. Při integraci jasného vzoru Omnichannel kontakt Center s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k jasnému vzoru Omnichannel kontaktní centrum.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k jasnému vzoru Omnichannel kontaktního centra pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Jasný vzor Omnichannel kontaktního centra pro jednotné přihlašování (SSO) s povoleným předplatným.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.



* Jasný vzor Omnichannel kontakt Center podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování
* Jasný vzor Omnichannel kontakt Center podporuje **jenom při** zřizování uživatelů


## <a name="adding-bright-pattern-omnichannel-contact-center-from-the-gallery"></a>Přidání jasného vzoru Omnichannel kontakt Center z Galerie

Pokud chcete nakonfigurovat integraci jasného vzoru Omnichannel kontaktního centra do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Omnichannel centrum kontaktů z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **jasný vzor Omnichannel kontakt Center** .
1. Z panelu výsledků vyberte **jasný vzorek Omnichannel kontakt Center** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bright-pattern-omnichannel-contact-center"></a>Konfigurace a testování jednotného přihlašování Azure AD pro jasné vzory Omnichannel kontaktního centra

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s jasným vzorem Omnichannel kontaktů centra pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Omnichannel kontaktní centrum v jasném vzoru.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s jasným vzorem Omnichannel Contact Center, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jasného vzoru Omnichannel kontakt Center SSO](#configure-bright-pattern-omnichannel-contact-center-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořit jasný vzor Omnichannel kontaktního centra kontaktů](#create-bright-pattern-omnichannel-contact-center-test-user)** – Chcete-li mít protějšek B. Simon v jasném vzorci Omnichannel kontaktní centrum, které je propojeno s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce Omnichannel ( **světlého vzoru) centra kontaktů** pro aplikace centra, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<SUBDOMAIN>_sso`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.brightpattern.com/agentdesktop/sso/redirect`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.brightpattern.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, kontaktuje [tým technické podpory Omnichannel o kontaktech na jasné vzory](mailto:support@brightpattern.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace Omnichannel Contact Center pro jasné vzory očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace Omnichannel Contact Center v jasném vzoru, že se v odpovědi SAML vrátí několik atributů, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Obor názvů  |
    | ---------------| --------------- |
    | firstName | User. křestní jméno |
    | lastName | User. příjmení |
    | e-mail | uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit jasný vzor Omnichannel kontaktů centra** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k jasnému vzoru Omnichannel kontaktnímu centru.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **jasný vzor Omnichannel kontakt Center**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-bright-pattern-omnichannel-contact-center-sso"></a>Konfigurace jasného vzoru Omnichannel kontaktního centra SSO

Chcete-li nakonfigurovat jednotné přihlašování na straně **Omnichannel kontaktů na jasném vzoru** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal na [tým technické podpory Omnichannel na střed](mailto:support@brightpattern.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-bright-pattern-omnichannel-contact-center-test-user"></a>Vytvořit jasný vzor Omnichannel kontakt Center Test User

V této části se vytvoří uživatel s názvem B. Simon ve světle světlého vzoru Omnichannel Contact Center. Jasný vzor Omnichannel kontakt Center podporuje zřizování uživatelů za běhu, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Omnichannel kontaktu v jasném vzorci neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Omnichannel (světlého vzoru) kontaktu na přístupovém panelu byste měli být automaticky přihlášeni k jasnému vzoru Omnichannel kontaktnímu softwaru, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte jasný vzor Omnichannel kontakt Center s Azure AD](https://aad.portal.azure.com/)