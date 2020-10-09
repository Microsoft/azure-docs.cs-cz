---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Orionem SolarWinds | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SolarWinds Orion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: jeedes
ms.openlocfilehash: 723546d6a997d895c1a1df39b558aabb797d0a11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545090"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Orionem SolarWinds

V tomto kurzu se dozvíte, jak integrovat SolarWinds Orion s Azure Active Directory (Azure AD). Když integrujete SolarWinds Orion s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SolarWinds Orion.
* Umožněte uživatelům, aby se automaticky přihlásili k SolarWinds Orion s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné SolarWinds Orion s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SolarWinds Orion podporuje **SP a IDP** iniciované jednotné přihlašování.
* Po nakonfigurování SolarWinds Orion můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-solarwinds-orion-from-the-gallery"></a>Přidání SolarWinds Orion z Galerie

Pokud chcete nakonfigurovat integraci SolarWinds Orion do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat SolarWinds Orion z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SolarWinds Orion** .
1. Z panelu výsledků vyberte **SolarWinds Orion** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SolarWinds Orion

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SolarWinds Orion s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SolarWinds Orion.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s SolarWinds Orion, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SolarWinds Orion SSO](#configure-solarwinds-orion-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit SolarWinds Orion Test User](#create-solarwinds-orion-test-user)** -to znamená, že má protějšek B. Simon v SolarWinds Orion, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Orion SolarWinds** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta podpory SolarWinds Orion](mailto:technicalsupport@solarwinds.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace SolarWinds Orion očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho aplikace SolarWinds Orion očekává, že se v odpovědi SAML vrátí zpátky několik atributů, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Název |  Zdrojový atribut|
    | ----------- | --------- |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |
    | E-mail |uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Orion SolarWinds** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k SolarWinds Orion.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **SolarWinds Orion**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-solarwinds-orion-sso"></a>Konfigurace jednotného přihlašování SolarWinds Orion

1. Přihlaste se ke službě SolarWinds Orion a vyberte **Nastavení**  ->  **všechna nastavení**.

    ![ Konfigurace SolarWinds Orion ](./media/solarwinds-orion-tutorial/settings.png)

1. V části **uživatelské účty** vyberte **Konfigurace SAML**.

    ![ Konfigurace SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Klikněte na **Přidat zprostředkovatele identity**.

    ![ Konfigurace SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Na stránce **Přidat zprostředkovatele identity** proveďte následující kroky:

    ![ Konfigurace SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Přejít na kartu **Konfigurace** .

    b. V textovém poli **název zprostředkovatele identity** zadejte libovolný platný název, třeba `My SSO service` .

    c. Do textového pole **Adresa URL cíle jednotného přihlašování** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d.  Do textového pole **Adresa URL vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    e. Otevřete stažený **certifikát (Base64)** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole pro **podpisový certifikát X. 509** .

    f. Klikněte na **Uložit**.

### <a name="create-solarwinds-orion-test-user"></a>Vytvořit testovacího uživatele SolarWinds Orion

1. Přihlaste se k webu SolarWinds Orion a vyberte **Nastavení**  ->  **všechna nastavení**.

    ![ SolarWinds Orion vytvořit testovacího uživatele ](./media/solarwinds-orion-tutorial/settings.png)

1. V části **uživatelské účty** vyberte **Spravovat účty**.

    ![ SolarWinds Orion vytvořit testovacího uživatele ](./media/solarwinds-orion-tutorial/user-accounts.png)

1. Na kartě **jednotlivé účty** klikněte na **Přidat nový účet**.

    ![ SolarWinds Orion vytvořit testovacího uživatele ](./media/solarwinds-orion-tutorial/create-user.png)

1. Vyberte typ účtu, který potřebujete vytvořit buď jednotlivé uživatele nebo skupiny SAML.

    ![ SolarWinds Orion vytvořit testovacího uživatele ](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  Do textového pole **název ID** zadejte název, který se musí shodovat s uživatelským jménem nebo názvem skupiny přesně stejně jako ve službě Azure AD.

1.  Klikněte na **Další** a potom stránku odešlete.

    ![ SolarWinds Orion vytvořit testovacího uživatele ](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici SolarWinds Orion, měli byste být automaticky přihlášeni k SolarWinds Orion, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si SolarWinds Orion s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit SolarWinds Orion s pokročilou viditelností a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

