---
title: 'Kurz: Integrace Azure Active Directory s místní SharePoint | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a místní SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9491ecfe60e4e15fdf0d1e7b2c8ea05bc9eebf9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198780"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Kurz: Integrace Azure Active Directory s místní SharePoint

V tomto kurzu se dozvíte, jak integrovat místní SharePoint se službou Azure Active Directory (Azure AD).
Integrace místní SharePoint s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k místní SharePoint.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k místní SharePoint (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s místní SharePoint, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Místní SharePoint jednotné přihlášení povolený předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Místní SharePoint podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Přidání místní SharePoint z Galerie

Pokud chcete nakonfigurovat integraci služby SharePoint v místním do služby Azure AD, budete muset přidat místní SharePoint z Galerie na váš seznam spravovaných aplikací SaaS.

**Pokud chcete přidat místní SharePoint z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **místní SharePoint**vyberte **místní SharePoint** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Místní SharePoint v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí místní podle testovacího uživatele volat SharePoint **Britta Simon**.
Pro jednotné přihlašování pro práci vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě SharePoint místní musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s místní SharePoint, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace služby SharePoint v místním jednotného přihlašování](#configure-sharepoint-on-premises-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Udělit přístup pro SharePoint v místním testovacího uživatele](#grant-access-to-sharepoint-on-premises-test-user)**  – Pokud chcete mít protějšek Britta Simon v místní SharePoint, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s místní SharePoint, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **místní SharePoint** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Místní SharePoint domény a adresy URL jednotné přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce: `urn:sharepoint:federation`

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [místní SharePoint tým podpory klienta](https://support.office.com/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

    > [!Note]
    > Poznámka: cesta k souboru, kam jste stáhli soubor certifikátu. Budete potřebovat soubor v pozdější části skriptu prostředí PowerShell pro konfiguraci.

6. Na **nastavit místní SharePoint** tématu, zkopírujte příslušné adresy URL podle vašich požadavků. Pro **jednotné přihlašování – adresa URL služby**, použijte hodnotu následujícímu vzoru: `https://login.microsoftonline.com/_my_directory_id_/wsfed` 

    > [!Note]
    > _my_directory_id_ je id tenanta předplatného služby Azure Ad.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

    > [!NOTE]
    > Aplikace služby SharePoint v místním používá SAML 1.1 tokenu, aby Azure AD očekává žádost WS Fed ze serveru SharePoint server a po ověření, vystavuje SAML 1.1. token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Konfigurace služby SharePoint v místním jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu služby SharePoint v místním společnosti jako správce.

2. **Konfigurace nového důvěryhodného zprostředkovatele identity v SharePoint serveru 2016**

    Přihlásit k serveru SharePoint Server 2016 a otevřete prostředí pro správu služby SharePoint 2016. Zadejte hodnoty $realm (hodnota identifikátoru SharePoint místní domény a adresy URL v části webu Azure portal), $wsfedurl (jednotné přihlašování – adresa URL služby) a $filepath (cesta k souboru do které jste stáhli soubor certifikátu) z webu Azure portal a spuštění Následující příkazy, které nakonfigurují nový důvěryhodného zprostředkovatele identity.

    > [!TIP]
    > Pokud pro vás nová pomocí Powershellu nebo chcete další informace o tom, jak funguje Powershellu, přečtěte si téma [SharePoint Powershellu](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    V dalším kroku použijte následující postup povolení důvěryhodného zprostředkovatele identity pro vaši aplikaci:

    a. V Centru pro správu, přejděte na **webovou aplikaci spravovat** a vyberte webovou aplikaci, kterou chcete zabezpečit pomocí služby Azure AD.

    b. Na pásu karet klikněte na tlačítko **zprostředkovatelé ověřování** a vyberte zónu, kterou chcete použít.

    c. Vyberte **zprostředkovatele Identity pro důvěryhodného** a vyberte identifikovat zprostředkovatele jste právě zaregistrovali, s názvem *AzureAD*.

    d. V nastavení adresy URL přihlašovací stránky, vyberte **vlastní přihlašovací stránku** a zadejte hodnotu "/_trust/".

    e. Klikněte na **OK**.

    ![Konfigurace poskytovatele ověřování](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Některé externí uživatelé nebudou moct používat Tato integrace jednotné přihlašování s jejich hlavní název uživatele bude mít pozměnění hodnotu jako `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Co nejdříve povolíme konfigurace vlastní aplikace pro zpracování hlavní název uživatele v závislosti na typu uživatele. Po, který by měl být všichni uživatelé typu Host moct používat jednotné přihlašování bez problémů jako zaměstnance organizace.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Udělení přístupu k SharePoint v místním testovacího uživatele

Uživatelé, kteří budou protokolovat do služby Azure AD a přístup k Sharepointu musí mít udělen přístup k aplikaci pomocí následujícího postupu k nastavení oprávnění pro přístup k webové aplikaci.

1. V Centru pro správu, klikněte na tlačítko **správy aplikací**.

2. Na **správy aplikací** stránku, **webových aplikací** klikněte na tlačítko **spravovat webové aplikace**.

3. Klikněte na příslušnou webovou aplikaci a pak klikněte na tlačítko **zásady uživatele**.

4. V zásadách pro webovou aplikaci, klikněte na tlačítko **Add Users**.

    ![Hledání pro uživatele podle své deklarace názvu](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. V **Add Users** dialogového okna klikněte na příslušnou zónu v **zóny**a potom klikněte na tlačítko **Další**.

6. V **zásady pro webovou aplikaci** v dialogu **výběr uživatelů** klikněte na tlačítko **Procházet** ikonu.

7. V **najít** textové pole, typ **uživatel instančního objektu name(UPN)** hodnota, pro který jste nakonfigurovali místní aplikace SharePoint ve službě Azure AD a klikněte na tlačítko **hledání**. </br>Příklad: *brittasimon@contoso.com*.

8. V části Azure AD v zobrazení seznamu, vyberte název vlastnosti a klikněte na tlačítko **přidat** klikněte **OK** zavřete dialogové okno.

9. V části oprávnění **úplné řízení**.

    ![Udělení úplného řízení uživateli deklarací identity](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Klikněte na tlačítko **Dokončit**a potom klikněte na tlačítko **OK**.

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurace jednoho důvěryhodného zprostředkovatele identity pro více webových aplikací

Konfigurace se dá použít pro jednu webovou aplikaci, ale vyžaduje další konfiguraci, pokud máte v úmyslu použit stejný zprostředkovatel identity pro důvěryhodného pro více webových aplikací. Předpokládejme například, jsme měli rozšířené webové aplikace pro použití adresy URL `https://portal.contoso.local` a nyní chcete ověřit uživatele pro `https://sales.contoso.local` také. Provedete to tak, musíme aktualizovat zprostředkovatele identity případném dalším sdílení dodržovat parametr WReply a aktualizovat registraci aplikace ve službě Azure AD přidat adresu URL odpovědi.

1. Na webu Azure Portal otevřete adresář Azure AD. Klikněte na tlačítko **registrace aplikací**, pak klikněte na tlačítko **zobrazit všechny aplikace**. Klikněte na aplikaci, kterou jste předtím vytvořili (integrace se Sharepointem SAML).

2. Klikněte na tlačítko **nastavení**.

3. V okně nastavení klikněte na tlačítko **adresy URL odpovědí**. 

4. Přidejte adresu URL pro další webové aplikace se `/_trust/default.aspx` připojena k adrese URL (například `https://sales.contoso.local/_trust/default.aspx`) a klikněte na tlačítko **Uložit**.

5. Na serveru SharePoint server, otevřete **prostředí pro správu služby SharePoint 2016** a spusťte následující příkazy, pomocí názvu vydavatele tokenu důvěryhodnou identitu, který jste použili dříve.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. V Centru pro správu přejděte na webovou aplikaci a povolit existujících důvěryhodného zprostředkovatele identity. Nezapomeňte si také nakonfigurovat adresu URL přihlašovací stránky jako vlastní přihlašovací stránku `/_trust/`.

7. V Centru pro správu, klikněte na webovou aplikaci a zvolte **zásady uživatele**. Přidáte uživatele s příslušnými oprávněními, jak je uvedeno dříve v tomto článku.

### <a name="fixing-people-picker"></a>Oprava výběr osob

Uživatelé nyní se můžete přihlásit do služby SharePoint 2016 pomocí identit z Azure AD, ale stále existují příležitosti pro zlepšení uživatelského prostředí. Například hledání uživatele představuje více výsledky hledání v výběr osob. Neexistuje výsledek hledání pro jednotlivé typy 3 deklarace identity, které byly vytvořeny v mapování deklarací identity. Vybrat uživatele s využitím výběr osob, musí přesně zadejte své uživatelské jméno a zvolte **název** deklarace identity výsledek.

![Výsledky hledání deklarací identity](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Neexistuje žádné ověřování na tyto hodnoty vyhledat, což může vést k překlepy nebo typ přiřazení jako deklarace identity uživatele nechtěně výběru chybného **příjmení** deklarací identity. To může zabránit uživatelům v úspěšně přístup k prostředkům.

Abychom v tomto scénáři je open source řešení [AzureCP](https://yvand.github.io/AzureCP/) zprostředkovatele vlastní deklarace identity, která poskytuje pro SharePoint 2016. Azure AD Graph se bude používat pro vyřešení co uživatelé zadat a provést ověření. Další informace najdete na [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k místní SharePoint.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **místní SharePoint**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **místní SharePoint**.

    ![Propojení místní služby SharePoint v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sharepoint-on-premises-test-user"></a>Vytvoření služby SharePoint v místním testovacího uživatele

V této části vytvořte uživatele Britta Simon v místní SharePoint. Práce s [tým podpory služby SharePoint v místním](https://support.office.com/) přidejte uživatele do místní platforma SharePoint. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SharePoint v místním na přístupovém panelu, vám by měl být automaticky přihlášeni SharePoint i místní pro které můžete nastavit jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
