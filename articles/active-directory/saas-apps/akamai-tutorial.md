---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Akamai | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979592"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Akamai

V tomto kurzu se dozvíte, jak integrovat Akamai s Azure Active Directory (Azure AD). Když integrujete Akamai s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Akamai.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Akamai svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Akamai odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

- Časová rezerva podporuje IDPé jednotné přihlašování.

## <a name="adding-akamai-from-the-gallery"></a>Přidání Akamai z Galerie

Pokud chcete nakonfigurovat integraci Akamai do služby Azure AD, musíte přidat Akamai z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Akamai** .
1. Na panelu výsledků vyberte **Akamai** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Akamai

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Akamai pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Akamai.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Akamai, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Akamai SSO](#configure-akamai-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Akamai Test User](#create-akamai-test-user)** -to, abyste měli protějšek B. Simon v Akamai, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Akamai** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Akamai](https://www.akamai.com/us/en/contact-us/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Akamai** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Akamai.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Akamai**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-akamai-sso"></a>Konfigurace jednotného přihlašování Akamai

### <a name="setting-up-idp"></a>Nastavení IDP

1. Přihlaste se ke konzole **Akamai Enterprise Application Access** .
1. V **konzole Akamai EAA**vyberte **identita** **poskytovatel identity** > identity.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure01.png)

1. Klikněte na **Přidat zprostředkovatele identity**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure02.png)

    a. Zadejte **jedinečný název**.
    
    b. Vyberte protokol **SAML třetí strany** a klikněte na **vytvořit zprostředkovatele identity a nakonfigurovat**.

### <a name="general-settings"></a>Obecná nastavení

1. **Zachycení identity** – zadejte název (základní adresa URL služby SP – bude použit pro konfiguraci Azure AD)

    > [!NOTE]
    > Můžete si vybrat, že budete mít vlastní doménu (budete potřebovat položku DNS a certifikát). V tomto příkladu budeme používat doménu Akamai.

1. **Akamai Cloud Zone** – vyberte příslušnou zónu cloudu.
1. **Ověření certifikátu** – kontrola dokumentace Akamai (volitelné)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Konfigurace ověřování

1. Adresa URL – zadejte adresu URL shodnou s průsečíkem identity (tady se uživatelé přesměrují po ověření).
2. Odhlašovací adresa URL: Aktualizujte adresu URL pro odhlášení.
3. Podepsat požadavek SAML: výchozí není zaškrtnuto.
4. V případě souboru metadat IDP přidejte aplikaci do konzoly služby Azure AD.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Ověřování na základě hlaviček

Akamai ověřování na základě hlaviček

1. Výběr **vlastního formuláře http** Průvodce přidáním aplikací.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure05.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure06.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure07.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Ověření

![Konfigurace Akamai](./media/header-akamai-tutorial/configure09.png)

![Konfigurace Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Služby

1. Klikněte na Uložit a přejděte na ověřování.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Rozšířená nastavení

1. V **záhlaví HTTP zákazníka**zadejte atribut **CustomerHeader** a **SAML**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure12.png)

1. Klikněte na **Uložit a přejděte na tlačítko nasazení** .

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Nasazení aplikace

1. Klikněte na tlačítko **nasadit aplikaci** .

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure14.png)

1. Ověřte, že aplikace byla úspěšně nasazena.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Ověřování protokolu Kerberos

#### <a name="remote-desktop"></a>Vzdálená plocha

1. V Průvodci přidáním aplikací vyberte **RDP** .

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure16.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure17.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure18.png)

1. Zadejte konektor, který bude obsluhovat.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Ověření

Klikněte na **Uložit a přejděte na služby**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Služby

Klikněte na **Uložit a přejděte na Upřesnit nastavení**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Rozšířená nastavení

Klikněte na **Uložit a přejděte na nasazení**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure22.png)

![Konfigurace Akamai](./media/header-akamai-tutorial/configure23.png)

![Konfigurace Akamai](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Nasazení

#### <a name="ssh"></a>SSH

1. Přejít na Přidat aplikace, zvolit **SSH**.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure25.png)

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure26.png)

1. Nakonfigurujte identitu aplikace.

    ![Konfigurace Akamai](./media/header-akamai-tutorial/configure27.png)

    a. Zadejte název/popis.

    b. Zadejte IP adresu/plně kvalifikovaný název domény aplikačního serveru a port pro SSH.

    c. Zadejte uživatelské jméno/přístupové heslo SSH * ověřte Akamai EAA.

    d. Zadejte název externího hostitele.

    e. Zadejte umístění konektoru a zvolte konektor.

#### <a name="authentication"></a>Ověření

Klikněte na **Uložit a přejděte na služby**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Služby

Klikněte na **Uložit a přejděte na Upřesnit nastavení**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Rozšířená nastavení

Kliknutím na Uložit a přejít na nasazení

![Konfigurace Akamai](./media/header-akamai-tutorial/configure30.png)

![Konfigurace Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Nasazení

Klikněte na **nasadit aplikaci**.

![Konfigurace Akamai](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Aplikace Kerberos

#### <a name="adding-directory"></a>Přidávání adresáře

![Konfigurace Akamai](./media/header-akamai-tutorial/configure33.png)

![Konfigurace Akamai](./media/header-akamai-tutorial/configure34.png)

![Konfigurace Akamai](./media/header-akamai-tutorial/configure35.png)

![Konfigurace Akamai](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Vytvořit testovacího uživatele Akamai

V této části vytvoříte uživatele s názvem B. Simon v Akamai. Pokud chcete přidat uživatele na platformě Akamai, pracujte s [týmem podpory klienta Akamai](https://www.akamai.com/us/en/contact-us/) . Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

## <a name="test-sso"></a>Test SSO

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Akamai, měli byste se automaticky přihlásit k Akamai, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Akamai s Azure AD](https://aad.portal.azure.com/)
