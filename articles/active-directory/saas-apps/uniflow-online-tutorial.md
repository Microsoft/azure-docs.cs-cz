---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s uniFLOW online | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a uniFLOW online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eb369047574ef76dd31996fd16399380ea027c8
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823302"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s uniFLOW online

V tomto kurzu se dozvíte, jak integrovat uniFLOW online s Azure Active Directory (Azure AD). Když integrujete uniFLOW online s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k uniFLOW online
* Umožněte uživatelům, aby se automaticky přihlásili k uniFLOW online pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* předplatné uniFLOW online jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* uniFLOW online podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-uniflow-online-from-the-gallery"></a>Přidání uniFLOW do online režimu z Galerie

Pokud chcete nakonfigurovat integraci uniFLOW online do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat uniFLOW online z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **uniFLOW online** .
1. Na panelu výsledků vyberte **UniFLOW online** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Konfigurace a testování jednotného přihlašování Azure AD pro uniFLOW online

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí uniFLOW online s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v uniFLOW online.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí uniFLOW online, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte UNIFLOW jednotné](#configure-uniflow-online-sso)** přihlašování online – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte online testovacího uživatele uniFLOW](#create-uniflow-online-test-user)** , abyste měli protějšek B. Simon v uniFLOW online, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **online** integrace aplikací uniFLOW najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory online klientů uniFLOW](mailto:support@nt-ware.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. aplikace uniFLOW online očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho uniFLOW aplikace online očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name (Název) |  Zdrojový atribut|
    | -----------| --------------- |
    | displayName | user.displayname |
    | zdívek | User. onpremisessamaccountname |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k uniFLOW online.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **UniFLOW online**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-uniflow-online-sso"></a>Konfigurace online jednotného přihlašování uniFLOW

1. V jiném okně webového prohlížeče se přihlaste k online webu uniFLOW jako správce.

1. V levém navigačním panelu vyberte kartu **uživatel** .

    ![Online konfigurace uniFLOW](./media/uniflow-online-tutorial/configure1.png)

1. Klikněte na **Zprostředkovatelé identity**.

    ![Online konfigurace uniFLOW](./media/uniflow-online-tutorial/configure2.png)

1. Klikněte na **Přidat zprostředkovatele identity**.

    ![Online konfigurace uniFLOW](./media/uniflow-online-tutorial/configure3.png)

1. V části **Přidat zprostředkovatele identity** proveďte následující kroky:


    ![Online konfigurace uniFLOW](./media/uniflow-online-tutorial/configure4.png)

    a. Zadejte zobrazovaný název ex: *AZUREAD SSO*.

    b. Jako **Typ poskytovatele**vyberte možnost **WS-** dobalení z rozevíracího seznamu.

    c. Pro **typ WS-** dobalení vyberte možnost **Azure Active Directory** z rozevíracího seznamu.

    d. Klikněte na **Uložit**.

1. Na kartě **Obecné** proveďte následující kroky:

    ![Online konfigurace uniFLOW](./media/uniflow-online-tutorial/configure5.png)

    a. Zadejte zobrazovaný název ex: *AZUREAD SSO*.

    b. Možnost vybrat **z adresy URL** pro **federační metadata ADGS**

    c. Do textového pole **Adresa URL federačních metadat** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    d. Vyberte **zprostředkovatele identity** jako **povolený**.

    e. Vyberte možnost **Automatická registrace uživatele** jako **aktivovanou**.

    f. Klikněte na **Uložit**.

### <a name="create-uniflow-online-test-user"></a>Vytvořit online testovacího uživatele uniFLOW

1. V jiném okně webového prohlížeče se přihlaste k online webu uniFLOW jako správce.

1. V levém navigačním panelu vyberte kartu **uživatel** .

    ![Online konfigurace uniFLOW](./media/uniflow-online-tutorial/configure1.png)

1. Klikněte na **Přidat uživatele**.

    ![Online konfigurace uniFLOW](./media/uniflow-online-tutorial/user1.png)

1. Klikněte na **vytvořit uživatele ručně**.

    ![Online konfigurace uniFLOW](./media/uniflow-online-tutorial/user2.png)

1. Na kartě **vytvořit uživatele ručně** zadejte požadované hodnoty podle požadavků vaší organizace.

    ![Online konfigurace uniFLOW](./media/uniflow-online-tutorial/user3.png)

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici uniFLOW online, měli byste se automaticky přihlásit k uniFLOW online, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si uniFLOW online s Azure AD](https://aad.portal.azure.com/)