---
title: 'Kurz: Azure Active Directory integrace se službou SharePoint v místním prostředí | Microsoft Docs'
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
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81867109"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k místnímu SharePointu

V tomto kurzu se dozvíte, jak integrovat SharePoint místně pomocí Azure Active Directory (Azure AD). Při integraci služby SharePoint v místním prostředí se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k místnímu webu SharePoint.
* Umožněte uživatelům, aby se automaticky přihlásili k místnímu SharePointu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure Active Directory s místním SharePointem, potřebujete následující položky:

* Azure Active Directory předplatné. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Farma SharePoint 2013 nebo novější.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete Azure Active Directory jednotné přihlašování v testovacím prostředí. Uživatelé z Azure Active Directory by měli mít přístup k místnímu SharePointu.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Vytváření podnikových aplikací v Azure Portal

Pokud chcete nakonfigurovat integraci služby SharePoint místně do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat místní SharePoint z galerie.

Pokud chcete z Galerie přidat místní SharePoint, proveďte následující kroky:

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

 > [!NOTE]
 > Pokud by element neměl být k dispozici, lze jej také otevřít prostřednictvím odkazu pevný **všechny služby** v horní části levého navigačního panelu. V následujícím přehledu se **Azure Active Directory** odkaz nachází v části **Identita** nebo ho můžete vyhledat pomocí textového pole filtrovat.

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

4. Do vyhledávacího pole zadejte místní **SharePoint**a z panelu výsledek vyberte **místní SharePoint** .

    <kbd>![Místní SharePoint v seznamu výsledků](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Zadejte název pro SharePoint OnPrem a kliknutím na tlačítko **Přidat** přidejte aplikaci.

1. V nové podnikové aplikaci klikněte na vlastnosti a ověřte, jestli je **požadované přiřazení uživatele** .

   <kbd>![Místní SharePoint v seznamu výsledků](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

v našem scénáři je tato hodnota nastavená na **ne**.

## <a name="configure-and-test-azure-ad"></a>Konfigurace a testování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí služby SharePoint v místním prostředí.
Aby se jednotné přihlašování fungovalo, je potřeba vytvořit odkazový vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v SharePointu.

Chcete-li nakonfigurovat a otestovat Azure Active Directory jednotné přihlašování pomocí služby SharePoint v místním prostředí, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
1. **[Nakonfigurujte místní SharePoint](#configure-sharepoint-on-premises)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořte testovacího uživatele Azure AD v části Azure Portal](#create-an-azure-ad-test-user-in-the-azure-portal)** – vytvořit nového uživatele ve službě Azure AD pro jednotné přihlašování.
1. **[Vytvořte skupinu zabezpečení Azure AD ve službě Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** – vytvořit novou skupinu zabezpečení ve službě Azure AD pro jednotné přihlašování.
1. **[Udělte oprávnění Azure Active Directory účtu v SharePointu místně](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** – udělte oprávnění uživateli Azure AD.
1. **[Udělení oprávnění skupině Azure AD v místní nabídce SharePoint](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** – udělte oprávnění skupině Azure AD.
1. **[Udělte účtu Guest přístup k místnímu serveru SharePoint v Azure Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** – udělte oprávnění účtu Guest ve službě Azure AD pro místní SharePoint.
1. **[Konfigurace důvěryhodného zprostředkovatele identity pro více webových aplikací](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** – způsob použití stejného důvěryhodného zprostředkovatele identity pro více webových aplikací

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí místní služby SharePoint, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)otevřete adresář služby Azure AD, klikněte na **podnikové aplikace**, klikněte na **dříve vytvořený název podnikové aplikace** a klikněte na **jednotné přihlašování**.

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** klikněte na režim **SAML** a povolte jednotné přihlašování.
 
3. Na stránce **nastavit jednotné přihlašování se** stránkou SAML kliknutím na ikonu **Upravit** otevřete základní dialogové okno **Konfigurace SAML** .

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v místní doméně a adresách URL služby SharePoint](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`urn:<sharepointFarmName>:<federationName>`

    1. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://<YourSharePointSiteURL>/_trust/`

    1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<YourSharePointSiteURL>/`
    1. klikněte na Uložit.

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru a adresy URL odpovědi.

5. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. V části **nastavit místní SharePoint** zkopírujte příslušné adresy URL podle vašich požadavků.
    
    1. **Přihlašovací adresa URL**
    
        Kopírovat přihlašovací adresu URL: nahradit **/Saml2** na konci pomocí **/wsfed**, by vypadala **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** takto: (Tato adresa URL není přesná)

    1. **Identifikátor Azure AD**
    1. **Odhlašovací adresa URL**
    > [!NOTE]
    > Tato adresa URL se nedá použít, protože je na SharePointu: musíte nahradit **/Saml2** pomocí **/wsfed**. Místní aplikace SharePoint používá token SAML 1,1, takže služba Azure AD očekává požadavek WS dodaný ze SharePoint serveru a po ověření vydá SAML 1,1. klíčové.

### <a name="configure-sharepoint-on-premises"></a>Místní konfigurace SharePointu

1. **Vytvoření nového důvěryhodného zprostředkovatele identity v SharePoint serveru 2016**

    Přihlaste se k serveru SharePoint a otevřete prostředí pro správu služby SharePoint. Vyplňte hodnoty:
    1. **$Realm** (hodnota identifikátoru z části místní doména a adresy URL služby SharePoint v Azure Portal).
    1. **$wsfedurl** (adresa URL služby jednotného přihlašování)
   1. **$FilePath** (cesta k souboru, do které jste stáhli soubor certifikátu) z Azure Portal.

    Spusťte následující příkazy, abyste nakonfigurovali nového důvěryhodného zprostředkovatele identity.

    > [!TIP]
    > Pokud s prostředím PowerShell ještě začínáte a chcete získat další informace o tom, jak prostředí PowerShell funguje, podívejte se na téma [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


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

    a. V centrální správě přejděte na **Správa webové aplikace** a vyberte webovou aplikaci, kterou chcete zabezpečit pomocí Azure AD.

    b. Na pásu karet klikněte na **Zprostředkovatelé ověřování** a vyberte zónu, kterou chcete použít.

    c. Vyberte **Důvěryhodný zprostředkovatel identity** a vyberte poskytovatele identifikace, kterého jste právě zaregistrovali s názvem *AzureAD*.

    d. Klikněte na tlačítko **OK**.

    ![Konfigurace poskytovatele ověřování](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Vytvoření testovacího uživatele Azure AD v Azure Portal

Cílem této části je vytvořit testovacího uživatele v Azure Portal.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory**a v části **Spravovat** posouvání vyberte **uživatele**.

2. Pak v horní části obrazovky vyberte **Všichni uživatelé** , za kterými následuje **Nový uživatel** .

3. Vyberte možnost **vytvořit uživatele** a ve vlastnostech uživatele proveďte následující kroky.  
   Můžete být schopni vytvářet uživatele v Azure AD pomocí vaší přípony tenanta nebo jakékoli ověřené domény. 

    a. Do pole **název** zadejte uživatelské jméno, používáme příkaz **testuser**.
  
    b. Do pole typ **uživatelského jména**`TestUser@yourcompanydomain.extension`  
    Například TestUser@contoso.com.

    ![Uživatelský dialog](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

    e. Teď můžete web sdílet s TestUser@contoso.com a povolit tomuto uživateli přístup k němu.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Vytvoření skupiny zabezpečení Azure AD v Azure Portal

1. Klikněte na **Azure Active Directory > skupiny**.

2. Klikněte na **Nová skupina**:

3. Vyplňte pole **typ skupiny**, **název skupiny**, **Popis skupiny**, **typ členství**. Klikněte na šipku a vyberte členy, vyhledejte nebo klikněte na člen, který chcete přidat do skupiny. Kliknutím na **Vybrat** přidejte vybrané členy a pak klikněte na **vytvořit**.

![Vytvoření skupiny zabezpečení Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Udělení oprávnění Azure Active Directory účtu v SharePointu v místním prostředí

Chcete-li udělit přístup k Azure Active Directory uživateli v místní službě SharePoint, je nutné sdílet kolekci webů nebo přidat Azure Active Directory uživatele do jedné ze skupin kolekce webů. Uživatelé se teď můžou přihlašovat ke službě SharePoint 201x pomocí identit z Azure AD, ale stále existují příležitosti pro zlepšení uživatelského prostředí. Například vyhledávání uživatele v nástroji pro výběr osob představuje více výsledků hledání. U každého typu deklarace identity, který je vytvořený v mapování deklarací, se vyskytuje výsledek hledání. Chcete-li vybrat uživatele pomocí výběru osoby, je nutné zadat jeho uživatelské jméno přesně a zvolit výsledek **deklarace identity** .

![Výsledky hledání deklarací identity](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Pro hodnoty, které hledáte, neexistuje žádné ověření, což může vést k chybám nebo uživatelům omylem zvolit špatný typ deklarace identity. To může uživatelům zabránit v úspěšném přístupu k prostředkům.

**K opravě výběru osob** v tomto scénáři existuje open source řešení s názvem [AzureCP](https://yvand.github.io/AzureCP/) , které poskytuje vlastního zprostředkovatele deklarací identity pro SharePoint 2013, 2016 a 2019. Použije rozhraní Microsoft Graph API k vyřešení toho, co uživatelé zadávají a provádějí ověřování. Další informace najdete na adrese [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Bez AzureCP můžete přidat skupiny tím, že přidáte ID skupiny Azure AD, ale toto není uživatelsky přívětivé a spolehlivé. Vypadá to, jak vypadá:  
  >   
  >![Přidat skupinu Azure AD do skupiny služby SharePoint](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Udělení oprávnění skupině Azure AD v místní službě SharePoint

Aby bylo možné přiřadit skupiny zabezpečení Azure Active Directory k místnímu serveru SharePoint, bude nutné použít vlastního zprostředkovatele deklarací pro server SharePoint. V našem příkladu jsme použili AzureCP.

 > [!NOTE]
 > Upozorňujeme, že AzureCP není produktem společnosti Microsoft ani není podporován technickou podporou společnosti Microsoft. Stáhněte, nainstalujte a nakonfigurujte AzureCP na místní SharePointové farmě na https://yvand.github.io/AzureCP/. 

1. Nakonfigurujte AzureCP pro místní farmu služby SharePoint nebo alternativní řešení vlastního zprostředkovatele deklarací identity. Postup konfigurace AzureCP je k dispozici na adresehttps://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. V Azure Portal otevřete adresář služby Azure AD. Klikněte na **podnikové aplikace**, klikněte na **dříve vytvořený název podnikové aplikace** a klikněte na **jednotné přihlašování**.

1. Na stránce **nastavte jednotné přihlašování pomocí Sam**, upravte **atributy uživatele & deklarace identity** .

1. Klikněte na **přidat deklaraci skupiny**.

1. Vyberte, které skupiny přidružené k uživateli by měly být vráceny v deklaraci, v našem případě vyberte **všechny skupiny** a v části zdrojový atribut vyberte **ID skupiny** a klikněte na **Uložit**.

Chcete-li udělit přístup ke skupině zabezpečení Azure Active Directory v místním SharePointu, je třeba sdílet kolekci webů nebo přidat Azure Active Directory skupinu zabezpečení do jedné ze skupin kolekce webů.

1. Přejděte do kolekce webů služby SharePoint, v části nastavení lokality pro kolekci webů klikněte na možnost lidé a skupiny. Vyberte skupinu SharePoint a pak klikněte na nová, do přidat uživatele do této skupiny a začněte zadávat název skupiny. Výběr uživatelů zobrazí Azure Active Directory skupinu zabezpečení.

    ![Přidat skupinu Azure AD do skupiny služby SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Udělte přístup k účtu Guest k místnímu SharePointu v Azure Portal

je teď možné, že budete mít jednotný způsob, jak udělit přístup k webu služby SharePoint k účtu Guest. Dojde k úpravě hlavního názvu uživatele (UPN). Uživatel s jdoe@outlook.com bude reprezentován jako `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Je možné, že budete mít bezproblémové prostředí při sdílení webu s externími uživateli. v Azure Portal je potřeba přidat do atributů uživatele některé změny **& deklarace identity** .

1. V Azure Portal otevřete adresář služby Azure AD. Klikněte na **podnikové aplikace**, klikněte na **dříve vytvořený název podnikové aplikace** a klikněte na **jednotné přihlašování**.

1. Na stránce **nastavte jednotné přihlašování pomocí Sam**, upravte **atributy uživatele & deklarace identity** .

1. v **požadované zóně deklarací** klikněte na **jedinečný identifikátor uživatele (ID názvu)**.

1. Změňte vlastnost **zdrojového atributu** na hodnotu **User. localuserprincipalname** a Save ( **Uložit**).

    ![Atributy uživatele & deklarace identity jako počáteční](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Použití pásu karet se vrátí zpět k **přihlašování založené na SAML** . **atributy uživatele & části deklarace identity** teď budou vypadat takto: 

    ![Atributy uživatele & deklarace identity jako konečný](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > V tomto nastavení nejsou vyžadovány příjmení a křestní jméno.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**.

1. klikněte na **Nový uživatel typu Host** .

1. Vyberte možnost **pozvat uživatele** a vyplňte vlastnosti uživatele a klikněte na **pozvat**.

1. Teď můžete web sdílet s MyGuestAccount@outlook.com a povolit tomuto uživateli přístup k němu.

    ![Web pro sdílení s účtem hosta](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurace důvěryhodného zprostředkovatele identity pro více webových aplikací

Konfigurace funguje pro jednu webovou aplikaci, ale vyžaduje další konfiguraci, pokud máte v úmyslu použít stejného důvěryhodného poskytovatele identity pro více webových aplikací. Předpokládejme například, že jsme rozšířili webovou aplikaci, aby používala adresu `https://sales.contoso.com` URL, a teď chcete ověřovat i uživatele `https://marketing.contoso.com` . Abychom to mohli udělat, musíme aktualizovat poskytovatele identity, aby se zavedl parametr WReply, a aktualizovat registraci aplikace ve službě Azure AD, aby se přidala adresa URL odpovědi.

1. V Azure Portal otevřete adresář služby Azure AD. Klikněte na **podnikové aplikace**, klikněte na **dříve vytvořený název podnikové aplikace** a klikněte na **jednotné přihlašování**.

2. Na stránce **nastavte jednotné přihlašování pomocí Sam**a upravte **základní konfiguraci SAML**.

    ![upravit základní konfiguraci SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. Do pole **Adresa URL odpovědi (adresa URL služby vyhodnocení zákazníka)** přidejte adresu URL pro další webové aplikace a klikněte na **Uložit**.

    ![upravit základní konfiguraci SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Na serveru SharePoint otevřete **prostředí pro správu SharePoint 201x** a spusťte následující příkazy pomocí názvu vystavitele důvěryhodného tokenu identity, který jste použili dříve.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. V centrální správě přejdete do webové aplikace a povolíte stávajícího důvěryhodného zprostředkovatele identity.

Můžete mít další situaci, kdy chcete pro interní uživatele poskytnout přístup k místnímu SharePointu. pro tento scénář byste museli nasadit Microsoft Azure Active Directory Connect, které umožní synchronizaci místních uživatelů s Azure Active Directory. Tato instalace by byla součástí jiného článku. 

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co je hybridní identita s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
