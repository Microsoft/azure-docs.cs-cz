---
title: 'Kurz: Azure Active Directory integrace s TigerConnect Secure Messenger | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TigerConnect Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: jeedes
ms.openlocfilehash: 6831e96ebd8fd2db51d640ff3d93955b1a9f1477
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182151"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Kurz: Azure Active Directory integrace s TigerConnect Secure Messenger

V tomto kurzu se dozvíte, jak integrovat TigerConnect Secure Messenger s Azure Active Directory (Azure AD).

Integrace TigerConnect Secure Messenger s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k TigerConnect zabezpečenému Messenger.
* Můžete uživatelům povolit, aby se automaticky přihlásili k TigerConnect zabezpečeným kurýrním programům (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Podrobnosti o integraci aplikací SaaS (software jako služba) se službou Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s TigerConnect Secure Messenger potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné zabezpečeného kurýrní služby TigerConnect s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete TigerConnect Secure Messenger s Azure AD.

* TigerConnect Secure Messenger podporuje jednotné přihlašování se spuštěnou službou **SP**
* Po nakonfigurování služby TigerConnect Secure Messenger můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-tigerconnect-secure-messenger-from-the-gallery"></a>Přidání programu TigerConnect Secure Messenger z Galerie

Pokud chcete nakonfigurovat integraci TigerConnect Secure Messenger do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat TigerConnect zabezpečený Messenger z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **TigerConnect Secure Messenger** .
1. Z panelu výsledků vyberte **TigerConnect Secure Messenger** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TigerConnect Secure Messenger na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TigerConnect Secure Messenger.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby TigerConnect Secure Messenger, je potřeba, abyste dokončili tyto stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
    * **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování **[TigerConnect Secure Messenger](#configure-tigerconnect-secure-messenger-sso)** ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte testovacího uživatele TigerConnect Secure Messenger](#create-a-tigerconnect-secure-messenger-test-user)** , aby byl uživatel s názvem Britta Simon v TigerConnect zabezpečený kurýrní program, který je propojený s uživatelem Azure AD s názvem Britta Simon.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí služby TigerConnect Secure Messenger, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **zabezpečeného kurýrní služby TigerConnect** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    1. Do pole **přihlašovací adresa URL** zadejte adresu URL:

       `https://home.tigertext.com`

    1. Do pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Hodnota **identifikátoru (ID entity)** není skutečná. Aktualizuje tuto hodnotu skutečným identifikátorem. Pokud chcete získat hodnotu, obraťte se na [tým podpory TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). Můžete také odkazovat na vzory zobrazené v podokně **základní konfigurace SAML** v Azure Portal.

1. V podokně **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** a ze daných možností stáhněte **XML federačních metadat** a uložte ho do svého počítače.

    ![Možnost stažení XML federačních metadat](common/metadataxml.png)

1. V části **nastavení zabezpečeného kurýrní služby TigerConnect** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TigerConnect zabezpečenému Messenger.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **TigerConnect Secure Messenger**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-tigerconnect-secure-messenger-sso"></a>Konfigurace jednotného přihlašování TigerConnect Secure Messenger

Pokud chcete nakonfigurovat jednotné přihlašování na straně zabezpečeného kurýrní služby TigerConnect, je potřeba odeslat stažený soubor XML federačních metadat a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory TigerConnect zabezpečeného kurýrní](mailto:prosupport@tigertext.com)služby. Tým TigerConnect Secure Messenger zajistí správné nastavení připojení SAML SSO na obou stranách.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>Vytvoření testovacího uživatele TigerConnect Secure Messenger

V této části vytvoříte uživatele s názvem Britta Simon v TigerConnect Secure Messenger. Spolupracujte s [týmem podpory TigerConnect Secure Messenger](mailto:prosupport@tigertext.com) a přidejte Britta Simon jako uživatele v TigerConnect Secure Messenger. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Když vyberete **TigerConnect zabezpečený Messenger** na portálu moje aplikace, měli byste být automaticky přihlášeni k předplatnému služby TigerConnect Secure Messenger, pro které jste nastavili jednotné přihlašování. Další informace o portálu moje aplikace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si TigerConnect zabezpečený Messenger pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)