---
title: 'Kurz: Azure Active Directory integrace s Brightspace podle Desire2Learn | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Brightspace pomocí Desire2Learn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: 61c4125038ea0c64ff8489a25f9c63c5cbb82a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583815"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Kurz: Azure Active Directory integrace s Brightspace pomocí Desire2Learn

V tomto kurzu se dozvíte, jak integrovat Brightspace pomocí Desire2Learn s Azure Active Directory (Azure AD). Při integraci Brightspace pomocí Desire2Learn se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k Brightspace prostřednictvím Desire2Learn.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Brightspace prostřednictvím Desire2Learn s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Brightspace podle povoleného předplatného Desire2Learn jednotného přihlašování.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Brightspace by Desire2Learn podporovala **IDP** iniciované jednotné přihlašování.

## <a name="add-brightspace-by-desire2learn-from-the-gallery"></a>Přidání Brightspace podle Desire2Learn z Galerie

Pokud chcete nakonfigurovat integraci Brightspace by Desire2Learn do služby Azure AD, musíte přidat Brightspace od galerie Desire2Learn z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Brightspace by Desire2Learn** .
1. Z panelu výsledků vyberte **Brightspace by Desire2Learn** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-brightspace-by-desire2learn"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Brightspace pomocí Desire2Learn

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Brightspace pomocí Desire2Learn pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Brightspace pomocí Desire2Learn.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Brightspace pomocí Desire2Learn, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Brightspace pomocí jednotného přihlašování Desire2Learn](#configure-brightspace-by-desire2learn-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Brightspace pomocí Desire2Learn testovacího uživatele](#create-brightspace-by-desire2learn-test-user)** – můžete mít protějšek B. Simon v Brightspace by Desire2Learn, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **Brightspace podle Desire2Learn** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte jednu z adres URL pomocí následujících vzorů:

    ```http
    https://<companyname>.tenants.brightspace.com/samlLogin
    https://<companyname>.desire2learn.com/shibboleth-sp
    ```

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [Brightspace podle týmu podpory klientů Desire2Learn](https://www.d2l.com/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Brightspace podle Desire2Learn** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Brightspace pomocí Desire2Learn.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Brightspace podle Desire2Learn**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-brightspace-by-desire2learn-sso"></a>Konfigurace Brightspace pomocí jednotného přihlašování Desire2Learn

Chcete-li nakonfigurovat jednotné přihlašování na **Brightspace po straně Desire2Learn** , je třeba odeslat stažený **kód XML federačních metadat** a příslušné zkopírované adresy URL od Azure Portal do [Brightspace podle týmu podpory Desire2Learn](https://www.d2l.com/contact/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-brightspace-by-desire2learn-test-user"></a>Vytvořit Brightspace podle Desire2Learn testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon v Brightspace pomocí Desire2Learn. Spolupracujte s [Brightspace podle Desire2Learn týmu podpory](https://www.d2l.com/contact/) a přidejte uživatele v Brightspace podle platformy Desire2Learn. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

> [!NOTE]
> K zřizování Azure Active Directory uživatelských účtů můžete použít jiné Brightspace pomocí nástrojů pro vytváření uživatelských účtů Desire2Learn a rozhraní API, které poskytuje Brightspace od společnosti Desire2Learn.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlásit k Brightspace pomocí Desire2Learn, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Brightspace by Desire2Learn v okně moje aplikace, měli byste se k Brightspace automaticky přihlásit Desire2Learn, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Brightspace nástrojem Desire2Learn můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
