---
title: 'Kurz: Azure Active Directory integrace s Expensify | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37eb989f49593570aa9fcc2ee6f2e5863b59fbc1
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68637766"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Kurz: Integrace Expensify s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Expensify s Azure Active Directory (Azure AD). Když integrujete Expensify s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Expensify.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Expensify svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Expensify odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Expensify podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-expensify-from-the-gallery"></a>Přidání Expensify z Galerie

Pokud chcete nakonfigurovat integraci Expensify do služby Azure AD, musíte přidat Expensify z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Expensify** .
1. Na panelu výsledků vyberte **Expensify** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Expensify pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Expensify.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Expensify, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte EXPENSIFY SSO](#configure-expensify-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte Expensify Test User](#create-expensify-test-user)** -to, abyste měli protějšek B. Simon v Expensify, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Expensify** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL:`https://www.expensify.com/authentication/saml/login`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://www.<companyname>.expensify.com`

    > [!NOTE]
    > Hodnota identifikátoru není skutečný. Aktualizuje tuto hodnotu skutečným identifikátorem. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory klienta Expensify](mailto:help@expensify.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Expensify** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-expensify-sso"></a>Konfigurace jednotného přihlašování Expensify

Pokud chcete povolit jednotné přihlašování v Expensify, musíte nejdřív v aplikaci povolit **řízení z domény** . V aplikaci můžete povolit ovládací prvek doména prostřednictvím kroků uvedených [tady](https://help.expensify.com/domain-control). Pro další podporu Pracujte s [týmem podpory klienta Expensify](mailto:help@expensify.com). Jakmile je povoleno řízení domény, postupujte podle těchto kroků:

![Konfigurace jednotného přihlašování](./media/expensify-tutorial/tutorial_expensify_51.png)

1. Přihlaste se k aplikaci Expensify.

2. Na levém panelu klikněte na **Nastavení** a přejděte na **SAML**.

3. Přepněte možnost **přihlášení SAML** jako **povolenou**.

4. Otevřete stažená federační metadata ze služby Azure AD v programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **metadata poskytovatele identity** .

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Expensify.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Expensify**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-expensify-test-user"></a>Vytvořit testovacího uživatele Expensify

V této části vytvoříte uživatele s názvem B. Simon v Expensify. Pokud chcete přidat uživatele na platformě Expensify, pracujte s [týmem podpory klienta Expensify](mailto:help@expensify.com) .

### <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Expensify, měli byste se automaticky přihlásit k Expensify, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)