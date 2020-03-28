---
title: 'Kurz: Integrace Azure Active Directory s místním SharePointem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SharePointem místně.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 22b673ef481593247b6ee1007c13390a498c66be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048621"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Kurz: Integrace jednotného přihlašování (Jednotné přihlašování) služby Azure Active Directory s místním SharePointem

V tomto kurzu se dozvíte, jak integrovat SharePoint místně s Azure Active Directory (Azure AD). Když integrujete SharePoint místně s Azure AD, můžete:

* Řízení ve službě Azure AD, který má přístup k SharePointu místně.
* Umožněte uživatelům, aby se automaticky přihlásili k SharePointu místně pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění – na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné, můžete získat bezplatný [účet](https://azure.microsoft.com/free/).
* SharePoint místní jednotné přihlášení (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete přisypná ní služby Azure AD v testovacím prostředí.

* SharePoint místní podporuje **sp** iniciované sso
* Jakmile nakonfigurujete SharePoint místně, můžete vynutit ovládací prvky relace, které chrání exfiltraci a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutit řízení relací pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
* Naleznete tento [odkaz](https://docs.microsoft.com/archive/blogs/kaevans/sharepoint-2013-user-profile-sync-for-claims-users) se dozvíte, jak synchronizovat profily uživatelů ze SharePointu on-premise do Azure AD

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Přidání SharePointu místně z galerie

Pokud chcete nakonfigurovat integraci SharePointu místně do Azure AD, musíte přidat SharePoint místně z galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory.**

    > [!NOTE]
    > Pokud by prvek neměl být k dispozici, lze jej také otevřít prostřednictvím pevného propojení **Všechny služby** v horní části levého navigačního panelu. V následujícím přehledu je odkaz **služby Azure Active Directory** umístěn v části **Identita** nebo jej lze vyhledat pomocí textového pole filtru.

1. Přejděte do **podnikových aplikací** a vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte **možnost Nová aplikace**.
1. V části **Přidat z galerie** zadejte do vyhledávacího pole **SharePoint místně.**
1. Z panelu výsledků vyberte **SharePoint místně** a přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharepoint-on-premises"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SharePoint místně

Konfigurace a testování služby Azure AD SSO s SharePointem místně pomocí testovacího uživatele s názvem **B.Simon**. Aby služby Přisazovací služby fungovaly, musíte vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v místním SharePointu.

Pokud chcete nakonfigurovat a otestovat přisypování služby Azure AD s místním SharePointem, vyplňte následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace sharepointového místního jednotného přihlašování](#configure-sharepoint-on-premises-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Vytvořte skupinu zabezpečení Azure AD na webu Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** – povolení nové skupiny zabezpečení ve službě Azure AD pro jednotné přihlašování.
5. **[Udělte přístup k místní skupině zabezpečení SharePointu](#grant-access-to-sharepoint-on-premises-security-group)** – udělte přístup pro konkrétní skupinu do služby Azure AD.
6. **[Přiřaďte skupinu zabezpečení Azure AD na webu Azure Portal](#assign-the-azure-ad-security-group-in-the-azure-portal)** – k přiřazení konkrétní skupiny k Azure AD pro ověřování.
7. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s místním SharePointem, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace místních aplikací **SharePointu** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:


    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Do pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`urn:sharepoint:federation`

    c. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL, identifikátorem a adresou URL pro odpověď. Chcete-li získat tyto hodnoty, obraťte se [na místní tým podpory klienta SharePointu.](https://support.office.com/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

    > [!Note]
    > Poznamenejte si cestu k souboru, do kterého jste stáhli soubor certifikátu, protože jej potřebujete použít později ve skriptu Prostředí PowerShell pro konfiguraci.

6. V části **Nastavit SharePoint v místním** prostředí zkopírujte příslušnou adresu URL podle vašeho požadavku. Pro **adresu URL služby jednotného přihlašování**použijte hodnotu následujícího vzoru:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ je id klienta předplatného Azure Ad.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

    > [!NOTE]
    > Místní aplikace Sharepointu používá token SAML 1.1, takže Azure AD očekává požadavek WS Fed ze sharepointového serveru a po ověření vydá SAML 1.1. Token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Konfigurace sharepointového místního jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste k webu místní společnosti SharePointu jako správce.

2. **Konfigurace nového důvěryhodného poskytovatele identity na SharePoint Serveru 2016**

    Přihlaste se k serveru SharePoint Serveru 2016 a otevřete Prostředí pro správu SharePointu 2016. Vyplňte hodnoty $realm (hodnota identifikátoru z části Místní domény a adresy URL SharePointu na webu Azure Portal), $wsfedurl (adresa URL služby jednotného přihlašování) a $filepath (cesta k souboru, do kterého jste stáhli soubor certifikátu) z webu Azure Portal a spuštění následující příkazy pro konfiguraci nového důvěryhodného zprostředkovatele identity.

    > [!TIP]
    > Pokud s powershellem začínáte používat nebo se chcete dozvědět víc o tom, jak PowerShell funguje, přečtěte [si část SharePoint PowerShellu](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

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
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Dále postupujte takto a povolte důvěryhodného zprostředkovatele identity pro vaši aplikaci:

    a. V centrální správě přejděte na **Spravovat webovou aplikaci** a vyberte webovou aplikaci, kterou chcete zabezpečit pomocí Azure AD.

    b. Na pásu karet klikněte na **Zprostředkovatelé ověřování** a vyberte zónu, kterou chcete použít.

    c. Vyberte **zprostředkovatele důvěryhodné identity** a vyberte identifikovat zprostředkovatele, který jste právě zaregistrovali s názvem *AzureAD*.

    d. V nastavení adresy URL přihlašovací stránky vyberte **Vlastní přihlašovací stránku** a zadejte hodnotu "/_trust/".

    e. Klikněte na tlačítko **OK**.

    ![Konfigurace zprostředkovatele ověřování](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Někteří externí uživatelé nebudou moci používat tuto integraci jednotného přihlášení, protože jejich hlavní `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`název uživatele bude mít pošitou hodnotu něco jako . Brzy umožníme zákazníkům app config o tom, jak zacházet s HLAVNÍ HOL v závislosti na typu uživatele. Poté by všichni uživatelé typu Host měli mít možnost bezproblémově používat jednotné přihlašovatjako zaměstnance organizace.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Vytvoření skupiny zabezpečení Azure AD na webu Azure Portal

1. Klikněte na **Službu Azure Active Directory > všechny skupiny**.

    ![Vytvoření skupiny zabezpečení Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Klikněte na **Nová skupina**:

    ![Vytvoření skupiny zabezpečení Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Vyplňte **typ skupiny**, **název skupiny**, **popis skupiny**, **typ členství**. Klikněte na šipku pro výběr členů, pak hledat nebo klikněte na člena, který chcete přidat do skupiny. Kliknutím na **Vyberte,** chcete-li přidat vybrané členy, klikněte na **Vytvořit**.

    ![Vytvoření skupiny zabezpečení Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Chcete-li přiřadit skupiny zabezpečení Služby Azure Active Directory k SharePointu místně, bude nutné nainstalovat a nakonfigurovat [AzureCP](https://yvand.github.io/AzureCP/) v místní sharepointové farmě nebo vyvinout a nakonfigurovat alternativního vlastního poskytovatele deklarací identity pro SharePoint.  Pokud azurecp nepoužíváte, přečtěte si další část s informacemi na konci dokumentu pro vytvoření vlastního poskytovatele deklarací identity.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Udělení přístupu k místní skupině zabezpečení SharePointu

**Konfigurace skupin zabezpečení a oprávnění pro registraci aplikace**

1. Na webu Azure Portal vyberte **Azure Active Directory**a pak vyberte Registrace **aplikací**.

    ![Okno podnikových aplikací](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Do vyhledávacího pole zadejte a vyberte **SharePoint místně**.

    ![SharePoint v místním prostředí v seznamu výsledků](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Klikněte na **Manifest**.

    ![Možnost manifestu](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Změnit `groupMembershipClaims` `NULL`: `groupMembershipClaims`, `SecurityGroup`Do : . Poté klikněte na uložit

    ![Upravit manifest](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Klikněte na **Nastavení**a potom klikněte na **Požadovaná oprávnění**.

    ![Požadovaná oprávnění](./media/sharepoint-on-premises-tutorial/settings.png)

6. Klikněte na **Přidat** a **vyberte rozhraní API**.

    ![Přístup k rozhraní API](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Přidejte **windows azure active directory** a rozhraní Microsoft Graph **API**, ale je možné vybrat pouze jeden po druhém.

    ![Výběr rozhraní API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Vyberte Službu Windows Azure Active Directory, zkontrolujte číst data adresáře a klikněte na Vybrat. Vraťte se a přidejte Microsoft Graph a vyberte pro něj také číst data adresáře.  Klikněte na Vybrat a klikněte na Hotovo.

    ![Povolit přístup](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Nyní v části Požadovaná nastavení klikněte na **Udělit oprávnění** a potom klikněte na Tlačítko Ano na udělení oprávnění.

    ![Udělit oprávnění](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Zkontrolujte v části oznámení a zjistěte, zda byla oprávnění úspěšně udělena.  Pokud tomu tak není, azurecp nebude fungovat správně a nebude možné nakonfigurovat SharePoint místně pomocí skupin zabezpečení Azure Active Directory.

10. Nakonfigurujte AzureCP na místní farmě SharePointu nebo v alternativním řešení vlastního poskytovatele deklarací identity.  V tomto příkladu používáme AzureCP.

    > [!NOTE]
    > Upozorňujeme, že AzureCP není produkt společnosti Microsoft ani ho nepodporuje technická podpora společnosti Microsoft. Stažení, instalace a konfigurace AzureCP v místní sharepointové farmě[https://yvand.github.io/AzureCP/](https://yvand.github.io/AzureCP/) 

11. **Udělte přístup skupině zabezpečení Služby Azure Active Directory v místním SharePointu** :- Skupinám musí být udělen přístup k aplikaci v místním SharePointu.  Pomocí následujících kroků nastavte oprávnění pro přístup k webové aplikaci.

12. V centrální správě klikněte na Správa aplikací, Správa webových aplikací, pak vyberte webovou aplikaci pro aktivaci pásu karet a klikněte na Zásady uživatele.

    ![Ústřední správa](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. V části Zásady pro webovou aplikaci klikněte na Přidat uživatele a vyberte zónu a klikněte na Další.  Klikněte na adresář.

    ![Zásady pro webové aplikace](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Potom vyhledejte a přidejte skupinu zabezpečení Služby Azure Active Directory a klikněte na OK.

    ![Přidání skupiny zabezpečení](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Vyberte oprávnění a klikněte na Dokončit.

    ![Přidání skupiny zabezpečení](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. V části Zásady pro webové aplikace se přidá skupina Azure Active Directory Group.  Deklarace skupiny zobrazuje ID objektu skupiny zabezpečení služby Azure Active Directory pro uživatelské jméno.

    ![Přidání skupiny zabezpečení](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Přejděte do kolekce sharepointových webů a přidejte tam také skupinu. Klikněte na Nastavení webu a potom klikněte na Oprávnění webu a Udělit oprávnění.  Vyhledejte deklaraci role skupiny, přiřaďte úroveň oprávnění a klikněte na Sdílet.

    ![Přidání skupiny zabezpečení](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurace jednoho důvěryhodného zprostředkovatele identity pro více webových aplikací

Konfigurace funguje pro jednu webovou aplikaci, ale potřebuje další konfiguraci, pokud máte v úmyslu použít stejného důvěryhodného zprostředkovatele identity pro více webových aplikací. Předpokládejme například, že jsme rozšířili `https://portal.contoso.local` webovou aplikaci tak, `https://sales.contoso.local` aby používala adresu URL, a nyní chcete ověřit i uživatele. Chcete-li to provést, musíme aktualizovat zprostředkovatele identity, aby byl respektovat parametr WReply a aktualizovat registraci aplikace ve službě Azure AD a přidat adresu URL odpovědi.

1. Na webu Azure Portal otevřete adresář Azure AD. Klikněte na **Registrace aplikací**a potom klikněte na **Zobrazit všechny aplikace**. Klikněte na aplikaci, kterou jste vytvořili dříve (Integrace Aplikace SHAREPoint SAML).

2. Klikněte na příkaz **Nastavení**.

3. V okně nastavení klepněte na tlačítko Odpovědět na **adresy URL**.

4. Přidejte adresu URL další `/_trust/default.aspx` webové aplikace s připojenou `https://sales.contoso.local/_trust/default.aspx`k adrese URL (například) a klepněte na tlačítko **Uložit**.

5. Na SharePointserveru otevřete **Prostředí pro správu SharePointu 2016** a spusťte následující příkazy s použitím názvu vydavatele důvěryhodného tokenu identity, který jste použili dříve.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```

6. V centrální správě přejděte do webové aplikace a povolte stávajícího důvěryhodného zprostředkovatele identity. Nezapomeňte také nakonfigurovat adresu URL přihlašovací stránky jako `/_trust/`vlastní přihlašovací stránku .

7. V centrální správě klikněte na webovou aplikaci a zvolte **Zásady uživatele**. Přidejte uživatele s příslušnými oprávněními, jak je uvedeno dříve v tomto článku.

### <a name="fixing-people-picker"></a>Oprava výběru lidí

Uživatelé se teď můžou přihlásit k SharePointu 2016 pomocí identit z Azure AD, ale pořád existují příležitosti ke zlepšení uživatelského prostředí. Například hledání uživatele představuje více výsledků hledání v výběru osob. Existuje výsledek hledání pro každý ze 3 typů deklarací, které byly vytvořeny v mapování deklarací. Chcete-li vybrat uživatele pomocí nástroje pro výběr osob, musíte přesně zadat jeho uživatelské jméno a zvolit výsledek deklarace **názvu.**

![Nároky výsledky hledání](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Neexistuje žádné ověření na hodnoty, které hledáte, což může vést k překlepy nebo uživatelé omylem výběru nesprávného typu deklarace pro přiřazení, jako je například **deklarace surname.** To může zabránit uživatelům v úspěšném přístupu k prostředkům.

Chcete-li pomoci s tímto scénářem, je open source řešení s názvem [AzureCP,](https://yvand.github.io/AzureCP/) který poskytuje vlastní poskytovatele deklarací identity pro SharePoint 2016. Pomocí rozhraní Microsoft Graph API vyřešíte, co uživatelé zadají a provedou ověření. Další informace najdete na [webu AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Přiřazení skupiny zabezpečení Azure AD na webu Azure Portal

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **SharePoint místně**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **SharePoint místně**.

    ![Místní odkaz SharePointu v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klepněte na **tlačítko Přidat uživatele**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. Vyhledejte skupinu zabezpečení, kterou chcete použít, a kliknutím na skupinu ji přidejte do oddílu Vybrat členy. Klepněte na tlačítko **Vybrat**a potom klepněte na **tlačítko Přiřadit**.

    ![Skupina zabezpečení hledání](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Zkontrolujte oznámení v panelu nabídek, abyste byli upozorněni, že skupina byla úspěšně přiřazena k podnikové aplikaci na webu Azure Portal.

### <a name="create-sharepoint-on-premises-test-user"></a>Vytvoření místního testovacího uživatele SharePointu

V této části vytvoříte uživatele s názvem Britta Simon v sharepointovém místním prostředí. Spolupracujte s [místním týmem podpory SharePointu](https://support.office.com/) a přidejte uživatele na místní platformě SharePointu. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici SharePoint místně na přístupovém panelu, měli byste být automaticky přihlášeni k sharepointovém místnímu, pro který nastavíte přiřazující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)