---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s NEOem desknets | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a desknets NEO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: ef3e024e1b704cc9f8711d9ca78ee916acf72680
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s NEOem v desknet

V tomto kurzu se dozvíte, jak integrovat NEO desknet s Azure Active Directory (Azure AD). Když integrujete NEO desknete v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k NEOu desknet.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k desknet NEO s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* desknet odběr NEO jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* NEO desknet podporuje jednotné přihlašování (SSO) iniciované v **SP** .

## <a name="adding-desknets-neo-from-the-gallery"></a>Přidání NEOu desknet z Galerie

Pokud chcete nakonfigurovat integraci NEOu desknet do služby Azure AD, musíte přidat NEO desknet z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Neo desknet** .
1. Z panelu výsledků vyberte **Neo desknet** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro NEO desknet

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s NEO desknet pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v NEO desknet.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí NEOu desknet, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte desknet SSO Neo SSO](#configure-desknets-neo-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Neo testovacího uživatele desknet](#create-desknets-neo-test-user)** , abyste měli protějšek B. Simon v neo desknet, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce Application Integration **Neo pro desknet** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<CUSTOMER_NAME>.dn-cloud.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL pro přihlášení. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory Neo klienta desknet](mailto:cloudsupport@desknets.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Neo pro desknet** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k NEO desknet.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **desknet 's Neo**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-desknets-neo-sso"></a>Konfigurace desknet jednotného přihlašování NEO

1. Přihlaste se k webu NEO své společnosti desknet jako správce.

1. V nabídce klikněte na ikonu **Nastavení odkazu ověřování SAML** .

    ![Snímek obrazovky s nastavením odkazu pro ověřování SAML](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. V části **společné nastavení** klikněte na **použít** při spolupráci s ověřováním SAML.

    ![Snímek obrazovky s použitím ověřování SAML](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. V části **Nastavení odkazu pro ověřování SAML** proveďte níže uvedené kroky.

    ![Snímek obrazovky s oddílem nastavení odkazu ověřování SAML.](./media/desknets-neo-tutorial/saml-authentication.png)

    a. Do textového pole **Adresa URL přístupu** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **ID entity SP** vložte hodnotu **identifikátoru** , kterou jste zkopírovali z Azure Portal.

    c. Klikněte na **zvolit soubor** a nahrajte stažený soubor **certifikátu (Base64)** z Azure Portal do textového pole **certifikátu x. 509** .

    d. Klikněte na **změnit**.

### <a name="create-desknets-neo-test-user"></a>Vytvořit testovacího uživatele NEO v desknet

1. Přihlaste se k webu NEO své společnosti desknet jako správce.

1. V **nabídce** klikněte na ikonu **Nastavení správce** .

    ![Snímek obrazovky s nastavením správce](./media/desknets-neo-tutorial/administrator-settings.png)

1. Klikněte na ikonu **Nastavení** a v části **vlastní nastavení** vyberte **Správa uživatelů** .

    ![Snímek obrazovky s nastavením správy uživatelů](./media/desknets-neo-tutorial/user-management.png)

1. Klikněte na **vytvořit informace o uživateli**.

    ![Snímek obrazovky s tlačítkem informace o uživateli](./media/desknets-neo-tutorial/create-new-user.png)

1. Vyplňte požadovaná pole na následující stránce a klikněte na **vytvořit**.

    ![Snímek obrazovky s oddílem pro vytvoření uživatele](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na desknet adresu URL pro přihlášení NEO, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlašování NEO na desknet přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici NEO (desknet) v okně moje aplikace, přesměruje se na NEO přihlašovací adresu URL desknet. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování NEO pro desknet můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


