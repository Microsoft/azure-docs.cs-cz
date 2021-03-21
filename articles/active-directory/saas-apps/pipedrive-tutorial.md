---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Pipedrivu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Pipedrivu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 96abe2209d4298f23c47c62fec970430f69cbaf6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654416"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Pipedrivu

V tomto kurzu se dozvíte, jak integrovat Pipedrivu s Azure Active Directory (Azure AD). Když integrujete Pipedrivu s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Pipedrivu.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Pipedrivu svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Pipedrivu odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pipedrivu podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="add-pipedrive-from-the-gallery"></a>Přidání Pipedrivu z Galerie

Pokud chcete nakonfigurovat integraci Pipedrivu do služby Azure AD, musíte přidat Pipedrivu z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **pipedrivu** .
1. Na panelu výsledků vyberte **pipedrivu** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-pipedrive"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Pipedrivu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Pipedrivu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Pipedrivu.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Pipedrivu postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte PIPEDRIVU SSO](#configure-pipedrive-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte pipedrivu Test User](#create-pipedrive-test-user)** -to, abyste měli protějšek B. Simon v pipedrivu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **pipedrivu** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta pipedrivu](mailto:support@pipedrive.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Pipedrivu aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace Pipedrivu několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | ------------ | --------- |
    | e-mail | uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do počítače a také zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](./media/pipedrive-tutorial/certificate-data.png)

1. V části **Nastavení pipedrivu** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Pipedrivu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **pipedrivu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-pipedrive-sso"></a>Konfigurace jednotného přihlašování Pipedrivu

1. V jiném okně prohlížeče se přihlaste k webu Pipedrivu jako správce.

1. Klikněte na **Profil uživatele** a vyberte **Nastavení**.

    ![Snímek obrazovky, který zobrazuje "nastavení" vybrané z nabídky "Profil uživatele".](./media/pipedrive-tutorial/configure-1.png)

1. Přejděte dolů ke službě Security Center a vyberte **jednotné přihlašování**.

    ![Snímek obrazovky, který zobrazuje jednotné přihlašování vybrané v Security Center.](./media/pipedrive-tutorial/configure-2.png)

1. V části **Konfigurace SAML pro pipedrivu** proveďte následující kroky:

    ![Snímek obrazovky zobrazující oddíl S konfigurací M L pro Pipedrivu se zvýrazněnými textovými poli](./media/pipedrive-tutorial/configure-3.png)

    a. Do textového pole **vystavitele** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    b. V poli **Adresa URL jednotného přihlašování (SSO)** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **adresy URL jednotného odhlašování (SLO)** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    d. V textovém poli **certifikát x. 509** Otevřete stažený soubor **certifikátu (Base64)** z Azure Portal do programu Poznámkový blok a zkopírujte jeho obsah a vložte ho do textového pole **certifikát x. 509** a uložte změny.

### <a name="create-pipedrive-test-user"></a>Vytvořit testovacího uživatele Pipedrivu

1. V jiném okně prohlížeče se přihlaste k webu Pipedrivu jako správce.

1. Přejděte dolů na společnost a vyberte **Spravovat uživatele**.

    ![Snímek obrazovky, který zobrazuje "spravovat uživatele" vybraný v nabídce "společnost".](./media/pipedrive-tutorial/user-1.png)

1. Klikněte na **Přidat uživatele**.
    
    ![Snímek obrazovky zobrazující stránku Spravovat uživatele s tlačítkem Přidat uživatele na pravé straně](./media/pipedrive-tutorial/user-2.png)

1. V části **Správa uživatelů** proveďte následující kroky:

    ![Konfigurace pipedrivu](./media/pipedrive-tutorial/user-3.png)

    a. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele, jako je `B.Simon@contoso.com` .

    b. Do textového pole **jméno v prvním** poli zadejte jméno uživatele.

    c. Do textového pole **příjmení** zadejte jméno uživatele.

    d. Klikněte na **Potvrdit a pozvat uživatele**.

## <a name="test-sso"></a>Test SSO 


V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Pipedrivu, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Pipedrivu přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k pipedrivu, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Pipedrivu v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Pipedrivu, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Pipedrivu můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).