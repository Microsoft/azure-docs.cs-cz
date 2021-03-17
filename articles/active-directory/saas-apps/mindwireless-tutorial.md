---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s mindWireless | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a mindWireless.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: jeedes
ms.openlocfilehash: 7ad7676981173400c4c5f13ec276d0c376ac704f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518938"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mindwireless"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s mindWireless

V tomto kurzu se dozvíte, jak integrovat mindWireless s Azure Active Directory (Azure AD). Když integrujete mindWireless s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k mindWireless.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k mindWireless svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* mindWireless odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* mindWireless podporuje jednotné přihlašování **IDP** .

## <a name="adding-mindwireless-from-the-gallery"></a>Přidání mindWireless z Galerie

Pokud chcete nakonfigurovat integraci mindWireless do služby Azure AD, musíte přidat mindWireless z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **mindWireless** .
1. Na panelu výsledků vyberte **mindWireless** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mindwireless"></a>Konfigurace a testování jednotného přihlašování Azure AD pro mindWireless

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí mindWireless pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v mindWireless.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí mindWireless, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte MINDWIRELESS SSO](#configure-mindwireless-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte mindWireless Test User](#create-mindwireless-test-user)** -to, abyste měli protějšek B. Simon v mindWireless, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **MindWireless** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.mwsmart.com/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta mindWireless](mailto:sdulloor@mindwireless.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. mindWireless aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace mindWireless několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Obor názvů  |  Zdrojový atribut|
    | -------------- | --------------- | ----------------|
    | ID zaměstnance | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`| User. ČísloZaměstnance |

    > [!NOTE]
    > Název deklarace identity musí být vždycky **ID zaměstnance** a hodnota, kterou jsme namapovali na **User. ČísloZaměstnance**, který obsahuje zaměstnance uživatele. Tady se mapování uživatele z Azure AD na mindWireless provádí v poli ČísloZaměstnance, ale můžete ho namapovat na jinou hodnotu, a to na základě nastavení aplikace. Pomocí [týmu podpory mindWireless](mailto:sdulloor@mindwireless.com) můžete nejprve pracovat se správným identifikátorem uživatele a mapovat tuto hodnotu s DEKLARACÍ identity **ID zaměstnance** .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení mindWireless** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k mindWireless.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **mindWireless**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-mindwireless-sso"></a>Konfigurace jednotného přihlašování mindWireless

Ke konfiguraci jednotného přihlašování na straně **mindWireless** je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory mindWireless](mailto:sdulloor@mindwireless.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-mindwireless-test-user"></a>Vytvořit testovacího uživatele mindWireless

V této části vytvoříte uživatele s názvem B. Simon v mindWireless. Pokud chcete přidat uživatele na platformě mindWireless, pracujte s [týmem podpory mindWireless](mailto:sdulloor@mindwireless.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici mindWireless, měli byste se automaticky přihlásit k mindWireless, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si mindWireless s Azure AD](https://aad.portal.azure.com/)