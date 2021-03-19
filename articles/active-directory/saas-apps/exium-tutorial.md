---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Exium | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Exium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 01b3bbc0a4f0c7d53ed0a9ba2449cf5985b2ab86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610205"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Exium

V tomto kurzu se dozvíte, jak integrovat Exium s Azure Active Directory (Azure AD). Když integrujete Exium s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Exium.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Exium svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Exium odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Exium podporuje jednotné přihlašování iniciované v **SP** .

## <a name="adding-exium-from-the-gallery"></a>Přidání Exium z Galerie

Pokud chcete nakonfigurovat integraci Exium do služby Azure AD, musíte přidat Exium z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Exium** .
1. Na panelu výsledků vyberte **Exium** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Exium

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Exium pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Exium.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Exium postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte EXIUM SSO](#configure-exium-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Exium Test User](#create-exium-test-user)** -to, abyste měli protějšek B. Simon v Exium, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Exium** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://subapi.exium.net/saml/<WORKSPACE_ID>/acs`

    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://service.exium.net/sign-in`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Exium](mailto:support@exium.net) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Exium.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Exium**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-exium-sso"></a>Konfigurace jednotného přihlašování Exium

1. Přihlaste se k webu Exium společnosti jako správce.

1. V **konzole pro správu** vyberte panel **Profil společnosti** .

    ![snímek obrazovky konzoly pro správu](./media/exium-tutorial/company-profile.png)

1. V **profilu** klikněte na **nastavení jednotného přihlašování** a **upravte** ho.

1. V části **nastavení jednotného přihlašování** proveďte níže uvedené kroky.

    ![snímek obrazovky s nastavením jednotného přihlašování](./media/exium-tutorial/update.png)

    a. Z rozevíracího seznamu vyberte **Typ jednotného přihlašování** jako **AzureAD** .

    b. Do pole **Adresa URL METADAT IDP SAML 2,0** vložte hodnotu **adresy URL federačních metadat aplikace** .

    c. Zkopírujte hodnotu **adresy URL jednotného přihlašování SAML 2,0** , vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    d. Zkopírujte hodnotu **ID entity saml 2,0 SP** , vložte tuto hodnotu do textového pole **identifikátor** v základní části **Konfigurace SAML** v Azure Portal.  

    e. Klikněte na **aktualizovat**.

### <a name="create-exium-test-user"></a>Vytvořit testovacího uživatele Exium

1. Přihlaste se k webu Exium společnosti jako správce.

1. Přejděte na **Správa uživatelů – > uživatelé** a klikněte na **Přidat uživatele**.

    ![snímek obrazovky pro vytvoření testovacího uživatele](./media/exium-tutorial/add-user.png)

1. Na následující stránce zadejte požadovaná pole a klikněte na **Uložit**.

    ![snímek obrazovky pro vytváření polí testovacího uživatele s tlačítkem Uložit](./media/exium-tutorial/add-user-2.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Exium, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Exium přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Exium v části Moje aplikace, přesměruje se na přihlašovací adresu Exium. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Exium můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


