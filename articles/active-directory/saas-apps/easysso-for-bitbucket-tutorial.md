---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s EasySSO pro BitBucket | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EasySSO pro BitBucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 21e5da3884cce6e3a7beff297e40fdc48a3ac761
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724319"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s EasySSO pro BitBucket

V tomto kurzu se dozvíte, jak integrovat EasySSO pro BitBucket s Azure Active Directory (Azure AD). Při integraci EasySSO pro BitBucket se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k EasySSO pro BitBucket.
* Umožněte uživatelům, aby se automaticky přihlásili k EasySSO pro BitBucket s účty Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné EasySSO pro BitBucket, které je povolené pro jednotné přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* EasySSO for BitBucket podporuje jednotné přihlašování (SSO) iniciované v SP a IdP.
* EasySSO for BitBucket podporuje zřizování uživatelů za běhu.

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Přidání EasySSO pro BitBucket z Galerie

Pokud chcete nakonfigurovat integraci EasySSO pro BitBucket do služby Azure AD, musíte přidat EasySSO pro BitBucket z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **EasySSO for Bitbucket** .
1. Z výsledků vyberte **EasySSO pro Bitbucket** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>Konfigurace a testování jednotného přihlašování Azure AD pro EasySSO pro BitBucket

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí EasySSO pro BitBucket pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojený vztah mezi uživatelem Azure AD a souvisejícím uživatelem v EasySSO pro BitBucket.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí EasySSO pro BitBucket, proveďte následující kroky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) .
1. [Nakonfigurujte EasySSO pro jednotné přihlašování Bitbucket SSO](#configure-easysso-for-bitbucket-sso) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte EasySSO pro Bitbucket testovacího uživatele](#create-an-easysso-for-bitbucket-test-user) , aby měl protějšek B. Simon v EasySSO pro BitBucket, která je propojená s reprezentací uživatele v Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce Application Integration **EasySSO for Bitbucket** vyhledejte část **Správa** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednoho Sign-On se stránkou SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v části **základní konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL, která používá následující vzor: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která používá následující vzor: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Vyberte **nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciované **SP** :

    - Do textového pole **přihlašovací adresa URL** zadejte adresu URL, která používá následující vzor: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Obraťte se na [tým podpory EasySSO](mailto:support@techtime.co.nz) a získejte tyto hodnoty, pokud jsou nejisté. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace EasySSO for BitBucket očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Snímek obrazovky s výchozími atributy](common/default-attributes.png)

1. Aplikace EasySSO for BitBucket také očekává několik dalších atributů, které se mají zpětně předat v odpovědi SAML. Tyto části jsou uvedené v následující tabulce. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle svých požadavků.
    
    | Název | Zdrojový atribut|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. userPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | uživatel. pošta |
    | urn: OID: 2.16.840.1.113730.3.1.241 | User. DisplayName |
    | urn: OID: 2.5.4.4 | User. příjmení |
    | urn: OID: 2.5.4.42 | User. křestní jméno |
    
    Pokud mají uživatelé Azure AD nakonfigurovaný **sAMAccountName** , musíte mapovat **název urn: OID: 0.9.2342.19200300.100.1.1** na atribut **sAMAccountName** .
    
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte možnost stáhnout odkazy pro **certifikát (Base64)** nebo **XML metadata federace** . Uložte počítač buď nebo obojím. Později budete potřebovat nakonfigurovat BitBucket EasySSO.

    ![Snímek obrazovky oddílu podpisového certifikátu SAML se zvýrazněnými odkazy ke stažení](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Pokud plánujete nakonfigurovat EasySSO pro BitBucket ručně s certifikátem, musíte také zkopírovat **adresu URL pro přihlášení** a **identifikátor Azure AD** a uložit je v počítači.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Jako **název** zadejte `B.Simon` .
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište heslo.
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k EasySSO pro BitBucket.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **EasySSO pro Bitbucket**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

1. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Konfigurace EasySSO pro jednotné přihlašování BitBucket

1. Chcete-li automatizovat konfiguraci v rámci přiblížení, je nutné nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na tlačítko **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče se kliknutím na **nastavit přiblížení** budete přesměrováni na aplikaci přiblížení. Odtud zadejte přihlašovací údaje správce, které se přihlásí k přiblížení. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-10.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit přiblížení ručně, v jiném okně webového prohlížeče se přihlaste ke svému webu Lupa jako správce.

1. Přejít do části **Správa** .

    ![Snímek obrazovky instance BitBucket se zvýrazněnou ikonou ozubeného kolečka](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Najděte a vyberte **EasySSO**.

    ![Snímek obrazovky snadné možnosti jednotného přihlašování](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Vyberte **SAML**. Tím přejdete do konfiguračního oddílu SAML.

    ![Snímek obrazovky stránky správce EasySSO se zvýrazněným elementem SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Vyberte kartu **certifikáty** a zobrazí se následující obrazovka:

    ![Snímek obrazovky karty certifikáty s zvýrazněnými různými možnostmi](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Vyhledejte **certifikát (Base64)** nebo **soubor metadat** , který jste uložili v předchozí části tohoto kurzu. Můžete pokračovat jedním z následujících způsobů:

    - Použijte soubor federačních **metadat** aplikace, který jste stáhli do místního souboru v počítači. Vyberte přepínač **nahrát** a použijte cestu specifickou pro váš operační systém.

    - Otevřete soubor federačních **metadat** aplikace a zobrazte obsah souboru v libovolném editoru prostého textu. Zkopírujte do schránky. Vyberte **vstup** a vložte obsah schránky do textového pole.
 
    - Proveďte plně ruční konfiguraci. Otevřete federační certifikát aplikace **(Base64)** , abyste zobrazili obsah souboru v libovolném editoru prostého textu. Zkopírujte ho do schránky a vložte ho do textového pole **IDP tokeny podepisování certifikátů** . Pak přejdete na kartu **Obecné** a v poli **Adresa URL příspěvku** a **ID entity** vyplňte příslušné hodnoty pro **přihlašovací adresu URL** a **identifikátor služby Azure AD** , které jste předtím uložili.
 
1. V dolní části stránky vyberte **Save (Uložit** ). Uvidíte, že obsah metadat nebo souborů certifikátů se analyzuje do polí konfigurace. Konfigurace EasySSO pro BitBucket se dokončila.

1. Pokud chcete konfiguraci otestovat, otevřete kartu **vzhled & chování** a vyberte **tlačítko pro přihlášení SAML**. To umožňuje samostatné tlačítko na obrazovce pro přihlášení k BitBucket, konkrétně testování integrace služby Azure AD SAML na konci. Toto tlačítko můžete ponechat zapnuté a nakonfigurovat jeho umístění, barvu a překlad pro režim výroby.

    ![Snímek obrazovky s popisem stránky v SAML & kartu chování s zvýrazněným tlačítkem pro přihlášení SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Pokud máte nějaké problémy, obraťte se na [tým podpory EasySSO](mailto:support@techtime.co.nz).

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Vytvoření EasySSO pro testovacího uživatele BitBucket

V této části vytvoříte uživatele s názvem Britta Simon v BitBucket. EasySSO for BitBucket podporuje zřizování uživatelů za běhu, které je ve výchozím nastavení zakázáno. Pokud ho chcete povolit, musíte explicitně zaškrtnout možnost **vytvořit uživatele při úspěšném přihlášení** v části **Obecné** v konfiguraci modulu plug-in EasySSO. Pokud uživatel ještě v BitBucket neexistuje, vytvoří se po ověření nový.

Pokud však nechcete povolit Automatické zřizování uživatelů při prvním přihlášení uživatele, musí uživatelé existovat v adresářích uživatele, který využívá instance BitBucket. Tento adresář může být například LDAP nebo Atlassian přeplněný.

![Snímek obrazovky s oddílem Obecné v konfiguraci modulu plug-in EasySSO s možností vytvořit uživatele při úspěšném přihlášení zvýrazněný](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na EasySSO pro adresu URL pro přihlášení BitBucket, kde můžete spustit tok přihlášení.

* Přejít na EasySSO pro adresu URL pro přihlašování BitBucket přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k EasySSO pro BitBucket, pro které jste nastavili jednotné přihlašování.

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici EasySSO for BitBucket v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud jste nakonfigurovali v režimu IDP, měli byste se automaticky přihlásit k EasySSO pro BitBucket, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování EasySSO pro BitBucket můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).