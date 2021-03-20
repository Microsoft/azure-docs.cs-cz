---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s litmus | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a litmus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: f2168715d59f9698cba58b7e91bbc897a8cd94f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727229"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s litmus

V tomto kurzu se dozvíte, jak integrovat litmus s Azure Active Directory (Azure AD). Když integrujete litmus s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k litmus.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k litmus svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Litmus odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Litmus podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-litmus-from-the-gallery"></a>Přidání litmus z Galerie

Pokud chcete nakonfigurovat integraci litmus do služby Azure AD, musíte přidat litmus z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **litmus** .
1. Na panelu výsledků vyberte **litmus** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-litmus"></a>Konfigurace a testování jednotného přihlašování Azure AD pro litmus

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí litmus pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v litmus.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí litmus postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte LITMUS SSO](#configure-litmus-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte litmus Test User](#create-litmus-test-user)** -to, abyste měli protějšek B. Simon v litmus, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **litmus** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://litmus.com/sessions/new`

1. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (RAW)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

1. V části **Nastavení litmus** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k litmus.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **litmus**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-litmus-sso"></a>Konfigurace jednotného přihlašování litmus

1. Pokud chcete automatizovat konfiguraci v rámci litmus, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit litmus** , které vás přesměruje do aplikace litmus. Odtud zadejte přihlašovací údaje správce, které se přihlásí k litmus. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit litmus ručně, v jiném okně webového prohlížeče se přihlaste k webu litmus společnosti jako správce.

1. V levém navigačním panelu klikněte na **zabezpečení** .

    ![Snímek obrazovky se zobrazí položka zabezpečení vybraná.](./media/litmus-tutorial/security-img.png)

1. V části **Konfigurace ověřování SAML** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v části Konfigurace ověřování SAML, kde můžete zadat hodnoty, které jsou popsány.](./media/litmus-tutorial/configure1.png)

    a. Přepněte na přepínač **Enable SAML** .

    b. Pro poskytovatele vyberte **Obecné** .

    c. Zadejte název **zprostředkovatele identity**. pro ex. `Azure AD`

1. Proveďte tyto kroky:

    ![Snímek obrazovky se zobrazí část, kde můžete zadat hodnoty, které jsou popsány.](./media/litmus-tutorial/configure3.png)

    a. Do textového pole **koncového bodu SAML 2,0 (http)** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Otevřete stažený soubor **certifikátu** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu X. 509** .

    c. Klikněte na **Uložit nastavení SAML**.

### <a name="create-litmus-test-user"></a>Vytvořit testovacího uživatele litmus

1. V jiném okně webového prohlížeče se přihlaste do aplikace litmus jako správce.

1. V levém navigačním panelu klikněte na **účty** .

    ![Snímek obrazovky znázorňující vybranou položku účtů.](./media/litmus-tutorial/accounts-img.png)

1. Klikněte na tlačítko **Přidat novou kartu uživatele** .

    ![Snímek obrazovky se zobrazí vybraná položka Přidat nový uživatel.](./media/litmus-tutorial/add-new-user.png)

1. V části **Přidat uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí oddíl přidat uživatele, kde můžete zadat hodnoty, které jsou popsány.](./media/litmus-tutorial/user-profile.png)

    a. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele, jako je **B. Simon \@ contoso.com**

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako **B**.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    d. Klikněte na **vytvořit uživatele**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k litmus, kde můžete spustit tok přihlášení.

* Přejít na adresu URL pro přihlášení k litmus přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k litmus, pro které jste nastavili jednotné přihlašování.

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici litmus v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k litmus, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování litmus můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).