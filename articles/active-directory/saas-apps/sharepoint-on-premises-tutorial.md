---
title: 'Kurz: Integrace Azure Active Directory s místní službou SharePoint | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SharePointem v místním prostředí.
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
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 9c956f89d890f93a887d2412c74c906095acf4db
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164357"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Kurz: Integrace Azure Active Directory s místním SharePointem

V tomto kurzu se naučíte integrovat SharePoint místně pomocí Azure Active Directory (Azure AD).
Integrace služby SharePoint v místním prostředí se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k místnímu SharePointu.
* Uživatelům můžete povolit, aby se automaticky přihlásili k místnímu SharePointu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s místním SharePointem, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Místní předplatné s povolenou službou SharePoint pro jednotné přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Místní SharePoint podporuje jednotné přihlašování v systému **SP**

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Místní přidání služby SharePoint z Galerie

Pokud chcete nakonfigurovat integraci služby SharePoint místně do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat místní SharePoint z galerie.

**Pokud chcete z Galerie přidat místní SharePoint, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

    > [!NOTE]   
    > Pokud by element neměl být k dispozici, lze jej také otevřít prostřednictvím odkazu pevný **všechny služby** v horní části levého navigačního panelu. V následujícím přehledu se **Azure Active Directory** odkaz nachází v části **Identita** nebo ho můžete vyhledat pomocí textového pole filtrovat.

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **místní SharePoint**, z panelu výsledků vyberte **místní SharePoint** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Místní SharePoint v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby SharePoint místně na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba vytvořit odkazový vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v SharePointu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby SharePoint v místním prostředí, musíte dokončit tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte místní jednotné přihlašování SharePoint](#configure-sharepoint-on-premises-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvořte v Azure Portal skupinu zabezpečení Azure AD](#create-an-azure-ad-security-group-in-the-azure-portal)** , abyste ve službě Azure AD povolili novou skupinu zabezpečení pro jednotné přihlašování.
5. **[Udělení přístupu k místní skupině zabezpečení služby SharePoint](#grant-access-to-sharepoint-on-premises-security-group)** – udělení přístupu pro konkrétní skupinu do Azure AD.
6. Přiřaďte k ověřování **[skupinu zabezpečení Azure AD v Azure Portal](#assign-the-azure-ad-security-group-in-the-azure-portal)** – pro přiřazení konkrétní skupiny k Azure AD.
7. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí místní služby SharePoint, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **místní aplikace SharePoint** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v místní doméně a adresách URL služby SharePoint](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`urn:sharepoint:federation`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se [na tým podpory místního klienta služby SharePoint](https://support.office.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

    > [!Note]
    > Poznamenejte si prosím cestu k souboru, do které jste stáhli soubor certifikátu, protože ho budete muset použít později ve skriptu PowerShellu pro konfiguraci.

6. V části **nastavit místní SharePoint** zkopírujte příslušné adresy URL podle vašich požadavků. Pro **adresu URL služby jednotného přihlašování**použijte hodnotu následujícího vzoru:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ je ID tenanta předplatného služby Azure AD.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    > [!NOTE]
    > Místní aplikace SharePoint používá token SAML 1,1, takže služba Azure AD očekává požadavek WS dodaný ze SharePoint serveru a po ověření vydá SAML 1,1. klíčové.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Konfigurace místního jednotného přihlašování pro SharePoint

1. V jiném okně webového prohlížeče se přihlaste k místnímu firemnímu webu služby SharePoint jako správce.

2. **Konfigurace nového důvěryhodného zprostředkovatele identity v SharePoint serveru 2016**

    Přihlaste se k serveru SharePoint Server 2016 a otevřete prostředí pro správu SharePointu 2016. Vyplňte hodnoty $realm (hodnota identifikátoru z části místní doména a adresy URL serveru SharePoint v Azure Portal), $wsfedurl (adresa URL služby jednotného přihlašování) a $filepath (cesta k souboru, do které jste stáhli soubor certifikátu) z Azure Portal a spusťte příkaz. následující příkazy nakonfigurujte nového důvěryhodného zprostředkovatele identity.

    > [!TIP]
    > Pokud s prostředím PowerShell ještě začínáte a chcete získat další informace o tom, jak prostředí PowerShell funguje, podívejte se na téma [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

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

    Potom postupujte podle těchto kroků a povolte důvěryhodného zprostředkovatele identity pro vaši aplikaci:

    a. V centrální správě přejděte na **Správa webové aplikace** a vyberte webovou aplikaci, kterou chcete zabezpečit pomocí Azure AD.

    b. Na pásu karet klikněte na **Zprostředkovatelé ověřování** a vyberte zónu, kterou chcete použít.

    c. Vyberte **Důvěryhodný zprostředkovatel identity** a vyberte poskytovatele identifikace, kterého jste právě zaregistrovali s názvem *AzureAD*.

    d. V nastavení adresa URL přihlašovací stránky vyberte **vlastní přihlašovací stránka** a zadejte hodnotu "/_trust/".

    e. Klikněte na **OK**.

    ![Konfigurace poskytovatele ověřování](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Někteří externí uživatelé nebudou moct tuto integraci s jednotným přihlašováním použít, protože jejich hlavní název uživatele (UPN) bude mít pozměněnou hodnotu `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`jako. Brzy umožníme konfiguraci aplikací pro zákazníky, jak se má v závislosti na typu uživatele zpracovat hlavní název uživatele (UPN). Jakmile všichni uživatelé typu Host by měli být schopni používat jednotné přihlašování jako zaměstnanci organizace.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména**`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Vytvoření skupiny zabezpečení Azure AD v Azure Portal

1. Klikněte na **Azure Active Directory > všechny skupiny**.

    ![Vytvoření skupiny zabezpečení Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Klikněte na **Nová skupina**:

    ![Vytvoření skupiny zabezpečení Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Vyplňte pole **typ skupiny**, **název skupiny**, **Popis skupiny**, **typ členství**. Klikněte na šipku a vyberte členy, vyhledejte nebo klikněte na člen, který chcete přidat do skupiny. Kliknutím na **Vybrat** přidejte vybrané členy a pak klikněte na **vytvořit**.

    ![Vytvoření skupiny zabezpečení Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Aby bylo možné přiřadit skupiny zabezpečení Azure Active Directory k místnímu serveru SharePoint, bude nutné nainstalovat a nakonfigurovat [AzureCP](https://yvand.github.io/AzureCP/) v místní farmě služby SharePoint nebo vyvinout a nakonfigurovat alternativního vlastního zprostředkovatele deklarací pro službu SharePoint.  Pokud nepoužíváte AzureCP, přečtěte si část s dalšími informacemi na konci dokumentu o vytvoření vlastního zprostředkovatele deklarací identity.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Udělení přístupu k místní skupině zabezpečení služby SharePoint

**Konfigurace skupin zabezpečení a oprávnění k registraci aplikace**

1. V Azure Portal vyberte **Azure Active Directory**a pak vyberte **Registrace aplikací**.

    ![Okno podnikových aplikací](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Do vyhledávacího pole zadejte a vyberte **SharePoint místně**.

    ![Místní SharePoint v seznamu výsledků](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Klikněte na **manifest**.

    ![Možnost manifestu](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Upravit `groupMembershipClaims`: `NULL`, na `groupMembershipClaims`: .`SecurityGroup` Pak klikněte na Uložit.

    ![Upravit manifest](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Klikněte na **Nastavení**a potom klikněte na **požadovaná oprávnění**.

    ![Požadovaná oprávnění](./media/sharepoint-on-premises-tutorial/settings.png)

6. Klikněte na **Přidat** a pak **Vyberte rozhraní API**.

    ![API Access](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Přidejte rozhraní **Windows Azure Active Directory** i **Microsoft Graph API**, ale je možné ho vybrat jen po jednom.

    ![Výběr rozhraní API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Vyberte Windows Azure Active Directory, zaškrtněte číst data adresáře a klikněte na vybrat. Vraťte se a přidejte Microsoft Graph a vyberte možnost číst data adresáře i pro něj.  Klikněte na vybrat a potom na Hotovo.

    ![Povolit přístup](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Teď v části požadovaná nastavení klikněte na **udělit oprávnění** a potom kliknutím na Ano udělíte oprávnění.

    ![Udělit oprávnění](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Pokud chcete zjistit, jestli se oprávnění úspěšně udělila, zkontrolujte v části oznámení.  Pokud nejsou, AzureCP nebude správně fungovat a nebude možné nakonfigurovat místní SharePoint pomocí skupin zabezpečení Azure Active Directory.

10. Nakonfigurujte AzureCP pro místní farmu služby SharePoint nebo alternativní řešení vlastního zprostředkovatele deklarací identity.  V tomto příkladu používáme AzureCP.

    > [!NOTE]
    > Upozorňujeme, že AzureCP není produktem společnosti Microsoft ani není podporován technickou podporou společnosti Microsoft. Stažení, instalace a konfigurace AzureCP na místní SharePointové farmě na https://yvand.github.io/AzureCP/ 

11. **Udělit přístup ke skupině zabezpečení Azure Active Directory v místní službě SharePoint** : – skupiny musí mít udělen přístup k aplikaci v místním SharePointu.  Pomocí následujících kroků nastavte oprávnění pro přístup k webové aplikaci.

12. V centrální správě klikněte na Správa aplikací, spravovat webové aplikace, vyberte webovou aplikaci pro aktivaci pásu karet a klikněte na zásady uživatele.

    ![Centrální správa](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. V části zásady pro webovou aplikaci klikněte na Přidat uživatele a vyberte zónu, klikněte na další.  Klikněte na adresář.

    ![Zásady pro webovou aplikaci](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Pak vyhledejte a přidejte skupinu zabezpečení Azure Active Directory a klikněte na OK.

    ![Přidání skupiny zabezpečení](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Vyberte oprávnění a pak klikněte na Dokončit.

    ![Přidání skupiny zabezpečení](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. V části zásady pro webovou aplikaci se přidá skupina Azure Active Directory.  Deklarace skupiny zobrazuje ID objektu skupiny zabezpečení Azure Active Directory pro uživatelské jméno.

    ![Přidání skupiny zabezpečení](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Přejděte do kolekce webů SharePointu a přidejte taky tuto skupinu. Klikněte na nastavení lokality a pak klikněte na oprávnění webu a udělení oprávnění.  Vyhledejte deklaraci identity role skupiny, přiřaďte úroveň oprávnění a klikněte na sdílet.

    ![Přidání skupiny zabezpečení](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurace jednoho důvěryhodného zprostředkovatele identity pro více webových aplikací

Konfigurace funguje pro jednu webovou aplikaci, ale vyžaduje další konfiguraci, pokud máte v úmyslu použít stejného důvěryhodného poskytovatele identity pro více webových aplikací. Předpokládejme například, že jsme rozšířili webovou aplikaci, aby používala adresu `https://portal.contoso.local` URL, a teď chcete ověřovat i `https://sales.contoso.local` uživatele. Abychom to mohli udělat, musíme aktualizovat poskytovatele identity, aby se zavedl parametr WReply, a aktualizovat registraci aplikace ve službě Azure AD, aby se přidala adresa URL odpovědi.

1. V Azure Portal otevřete adresář služby Azure AD. Klikněte na **Registrace aplikací**a pak klikněte na **Zobrazit všechny aplikace**. Klikněte na aplikaci, kterou jste předtím vytvořili (integrace služby SharePoint SAML).

2. Klikněte na **Nastavení**.

3. V okně Nastavení klikněte na **adresy URL odpovědí**.

4. Přidejte adresu URL pro další webovou aplikaci s `/_trust/default.aspx` připojením k adrese URL ( `https://sales.contoso.local/_trust/default.aspx`například) a klikněte na **Uložit**.

5. Na serveru SharePoint otevřete **prostředí Management Shell služby sharepoint 2016** a pomocí názvu vystavitele důvěryhodného tokenu identity, který jste použili dříve, spusťte následující příkazy.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. V centrální správě přejdete do webové aplikace a povolíte stávajícího důvěryhodného zprostředkovatele identity. Nezapomeňte taky nakonfigurovat adresu URL přihlašovací stránky jako vlastní přihlašovací stránku `/_trust/`.

7. V centrální správě klikněte na webovou aplikaci a vyberte **zásady uživatele**. Přidejte uživatele s příslušnými oprávněními, jak je uvedeno dříve v tomto článku.

### <a name="fixing-people-picker"></a>Odstraňování uživatelů pro výběr

Uživatelé se teď můžou přihlašovat k SharePointu 2016 pomocí identit z Azure AD, ale stále existují příležitosti pro zlepšení uživatelského prostředí. Například vyhledávání uživatele v nástroji pro výběr osob představuje více výsledků hledání. Pro každý z těchto tří typů deklarací, které byly vytvořeny v mapování deklarací, existuje výsledek hledání. Chcete-li vybrat uživatele pomocí výběru osoby, je nutné zadat jeho uživatelské jméno přesně a zvolit výsledek deklarace identity.

![Výsledky hledání deklarací identity](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

U hledaných hodnot není žádné ověření, což může vést k nesprávnému pravopisu nebo uživatelům omylem zvolit špatný typ deklarace identity, jako je třeba **příjmení** . To může uživatelům zabránit v úspěšném přístupu k prostředkům.

Pro pomoc s tímto scénářem je open source řešení s názvem [AzureCP](https://yvand.github.io/AzureCP/) , které poskytuje vlastního zprostředkovatele deklarací identity pro SharePoint 2016. Pomocí grafu Azure AD zjistíte, co uživatelé zadávají a provedou ověřování. Další informace najdete na adrese [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Přiřaďte skupinu zabezpečení Azure AD v Azure Portal

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **místní SharePoint**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **místní SharePoint**.

    ![Odkaz na místní SharePoint v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na **Přidat uživatele**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. Vyhledejte skupinu zabezpečení, kterou chcete použít, a potom ji kliknutím na skupinu přidejte do oddílu Vybrat členy. Klikněte na **Vybrat**a pak na **přiřadit**.

    ![Hledat skupinu zabezpečení](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Ověřte oznámení v řádku nabídek, abyste byli informováni o úspěšném přiřazení skupiny do podnikové aplikace v Azure Portal.

### <a name="create-sharepoint-on-premises-test-user"></a>Vytvoření místního testovacího uživatele SharePointu

V této části vytvoříte uživatele s názvem Britta Simon v místním SharePointu. Pokud chcete přidat uživatele na místní platformu SharePoint, pracujte s [týmem podpory pro místní SharePoint](https://support.office.com/) . Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici místní služby SharePoint na přístupovém panelu byste měli být automaticky přihlášeni k místnímu serveru SharePoint, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
