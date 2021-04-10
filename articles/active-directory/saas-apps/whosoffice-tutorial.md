---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s WhosOffice | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a WhosOffice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 622aa6ea143eb9c279cc7451ccc2c91a7b9e602f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734430"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosoffice"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s WhosOffice

V tomto kurzu se dozvíte, jak integrovat WhosOffice s Azure Active Directory (Azure AD). Když integrujete WhosOffice s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k WhosOffice.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k WhosOffice svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* WhosOffice odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* WhosOffice podporuje jednotné přihlašování (SSO) **a IDP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-whosoffice-from-the-gallery"></a>Přidání WhosOffice z Galerie

Pokud chcete nakonfigurovat integraci WhosOffice do služby Azure AD, musíte přidat WhosOffice z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **WhosOffice** .
1. Na panelu výsledků vyberte **WhosOffice** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-whosoffice"></a>Konfigurace a testování jednotného přihlašování Azure AD pro WhosOffice

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí WhosOffice pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v WhosOffice.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí WhosOffice postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte WHOSOFFICE SSO](#configure-whosoffice-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte WhosOffice Test User](#create-whosoffice-test-user)** -to, abyste měli protějšek B. Simon v WhosOffice, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **WhosOffice** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/tužka pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.my.whosoffice.com/int/azure/consume.aspx`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.my.whosoffice.com/int/azure`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a přihlašovací adresy URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta WhosOffice](mailto:support@whosoffice.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení WhosOffice** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k WhosOffice.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **WhosOffice**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-whosoffice-sso"></a>Konfigurace jednotného přihlašování WhosOffice

1. Pokud chcete automatizovat konfiguraci v rámci WhosOffice, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit WhosOffice** , které vás přesměruje do aplikace WhosOffice. Odtud zadejte přihlašovací údaje správce, které se přihlásí k WhosOffice. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit WhosOffice ručně, v jiném okně webového prohlížeče se přihlaste k webu WhosOffice společnosti jako správce.

1. Klikněte na **Nastavení** a vyberte **Společnost**.

    ![Snímek obrazovky se zobrazí společnost vybraná z nastavení.](./media/whosoffice-tutorial/configuration1.png)

1. Klikněte na **aplikace/integrace**.

    ![Snímek obrazovky zobrazuje aplikace nebo integrace vybrané z nastavení společnosti.](./media/whosoffice-tutorial/configuration2.png)

1. V rozevíracím seznamu Zprostředkovatel vyberte **Microsoft Azure** a klikněte na **aktivovat poskytovatele přihlášení**.

    ![Snímek obrazovky ukazuje aktivovat poskytovatele přihlášení, který je vybraný pro Microsoft Azure.](./media/whosoffice-tutorial/configuration3.png)

1. Kliknutím na možnost **nahrát** nahrajte stažený soubor federačních metadat z Azure Portal.
    
    ![Snímek obrazovky ukazuje možnost nahrání pro soubor s metadaty.](./media/whosoffice-tutorial/configuration4.png)

### <a name="create-whosoffice-test-user"></a>Vytvořit testovacího uživatele WhosOffice

1. V jiném okně webového prohlížeče se přihlaste k webu WhosOffice jako správce.

1. Klikněte na **Nastavení** a vyberte **Uživatelé**.

    ![Snímek obrazovky ukazuje uživatele vybrané z nastavení.](./media/whosoffice-tutorial/user1.png)

1. Vyberte **vytvořit nového uživatele**.

    ![Snímek obrazovky zobrazuje vybraný vytvořit nového uživatele.](./media/whosoffice-tutorial/user2.png)

1. Zadejte potřebné informace o uživateli podle požadavků vaší organizace.

    ![Snímek obrazovky se zobrazí dialogové okno Nový uživatel, kde můžete zadat uživatelská data.](./media/whosoffice-tutorial/user3.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k WhosOffice, kde můžete spustit tok přihlášení.

* Přejít na adresu URL pro přihlášení k WhosOffice přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k WhosOffice, pro které jste nastavili jednotné přihlašování.

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici WhosOffice v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k WhosOffice, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování WhosOffice můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).