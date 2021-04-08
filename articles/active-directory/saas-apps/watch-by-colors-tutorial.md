---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) ke sledování pomocí barev | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a sledujte barvy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.openlocfilehash: fae276049ec3ff6c4bf96be38ff0d2a952731a15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92636778"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) ke sledování pomocí barev

V tomto kurzu se dozvíte, jak integrovat sledování barev pomocí Azure Active Directory (Azure AD). Když integrujete kukátko pomocí barev v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke sledování barev.
* Umožněte uživatelům, aby se automaticky přihlásili, aby mohli sledovat barvy s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Sledujte předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Sledování pomocí barev podporuje **SP a IDP** iniciované jednotné přihlašování.

## <a name="adding-watch-by-colors-from-the-gallery"></a>Přidávání kukátka podle barev z Galerie

Pokud chcete nakonfigurovat integraci sledování podle barev do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS Přidat kukátko od galerie barev.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **sledování podle barev** .
1. Vyberte možnost **sledovat podle barev** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Konfigurace a testování jednotného přihlašování Azure AD pro sledování pomocí barev

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím barev pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v kukátku pomocí barev.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí sledování pomocí barev, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte si sledování pomocí barev SSO](#configure-watch-by-colors-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření kukátka pomocí barev – test uživatele](#create-watch-by-colors-test-user)** – Pokud chcete mít protějšek B. Simon v Sledujte barvy, které jsou propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **sledování podle barev** aplikace, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základním oddílu konfigurace SAML** je aplikace předem nakonfigurovaná v režimu iniciované **IDP** a nezbytné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.colorscorporation.com/login`

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke sledování barevně.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **sledovat podle barev**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-watch-by-colors-sso"></a>Konfigurace sledování pomocí jednotného přihlašování barev

1. Chcete-li automatizovat konfiguraci v rámci sledování pomocí barev, je nutné nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na tlačítko **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Nastavení sledování podle barev** , které vás přesměruje na aplikaci Watch podle barev. Odtud zadejte přihlašovací údaje správce, které se budou přihlašovat ke sledování barev. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit sledování pomocí barev ručně, otevřete nové okno webového prohlížeče a přihlaste se ke svému kukátku pomocí barev webu společnosti jako správce a proveďte následující kroky:

4. V pravém horním rohu stránky klikněte na **profil**  >  **Nastavení účtu**  >  **SSO (jednotné přihlašování)**.

    ![Snímek obrazovky se zobrazí stránka nastavení účtu, kde jsou zakázané S S.](./media/watch-by-colors-tutorial/config01.png)

5. Na stránce **jednotné přihlašování (SSO)** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na kartě nastavení SAML, kde můžete povolit SAML.](./media/watch-by-colors-tutorial/config02.png)

    a. Přepněte **možnost povolit protokol SAML** na **zapnuto**.

    b. Do textového pole **Adresa URL** vložte **adresu URL federačních metadat**, kterou jste zkopírovali z Azure Portal.

    c. Klikněte na **importovat** a na stránce se automaticky vyplní následující pole.

    d. Klikněte na **Uložit**.

### <a name="create-watch-by-colors-test-user"></a>Vytvořit kukátko pomocí barev test uživatele

Pokud chcete uživatelům Azure AD povolit, aby se k nim přihlásili, aby se mohli sledovat pomocí barev, musí se jim zřídit ve sledování barev. V Sledujte barvy je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se a sledujte barvy jako správce zabezpečení.

1. V pravém horním rohu stránky klikněte na **profily**  >  **Uživatelé**  >  **Přidat uživatele**.

    ![Snímek obrazovky se zobrazí stránka uživatelé.](./media/watch-by-colors-tutorial/config03.png)

1. Na stránce s **podrobnostmi o uživateli** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí podrobnosti o uživateli, kde můžete zadat hodnoty, které jsou popsány.](./media/watch-by-colors-tutorial/config04.png)

    a. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako **B**.

    b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. Do textového pole **e-mailu** zadejte e-maily uživatele jako `B.Simon@contoso.com` .

    d. Do textového pole **heslo** zadejte heslo.

    e. V rámci vaší organizace vyberte **oprávnění účtu** .

    f. Klikněte na **Uložit**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici sledovat podle barev na přístupovém panelu, měli byste se ke sledování automaticky přihlásit pomocí barev, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si sledování barev pomocí Azure AD](https://aad.portal.azure.com/)