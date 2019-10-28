---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Paylocity | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Paylocity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f5e5a71f-ea04-4533-ba58-0ec85fdf0982
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f6684df42971b91975f6a7f5aeded9326cef3f5
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969802"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-paylocity"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Paylocity

V tomto kurzu se dozvíte, jak integrovat Paylocity s Azure Active Directory (Azure AD). Když integrujete Paylocity s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Paylocity.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Paylocity svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Paylocity odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Paylocity podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-paylocity-from-the-gallery"></a>Přidání Paylocity z Galerie

Pokud chcete nakonfigurovat integraci Paylocity do služby Azure AD, musíte přidat Paylocity z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Paylocity** .
1. Na panelu výsledků vyberte **Paylocity** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-paylocity"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Paylocity

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Paylocity pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Paylocity.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Paylocity, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte PAYLOCITY SSO](#configure-paylocity-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Paylocity Test User](#create-paylocity-test-user)** -to, abyste měli protějšek B. Simon v Paylocity, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Paylocity** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

    ![Informace o jednotném přihlašování v doméně Paylocity a adresách URL](common/preintegrated.png)

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu url: `https://access.paylocity.com/`

1. Klikněte na **Uložit**.

1. Paylocity aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace Paylocity několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale je nutné aktualizovat tyto atributy skutečnými hodnotami.

    | Name (Název) |  Zdrojový atribut|
    | ---------------| --------------- |
    | PartnerID | < "PartnerID" > |
    | PaylocityUser | < "PaylocityUser" > |
    | PaylocityEntity | < "PaylocityEntity" > |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Paylocity** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Paylocity.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Paylocity**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-paylocity-sso"></a>Konfigurace jednotného přihlašování Paylocity

Ke konfiguraci jednotného přihlašování na straně **Paylocity** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Paylocity](mailto:service@paylocity.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-paylocity-test-user"></a>Vytvořit testovacího uživatele Paylocity

V této části vytvoříte uživatele s názvem B. Simon v Paylocity. Pokud chcete přidat uživatele na platformě Paylocity, pracujte s [týmem podpory Paylocity](mailto:service@paylocity.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Paylocity, měli byste se automaticky přihlásit k Paylocity, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Paylocity s Azure AD](https://aad.portal.azure.com/)