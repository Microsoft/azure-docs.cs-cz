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
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867109"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Kurz: Integrace jednotného přihlašování (Jednotné přihlašování) služby Azure Active Directory s místním SharePointem

V tomto kurzu se dozvíte, jak integrovat SharePoint místně s Azure Active Directory (Azure AD). Když integrujete SharePoint místně s Azure AD, můžete:

* Řízení ve službě Azure AD, který má přístup k SharePointu místně.
* Umožněte uživatelům, aby se automaticky přihlásili k SharePointu místně pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění – na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadované součásti

Pokud chcete nakonfigurovat integraci Azure Active Directory s místním SharePointem, potřebujete následující položky:

* Předplatné služby Azure Active Directory. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Farma SharePointu 2013 nebo novější.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování služby Azure Active Directory v testovacím prostředí. Uživatelé ze služby Azure Active Directory budou mít přístup k vašemu Sharepointu místně.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Vytvoření podnikových aplikací na webu Azure Portal

Pokud chcete nakonfigurovat integraci SharePointu místně do Azure AD, musíte přidat SharePoint místně z galerie do seznamu spravovaných aplikací SaaS.

Pokud chcete přidat SharePoint místně z galerie, proveďte následující kroky:

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

 > [!NOTE]
 > Pokud by prvek neměl být k dispozici, lze jej také otevřít prostřednictvím pevného propojení **Všechny služby** v horní části levého navigačního panelu. V následujícím přehledu je odkaz **služby Azure Active Directory** umístěn v části **Identita** nebo jej lze vyhledat pomocí textového pole filtru.

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

4. Do vyhledávacího pole zadejte **SharePoint místně**, z panelu výsledků vyberte **SharePoint místně.**

    <kbd>![SharePoint v místním prostředí v seznamu výsledků](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Zadejte název sharepointového onpremu a kliknutím na tlačítko **Přidat** přidejte aplikaci.

1. V nové podnikové aplikaci klikněte na Vlastnosti a zkontrolujte požadovanou hodnotu **přiřazení uživatele.**

   <kbd>![SharePoint v místním prostředí v seznamu výsledků](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

v našem scénáři je tato hodnota nastavena na **hodnotu Ne**.

## <a name="configure-and-test-azure-ad"></a>Konfigurace a testování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD s místním SharePointem.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v místním SharePointu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování služby Azure Active Directory s místním SharePointem, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
1. **[Konfigurace SharePointu místně](#configure-sharepoint-on-premises)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
1. **[Vytvořte uživatele testu Azure AD na webu Azure Portal](#create-an-azure-ad-test-user-in-the-azure-portal)** – vytvořte nového uživatele ve službě Azure AD pro jednotné přihlašování.
1. **[Vytvořte skupinu zabezpečení Azure AD na webu Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** – vytvořte novou skupinu zabezpečení ve službě Azure AD pro jednotné přihlašování.
1. **[Udělte oprávnění účtu Azure Active Directory v místním SharePointu](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** – udělte oprávnění uživateli Azure AD.
1. **[Udělte oprávnění ke skupině Azure AD v místním SharePointu](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** – udělte oprávnění ke skupině Azure AD.
1. **[Udělte přístup k účtu guest sharepointu místně na webu Azure Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** – udělte oprávnění k účtu Guest ve službě Azure AD pro SharePoint místně.
1. **[Konfigurace důvěryhodného zprostředkovatele identity pro více webových aplikací](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** – jak používat stejného důvěryhodného zprostředkovatele identity pro více webových aplikací

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s místním SharePointem, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)otevřete adresář Azure AD, klikněte na **podnikové aplikace**, klikněte na dříve vytvořený název **podnikové aplikace** a klikněte na **Jedno přihlášení**.

2. V **dialogovém okně Vybrat metodu jednotného přihlášení** povolte jednotné přihlašování kliknutím na režim **SAML.**
 
3. Na stránce **Nastavení jednotného přihlášení pomocí saml** otevřete klepnutím na ikonu **Upravit** dialogové okno **Základní konfigurace SAML.**

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![SharePoint místní informace o doméně a adresách URL jednotného přihlášení](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Do pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`urn:<sharepointFarmName>:<federationName>`

    1. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<YourSharePointSiteURL>/_trust/`

    1. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<YourSharePointSiteURL>/`
    1. klikněte na uložit.

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL, identifikátorem a adresou URL pro odpověď.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. V části **Nastavit SharePoint v místním** prostředí zkopírujte příslušnou adresu URL podle vašeho požadavku.
    
    1. **Přihlašovací adresa URL**
    
        Kopírovat přihlašovací URL nahradit **/saml2** na konci s **/wsfed**, to by vypadalo jako **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** (tato adresa URL není přesné)

    1. **Identifikátor azure reklamy**
    1. **Adresa URL odhlášení**
    > [!NOTE]
    > Tuto adresu URL nelze použít tak, jak je tomu v sharepointovém bodě: je nutné nahradit **/saml2** **/wsfed**. Místní aplikace Sharepointu používá token SAML 1.1, takže Azure AD očekává požadavek WS Fed ze sharepointového serveru a po ověření vydá SAML 1.1. Token.

### <a name="configure-sharepoint-on-premises"></a>Konfigurace SharePointu v místním prostředí

1. **Vytvoření nového důvěryhodného poskytovatele identity na SharePoint Serveru 2016**

    Přihlaste se na SharePoint server a otevřete SharePoint management Shell. Vyplňte hodnoty:
    1. **$realm** (Hodnota identifikátoru z části Místní domény a adresy URL SharePointu na webu Azure Portal).
    1. **$wsfedurl** (adresa URL služby jednotného přihlašování).
   1. **$filepath** (cesta k souboru, do kterého jste stáhli soubor certifikátu) z webu Azure Portal.

    Spusťte následující příkazy a nakonfigurujte nového důvěryhodného zprostředkovatele identity.

    > [!TIP]
    > Pokud s powershellem začínáte používat nebo se chcete dozvědět víc o tom, jak PowerShell funguje, přečtěte [si část SharePoint PowerShellu](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **Povolení důvěryhodného zprostředkovatele identity pro vaši aplikaci**

    a. V centrální správě přejděte na **Spravovat webovou aplikaci** a vyberte webovou aplikaci, kterou chcete zabezpečit pomocí Azure AD.

    b. Na pásu karet klikněte na **Zprostředkovatelé ověřování** a vyberte zónu, kterou chcete použít.

    c. Vyberte **zprostředkovatele důvěryhodné identity** a vyberte identifikovat zprostředkovatele, který jste právě zaregistrovali s názvem *AzureAD*.

    d. Klikněte na tlačítko **OK**.

    ![Konfigurace zprostředkovatele ověřování](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Vytvoření testovacího uživatele Azure AD na webu Azure Portal

Cílem této části je vytvořit testovacího uživatele na webu Azure Portal.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**v části **Správa** posouvání vyberte **Uživatelé**.

2. V horní části obrazovky vyberte **Možnost Všichni uživatelé** následovaní **novým uživatelem.**

3. Vyberte možnost **Vytvořit uživatele** a ve vlastnostech Uživatele proveďte následující kroky.  
   Můžete být schopni vytvořit uživatele ve vašem Azure AD pomocí přípony klienta nebo jakékoli ověřené domény. 

    a. Do pole **Název** zadejte uživatelské jméno , použili jsme **TestUser**.
  
    b. V poli **Uživatelské jméno** typ pole`TestUser@yourcompanydomain.extension`  
    Například TestUser@contoso.com.

    ![Dialogové okno Uživatel](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

    e. Nyní můžete sdílet web TestUser@contoso.com s a povolit tomuto uživateli přístup k němu.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Vytvoření skupiny zabezpečení Azure AD na webu Azure Portal

1. Klikněte na **položku > skupin služby Azure Active Directory**.

2. Klikněte na **Nová skupina**:

3. Vyplňte **typ skupiny**, **název skupiny**, **popis skupiny**, **typ členství**. Klikněte na šipku pro výběr členů, pak hledat nebo klikněte na člena, který chcete přidat do skupiny. Kliknutím na **Vyberte,** chcete-li přidat vybrané členy, klikněte na **Vytvořit**.

![Vytvoření skupiny zabezpečení Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Udělení oprávnění účtu Azure Active Directory v místním SharePointu

Chcete-li udělit přístup uživateli Služby Azure Active Directory v místním SharePointu, musíte sdílet kolekci webů nebo přidat uživatele Služby Azure Active Directory do jedné ze skupin y kolekce webů. Uživatelé se teď můžou přihlásit ke SharePointu 201x pomocí identit z Azure AD, ale pořád existují příležitosti ke zlepšení uživatelského prostředí. Například hledání uživatele představuje více výsledků hledání v výběru osob. Existuje výsledek hledání pro každý z typů deklarací, které jsou vytvořeny v mapování deklarací. Chcete-li vybrat uživatele pomocí nástroje pro výběr osob, musíte přesně zadat jeho uživatelské jméno a zvolit výsledek deklarace **názvu.**

![Nároky výsledky hledání](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

U hledaných hodnot není žádné ověření, což může vést k překlepům nebo k náhodnému výběru nesprávného typu deklarace. To může zabránit uživatelům v úspěšném přístupu k prostředkům.

**Chcete-li opravit výběr osob** pomocí tohoto scénáře, existuje open source řešení s názvem [AzureCP,](https://yvand.github.io/AzureCP/) který poskytuje vlastní zprostředkovatele deklarací identity pro SharePoint 2013, 2016 a 2019. Pomocí rozhraní Microsoft Graph API vyřešíte, co uživatelé zadají a provedou ověření. Další informace najdete na [webu AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Bez AzureCP můžete přidat skupiny přidáním ID skupiny Azure AD, ale to není uživatelsky přívětivé a spolehlivé. Tam je, jak to vypadá:  
  >   
  >![Přidání skupiny Azure AD do sharepointové skupiny](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Udělení oprávnění skupině Azure AD v místním SharePointu

Chcete-li přiřadit skupiny zabezpečení Služby Azure Active Directory k sharepointovému webu, bude nutné použít vlastního poskytovatele deklarací identity pro SharePoint Server. V našem příkladu jsme použili AzureCP.

 > [!NOTE]
 > Upozorňujeme, že AzureCP není produkt společnosti Microsoft ani ho nepodporuje technická podpora společnosti Microsoft. Stáhněte, nainstalujte a nakonfigurujte AzureCP v místní sharepointové farmě za . https://yvand.github.io/AzureCP/ 

1. Nakonfigurujte AzureCP na místní farmě SharePointu nebo v alternativním řešení vlastního poskytovatele deklarací identity. kroky konfigurace AzureCP jsou k dispozici na adresehttps://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. Na webu Azure Portal otevřete adresář Azure AD. Klikněte na **Podnikové aplikace**, klikněte na dříve vytvořený název **podnikové aplikace** a klikněte na **Jedno přihlášení**.

1. Na stránce **Nastavit jednotné přihlašování se skupinou SAM**upravte oddíl **Atributy uživatele & deklarace identity.**

1. Klikněte na **Přidat skupinovou deklaraci**.

1. Vyberte skupiny přidružené k uživateli, které mají být vráceny v deklaraci, v našem případě vyberte **Všechny skupiny** a v části Atribut zdroj vyberte **ID skupiny** a klikněte na **Uložit**.

Chcete-li udělit přístup ke skupině zabezpečení Služby Azure Active Directory v místním SharePointu, musíte sdílet kolekci webů nebo přidat skupinu zabezpečení Azure Active Directory do jedné ze skupin kolekce webů.

1. Přejděte do kolekce webů služby SharePoint v části Nastavení webu pro kolekci webů klikněte na "Lidé a skupiny". Vyberte skupinu SharePoint, klikněte na Nový, "Přidat uživatele do této skupiny" a začněte zadávat název skupiny, ve které bude výběr osob zobrazovat skupinu zabezpečení Služby Azure Active Directory.

    ![Přidání skupiny Azure AD do sharepointové skupiny](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Udělení přístupu k účtu Guest místnímu SharePointu na webu Azure Portal

nyní je možné udělit přístup k vašemu sharepointovému webu účtu guest konzistentním způsobem. Stává se, že hlavní upn získá změněn. Uživatel jdoe@outlook.com s budou `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`reprezentovány jako . Je možné získat bezproblémové prostředí při sdílení webu s externími uživateli, je nutné přidat některé změny v uživatelské **atributy & deklarace identity** na webu Azure Portal.

1. Na webu Azure Portal otevřete adresář Azure AD. Klikněte na **Podnikové aplikace**, klikněte na dříve vytvořený název **podnikové aplikace** a klikněte na **Jedno přihlášení**.

1. Na stránce **Nastavit jednotné přihlašování se skupinou SAM**upravte oddíl **Atributy uživatele & deklarace identity.**

1. v zóně **Požadovaná deklarace** klikněte na **jedinečný identifikátor uživatele (ID jména).**

1. Změňte **vlastnost atribut zdroje** na hodnotu **user.localuserprincipalname** a **uložte**.

    ![Atributy uživatele & počáteční deklarace identity](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Pomocí pásu karet se nyní vraťte zpět **na přihlašování založené na saml,** část **Atributy uživatele & deklarace identity** bude vypadat takto : 

    ![Atributy uživatele & deklarace identity konečné](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > V tomto nastavení není vyžadováno příjmení a křestní jméno.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**.

1. klikněte na **nový uživatel typu Host**

1. Vyberte možnost **Pozvat uživatele** a vyplňte vlastnosti uživatele a klikněte na **Pozvat**.

1. Nyní můžete sdílet web MyGuestAccount@outlook.com s a povolit tomuto uživateli přístup k němu.

    ![Web sdílení s účtem hosta](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurace důvěryhodného zprostředkovatele identity pro více webových aplikací

Konfigurace funguje pro jednu webovou aplikaci, ale potřebuje další konfiguraci, pokud máte v úmyslu použít stejného důvěryhodného zprostředkovatele identity pro více webových aplikací. Předpokládejme například, že jsme rozšířili `https://sales.contoso.com` webovou aplikaci tak, `https://marketing.contoso.com` aby používala adresu URL, a nyní chcete ověřit i uživatele. Chcete-li to provést, musíme aktualizovat zprostředkovatele identity, aby byl respektovat parametr WReply a aktualizovat registraci aplikace ve službě Azure AD a přidat adresu URL odpovědi.

1. Na webu Azure Portal otevřete adresář Azure AD. Klikněte na **Podnikové aplikace**, klikněte na dříve vytvořený název **podnikové aplikace** a klikněte na **Jedno přihlášení**.

2. Na stránce **Nastavit jednotné přihlašování se službou SAM**upravte základní **konfiguraci SAML**.

    ![upravit základní konfiguraci SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. V **adrese URL odpovědi (Adresa URL služby Assertion Consumer Service)** přidejte adresu URL dalších webových aplikací a klepněte na tlačítko **Uložit**.

    ![upravit základní konfiguraci SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Na sharepointovém serveru otevřete **prostředí SharePoint 201x Management Shell** a spusťte následující příkazy s použitím názvu vydavatele důvěryhodného tokenu identity, který jste použili dříve.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. V centrální správě přejděte do webové aplikace a povolte stávajícího důvěryhodného zprostředkovatele identity.

Můžete mít jiný scénář, kde chcete udělit přístup k místní sharepoint pro interní uživatele, pro tento scénář budete muset nasadit Microsoft Azure Active Directory Connect, který umožní synchronizovat místní uživatele s Azure Active Directory, toto nastavení by bylo součástí jiného článku. 

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co je hybridní identita ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
