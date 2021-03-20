---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Splashtop | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Splashtop.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: b6dda20487caf6fe3ba49578cfdc0b65434a8dfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520553"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Splashtop

V tomto kurzu se dozvíte, jak integrovat Splashtop s Azure Active Directory (Azure AD). Když integrujete Splashtop s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Splashtop.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Splashtop svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Splashtop odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Splashtop podporuje jednotné přihlašování iniciované v **SP**

* Po nakonfigurování Splashtop můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Přidání Splashtop z Galerie

Pokud chcete nakonfigurovat integraci Splashtop do služby Azure AD, musíte přidat Splashtop z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Splashtop** .
1. Na panelu výsledků vyberte **Splashtop** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Splashtop

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Splashtop pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Splashtop.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Splashtop, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SPLASHTOP SSO](#configure-splashtop-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Splashtop Test User](#create-splashtop-test-user)** -to, abyste měli protějšek B. Simon v Splashtop, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Splashtop** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://my.splashtop.com/login/sso`

1. Splashtop aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, zatímco **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Aplikace TicketManager očekává, že **NameIdentifier** budou mapovány pomocí **User. mail**, takže potřebujete upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Splashtop** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Splashtop.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Splashtop**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-splashtop-sso"></a>Konfigurace jednotného přihlašování Splashtop

V této části budete muset požádat o novou metodu jednotného přihlašování z [webového portálu Splashtop](https://my.splashtop.com/login).
1. Na webovém portálu Splashtop přejděte na kartu tým **informace o účtu**  /   , přejděte dolů k části Vyhledání **jednotného přihlašování** . Pak klikněte na **použít u nové metody jednotného přihlašování**.

    ![Snímek obrazovky se zobrazí na stránce jednotného přihlašování, kde můžete vybrat použít pro nové metody S S-O.](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. V okně pro použití zadejte **jméno jednotného přihlašování**. Například nový Azure, vyberte **Azure** jako typ IDP a vložte **přihlašovací adresu URL** a **identifikátor Azure AD** zkopírovaný z aplikace Splashtop na Azure Portal.

    ![Snímek obrazovky se zobrazí na stránce použít pro s S/O metoda, kde můžete zadat název a další informace.](media/splashtop-tutorial/azure-sso-1.png)

1. V části informace o certifikátu klikněte pravým tlačítkem na soubor certifikátu stažený z aplikace Splashtop na Azure Portal, upravte ho pomocí poznámkového bloku a pak zkopírujte obsah a vložte ho do pole **Stáhnout certifikát (Base64)** .

    ![Snímek obrazovky zobrazující výběr souboru certifikátu a jeho otevření v poznámkovém bloku](media/splashtop-tutorial/cert-1.png)
    ![Snímek obrazovky zobrazuje obsah souboru certifikátu.](media/splashtop-tutorial/cert-2.png)
    ![Snímek obrazovky se zobrazí v textovém poli stáhnout certifikát.](media/splashtop-tutorial/azure-sso-2.png)

1. A to je vše! Klikněte na **Uložit** a Splashtop ověřovací tým jednotného přihlašování vám pošle informace o ověření a pak aktivuje metodu jednotného přihlašování.

### <a name="create-splashtop-test-user"></a>Vytvořit testovacího uživatele Splashtop

1. Po aktivaci metody SSO zkontrolujte nově vytvořenou metodu jednotného přihlašování a povolte ji v části **jednotné přihlašování** .

    ![Snímek obrazovky se zobrazí na stránce jednotného přihlašování, kde můžete novou metodu povolit.](media/splashtop-tutorial/enable.png)

1. Pozvěte testovacího uživatele například `B.Simon@contoso.com` k vašemu týmu Splashtop s nově vytvořenou metodou jednotného přihlašování.

    ![Snímek obrazovky se zobrazí stránka pozvat uživatele, kde můžete vybrat novou metodu.](media/splashtop-tutorial/invite.png)

1. Existující účet Splashtop můžete také změnit na účet jednotného přihlašování, viz [pokyny](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. A to je vše! Účet jednotného přihlašování můžete použít k přihlášení k webovému portálu Splashtop nebo obchodní aplikaci Splashtop.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Splashtop, měli byste se automaticky přihlásit k Splashtop, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Splashtop s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Splashtop pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)