---
title: 'Kurz: Azure Active Directory integrace se softwarem Ungerboeck | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Ungerboeck softwarem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jeedes
ms.openlocfilehash: ddc83da2836d414ee3fadd23288475a6829018aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521777"
---
# <a name="tutorial-integrate-ungerboeck-software-with-azure-active-directory"></a>Kurz: Integrace softwaru Ungerboeck s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat software Ungerboeck s Azure Active Directory (Azure AD). Když integrujete software Ungerboeck s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Ungerboeck softwaru.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Ungerboeck softwaru pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné povoleného jednotného přihlašování Ungerboeck softwaru (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Ungerboeck software podporuje jednotné přihlašování iniciované v **SP** .

## <a name="adding-ungerboeck-software-from-the-gallery"></a>Přidání softwaru Ungerboeck z Galerie

Pokud chcete nakonfigurovat integraci Ungerboeck softwaru do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Ungerboeck software z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Ungerboeck software** .
1. Vyberte **Ungerboeck software** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s Ungerboeck softwarem pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Ungerboeck softwaru.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Ungerboeck softwaru, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[NAKONFIGURUJTE jednotné přihlašování Ungerboeck softwaru](#configure-ungerboeck-software-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
4. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
5. **[Vytvořte uživatele Ungerboeck software test](#create-ungerboeck-software-test-user)** , aby měl protějšek B. Simon v Ungerboeck softwaru, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **softwarových aplikací Ungerboeck** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.ungerboeck.com/prod`

    1. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:
    
    *  **V produkčním prostředí**:

       - `https://<SUBDOMAIN>.ungerboeck.com/prod`
       - `https://<SUBDOMAIN>.ungerboeck.net/prod`
       - `https://<SUBDOMAIN>.ungerboeck.io/prod`

   * **Pro testovací prostředí**:

     - `https://<SUBDOMAIN>.ungerboeck.com/test`
     - `https://<SUBDOMAIN>.ungerboeck.net/test`
     - `https://<SUBDOMAIN>.ungerboeck.io/test`

   > [!NOTE]
   > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovacího URL a identifikátoru, který je vysvětlen dále v části **Konfigurace jednotného přihlašování Ungerboeck softwaru** v kurzu.

1. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

1. V části **podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte ho do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

1. V části **nastavit software Ungerboeck** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-ungerboeck-software-sso"></a>Konfigurace jednotného přihlašování Ungerboeck softwaru

Chcete-li nakonfigurovat jednotné přihlašování na straně **softwaru Ungerboeck** , je třeba odeslat **hodnotu kryptografického otisku** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory softwaru Ungerboeck](mailto:Rhonda.Jannings@ungerboeck.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k softwaru Ungerboeck.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Ungerboeck software**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-ungerboeck-software-test-user"></a>Vytvořit uživatele testu softwaru Ungerboeck

V této části vytvoříte uživatele s názvem B. Simon v softwaru Ungerboeck. Pokud chcete přidat uživatele na Ungerboeck software Platform, pracujte s [týmem podpory Ungerboeck softwaru](mailto:Rhonda.Jannings@ungerboeck.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici software Ungerboeck, měli byste být automaticky přihlášeni k softwaru Ungerboeck, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)