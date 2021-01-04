---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s AskYourTeam | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 426b4de61f2cf654ce24b311767a0aabbba6c6d3
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724829"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s AskYourTeam

V tomto kurzu se dozvíte, jak integrovat AskYourTeam s Azure Active Directory (Azure AD). Když integrujete AskYourTeam s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k AskYourTeam.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k AskYourTeam svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* AskYourTeam odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* AskYourTeam podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-askyourteam-from-the-gallery"></a>Přidání AskYourTeam z Galerie

Pokud chcete nakonfigurovat integraci AskYourTeam do služby Azure AD, musíte přidat AskYourTeam z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **AskYourTeam** .
1. Na panelu výsledků vyberte **AskYourTeam** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>Konfigurace a testování jednotného přihlašování Azure AD pro AskYourTeam

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí AskYourTeam pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v AskYourTeam.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí AskYourTeam postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte ASKYOURTEAM SSO](#configure-askyourteam-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte AskYourTeam Test User](#create-askyourteam-test-user)** -to, abyste měli protějšek B. Simon v AskYourTeam, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **AskYourTeam** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a Sign-On hodnoty URL, které jsou vysvětleny dále v tomto kurzu.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení AskYourTeam** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k AskYourTeam.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **AskYourTeam**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-askyourteam-sso"></a>Konfigurace jednotného přihlašování AskYourTeam

1. Pokud chcete automatizovat konfiguraci v rámci AskYourTeam, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit AskYourTeam** , které vás přesměruje do aplikace AskYourTeam. Odtud zadejte přihlašovací údaje správce, které se přihlásí k AskYourTeam. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit AskYourTeam ručně, v jiném okně webového prohlížeče se přihlaste k webu AskYourTeam společnosti jako správce.

1. Klikněte na **Moje organizace**.

    ![Snímek obrazovky zobrazuje odkaz Moje organizace.](./media/askyourteam-tutorial/user1.png)

1. Klikněte na **integrace**.

    ![Snímek obrazovky ukazuje odkaz integrace.](./media/askyourteam-tutorial/configure1.png)

1. Klikněte na **Upravit nastavení**.

    ![Snímek obrazovky s tlačítkem upravit nastavení zobrazí jednu Sign-Onovou zprávu.](./media/askyourteam-tutorial/configure2.png)

1. Na stránce **Upravit integraci jednoho Sign-On** proveďte následující kroky: 

    ![Snímek obrazovky znázorňující integraci s jednou Sign-On, kde můžete zadat hodnoty pro tento krok.](./media/askyourteam-tutorial/configure3.png)

    a. Do textového pole **Adresa URL služby Single Sign-On Service v SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **ID entity SAML** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL pro** odhlášení vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **Base64 podpisového certifikátu SAML** .

    > [!NOTE]
    > Případně můžete také odeslat soubor **XML s federačními metadaty** kliknutím na možnost **zvolit soubor** .

    e. Kopírovat **adresu URL odpovědi (adresa URL služby kontrolního výrazu)** , vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    f. Kopírovat hodnotu **adresy URL pro přihlášení** vložte tuto hodnotu do textového pole **přihlašovací adresa URL** v základní části **Konfigurace SAML** v Azure Portal.

    například Klikněte na **Uložit**.

### <a name="create-askyourteam-test-user"></a>Vytvořit testovacího uživatele AskYourTeam

1. V jiném okně webového prohlížeče se přihlaste k webu AskYourTeam jako správce.

1. Klikněte na **Moje organizace**.

    ![Snímek obrazovky se zobrazí odkaz Moje organizace, kde zahájíte tuto úlohu.](./media/askyourteam-tutorial/user1.png)

1. Klikněte na **Uživatelé** a vyberte **Nový uživatel**.

    ![Snímek obrazovky s novým uživatelem zobrazuje odkaz uživatelé.](./media/askyourteam-tutorial/user2.png)

1. V části **Nový uživatel** proveďte následující kroky:

    ![Snímek obrazovky ukazuje novou část uživatele, do které zadáte informace o uživateli.](./media/askyourteam-tutorial/user3.png)

    1. Do textového pole **jméno** a příjmení zadejte jméno uživatele.

    1. Do textového pole **příjmení** zadejte příjmení uživatele.

    1. Do textového pole **e-mail** zadejte EmailAddress uživatele, jako je B.Simon@contoso.com .

    1. Vyberte **roli** pro uživatele podle požadavků vaší organizace.

    1. Klikněte na **Uložit**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k AskYourTeam, kde můžete spustit tok přihlášení.

* Přejít na adresu URL pro přihlášení k AskYourTeam přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k AskYourTeam, pro které jste nastavili jednotné přihlašování.

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici AskYourTeam v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k AskYourTeam, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování AskYourTeam můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
