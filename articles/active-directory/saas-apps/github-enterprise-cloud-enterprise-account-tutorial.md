---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s účtem GitHub Enterprise Cloud-Enterprise | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a GitHub Enterprise cloudu – podnikový účet.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: jeedes
ms.openlocfilehash: 7360ad5abc7342043152c2da11038b624b0bfadc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649950"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí účtu GitHub Enterprise Cloud – Enterprise

V tomto kurzu se dozvíte, jak integrovat účet GitHub Enterprise Cloud-Enterprise s Azure Active Directory (Azure AD). Když integrujete účet GitHub Enterprise Cloud – Enterprise s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k účtu GitHub Enterprise a všem organizacím v rámci podnikového účtu.


## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* [Účet GitHub Enterprise](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts).
* Uživatelský účet GitHubu, který je vlastníkem podnikového účtu. 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* GitHub Enterprise Cloud – účet Enterprise podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování.
* GitHub Enterprise Cloud – účet Enterprise podporuje **jenom časové** zřizování uživatelů.

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Přidání účtu GitHub Enterprise Cloud – Enterprise z Galerie

Pokud chcete nakonfigurovat integraci účtu GitHub Enterprise Cloud-Enterprise do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat účet GitHub Enterprise Cloud-Enterprise z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **GitHub Enterprise Cloud-Enterprise Account** .
1. Vyberte **GitHub Enterprise Cloud-Enterprise účet** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Konfigurace a testování jednotného přihlašování Azure AD pro GitHub Enterprise Cloud – podnikový účet

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí účtu GitHub Enterprise Cloud – Enterprise pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci účtu GitHub Enterprise Cloud-Enterprise.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí účtu GitHub Enterprise Cloud-Enterprise, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte uživatele Azure AD a testovací uživatelský účet k aplikaci GitHub](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** – Pokud chcete povolit uživatelský účet a otestovat uživatele `B.Simon` pro použití jednotného přihlašování Azure AD.
1. **[Povolení a testování SAML pro účet Enterprise a jeho organizace](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Otestujte jednotné přihlašování pomocí jiného vlastníka podnikového účtu nebo členského účtu organizace](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** – ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **GitHub Enterprise Cloud-Enterprise** Application Integration, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

     Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Nahraďte `<ENTERPRISE-SLUG>` skutečným názvem vašeho účtu GitHub Enterprise.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificateBase64.png)

1. V části **Nastavení účtu GitHub Enterprise Cloud-Enterprise** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal volána `B.Simon` .

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Přiřazení uživatele Azure AD a testovacího uživatelského účtu k aplikaci GitHubu

V této části povolíte `B.Simon` a vašemu uživatelskému účtu budete používat jednotné přihlašování Azure tím, že udělíte přístup k účtu GitHub Enterprise Cloud-Enterprise.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **GitHub Enterprise Cloud – podnikový účet**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte **B. Simon** a uživatelský účet ze seznamu Uživatelé a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Povolení a testování SAML pro účet Enterprise a jeho organizace

Pokud chcete nakonfigurovat jednotné přihlašování na straně **účtu GitHub Enterprise Cloud-Enterprise** , postupujte podle kroků uvedených v [této dokumentaci k GitHubu](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account). 
1. Přihlaste se k GitHub.com pomocí uživatelského účtu, který je [vlastníkem podnikového účtu](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner). 
1. Zkopírujte hodnotu z `Login URL` pole aplikace z Azure Portal a vložte ji do `Sign on URL` pole v účtu GitHub Enterprise v nastavení SAML. 
1. Zkopírujte hodnotu z `Azure AD Identifier` pole aplikace z Azure Portal a vložte ji do `Issuer` pole v účtu GitHub Enterprise v nastavení SAML. 
1. Zkopírujte obsah souboru **certifikátu (Base64)** , který jste si stáhli v předchozích krocích, Azure Portal a vložte je do příslušného pole v nastavení SAML Enterprise Account. 
1. Klikněte na `Test SAML configuration` a potvrďte, že se můžete úspěšně ověřit z účtu GitHub Enterprise do služby Azure AD.
1. Po úspěšném dokončení testu nastavení uložte. 
1. Po prvním ověření pomocí protokolu SAML z účtu GitHub Enterprise se vytvoří _propojená externí identita_ v účtu GitHub Enterprise, který přidruží k účtu uživatele GitHub k účtu uživatele Azure AD.  
 
Po povolení jednotného přihlašování SAML pro svůj účet GitHubu Enterprise je jednotné přihlašování SAML ve výchozím nastavení povolené pro všechny organizace, které vlastní váš podnikový účet. Všichni členové budou muset ověřit pomocí jednotného přihlašování SAML, aby získali přístup k organizacím, kde jsou členy, a pokud se k účtu Enterprise budou muset při přístupu k podnikovému účtu ověřit pomocí jednotného přihlašování SAML.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Testování jednotného přihlašování s jiným vlastníkem podnikového účtu nebo členským účtem organizace

Po nastavení integrace SAML pro účet služby GitHub Enterprise (který platí také pro organizace GitHubu v podnikovém účtu) by měly mít jiní vlastníci podnikového účtu, kteří jsou přiřazeni k aplikaci ve službě Azure AD, přejít na adresu URL účtu GitHub Enterprise ( `https://github.com/enterprises/<enterprise account>` ), ověřit přes SAML a získat přístup k zásadám a nastavením v rámci účtu GitHub Enterprise. 

Vlastník organizace v podnikovém účtu by měl být schopný [pozvat uživatele, aby se připojil ke své organizaci GitHubu](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization). Přihlaste se k GitHub.com pomocí účtu vlastníka organizace a podle kroků v článku Pozvěte `B.Simon` organizaci. Pro případ, že ještě neexistuje uživatelský účet GitHubu, bude nutné ho vytvořit `B.Simon` . 

Otestování přístupu ke organizaci GitHubu v rámci podnikového účtu pomocí `B.Simon` testovacího účtu uživatele:
1. Pozvat `B.Simon` organizaci v rámci podnikového účtu jako vlastník organizace. 
1. Přihlaste se k GitHub.com pomocí uživatelského účtu, který chcete propojit s `B.Simon` uživatelským účtem služby Azure AD.
1. Přihlaste se k Azure AD pomocí `B.Simon` uživatelského účtu.
1. Přejít na organizaci GitHubu. Uživatel by měl být vyzván k ověření prostřednictvím SAML. Po úspěšném ověření SAML `B.Simon` by mělo být umožněn přístup k prostředkům organizace. 

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete účet GitHub Enterprise Cloud – Enterprise, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
