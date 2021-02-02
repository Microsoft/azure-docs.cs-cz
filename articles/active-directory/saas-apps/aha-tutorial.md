---
title: 'Kurz: Integrace Azure Active Directory s Aha! | Dokumentace Microsoftu'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Aha!.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a8a33290c7fa904fc16b728a6e2411bd5b42683f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259649"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Kurz: integrace programu Aha! s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat program Aha! s Azure Active Directory (Azure AD). Při integraci Aha! pomocí Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k Aha!
* Umožněte uživatelům, aby se automaticky přihlásili do Aha! se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Aha! odběr s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Aha! podporuje jednotné přihlašování iniciované v **SP**
* Aha! podporuje zřizování uživatelů **jenom v čase**

## <a name="add-aha-from-the-gallery"></a>Přidejte Aha. z Galerie

Ke konfiguraci integrace programu Aha! do Azure AD je nutné přidat Aha! z Galerie do vašeho seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte **Aha!** do vyhledávacího pole.
1. Vyberte možnost **Aha!** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-aha"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Aha!

Konfigurace a testování jednotného přihlašování Azure AD pomocí programu Aha! pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Aha!.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí programu Aha!, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurujte Aha. SSO](#configure-aha-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace
    1. **[Vytvořte se systémem Aha! Test User](#create-aha-test-user)** – abyste měli protějšek B. Simon v programu Aha! který je propojený s reprezentací uživatele Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal v **Aha!** Stránka integrace aplikace, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.aha.io/session/new`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.aha.io`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Kontaktujte [Aha! Tým podpory klientů](https://www.aha.io/company/contact) , který získá tyto hodnoty. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V **Nastavení Aha!** na základě vašeho požadavku zkopírujte příslušné adresy URL (y).

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Aha!.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Aha!**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-aha-sso"></a>Nakonfigurujte Aha. Jednotné přihlašování

1. Pokud chcete automatizovat konfiguraci v rámci programu Aha!, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Nastavení Aha.** vás přesměruje na Aha! použití. Odtud zadejte přihlašovací údaje správce pro přihlášení do režimu Aha!. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-8.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit program Aha! ručně otevřete nové okno webového prohlížeče a přihlaste se do své složky Aha! firemní web jako správce a proveďte následující kroky:

4. V nabídce v horní části klikněte na **Nastavení**.

    ![Nastavení](./media/aha-tutorial/setting.png "Nastavení")

5. Klikněte na **účet**.

    ![Profil](./media/aha-tutorial/account.png "Profil")

6. Klikněte na **zabezpečení a jednotné přihlašování**.

    ![Snímek obrazovky, který zvýrazní možnost nabídky zabezpečení a jednotného přihlašování](./media/aha-tutorial/security.png "Zabezpečení a jednotné přihlašování")

7. V části **jednotné přihlašování** jako **zprostředkovatel identity** vyberte **SAML 2.0**.

    ![Zabezpečení a jednotné přihlašování](./media/aha-tutorial/saml.png "Zabezpečení a jednotné přihlašování")

8. Na stránce konfigurace **jednotného přihlašování** proveďte následující kroky:

    ![Jednotné přihlašování](./media/aha-tutorial/sso.png "Jednotné přihlašování")

    a. Do textového pole **název** zadejte název konfigurace.

    b. Pro možnost **konfigurovat pomocí** vyberte **soubor metadat**.

    c. Pokud chcete nahrát stažený soubor metadat, klikněte na **Procházet**.

    d. Klikněte na **Aktualizovat**.

### <a name="create-aha-test-user"></a>Vytvořte Aha! testovací uživatel

V této části se v Aha! vytvoří uživatel s názvem B. Simon. Aha! podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v Aha! ještě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Aha! Přihlašovací adresa URL, kde můžete spustit tok přihlášení. 

* Přejít na Aha! Přihlašovací adresa URL přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na ikonu Aha! dlaždice se v mých aplikacích přesměruje na Aha! Přihlašovací adresa URL: Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete Aha! můžete vyhovět řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
