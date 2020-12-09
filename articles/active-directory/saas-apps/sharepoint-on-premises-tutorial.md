---
title: 'Kurz: Azure Active Directory integrace se službou SharePoint v místním prostředí | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SharePointem v místním prostředí.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: a693b22c609829f3bf6e76637eac5793d73703e6
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862305"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí služby SharePoint v místním prostředí

V tomto kurzu se naučíte integrovat SharePoint místně pomocí Azure Active Directory (Azure AD). Při integraci služby SharePoint v místním prostředí se službou Azure AD můžete:

* Řízení přístupu k místnímu SharePointu v Azure AD.
* Umožněte uživatelům, aby se automaticky přihlásili k místnímu SharePointu pomocí svých účtů Azure AD.
* Spravujte své účty v Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s místním SharePointem, budete potřebovat tyto položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Farma SharePoint 2013 nebo novější.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD (SSO) v testovacím prostředí. Uživatelé z Azure AD mají přístup k místnímu SharePointu.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Vytváření podnikových aplikací v Azure Portal

Pokud chcete nakonfigurovat integraci služby SharePoint místně do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat místní SharePoint z galerie.

Postup přidání místního SharePointu z Galerie:

1. V podokně Azure Portal v levém podokně vyberte možnost **Azure Active Directory**.

   > [!NOTE]
   > Pokud prvek není k dispozici, můžete jej otevřít také pomocí odkazu **všechny služby** v horní části podokna vlevo. V následujícím přehledu se **Azure Active Directory** odkaz nachází v části **Identita** . Můžete ho také vyhledat pomocí pole Filtr.

1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

1. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace** .

1. Do vyhledávacího pole zadejte **SharePoint místně**. V podokně výsledků vyberte **místní SharePoint** .

    <kbd>![Místní SharePoint v seznamu výsledků](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Zadejte název místní instance SharePointu a vyberte **Přidat** pro přidání aplikace.

1. V nové podnikové aplikaci vyberte možnost **vlastnosti** a ověřte, zda je požadovaná hodnota **přiřazení uživatele?**.

   <kbd>![Je vyžadováno přiřazení uživatele? přepnut](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   V tomto scénáři je hodnota nastavena na **ne**.

## <a name="configure-and-test-azure-ad"></a>Konfigurace a testování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí služby SharePoint v místním prostředí. Jednotné přihlašování umožňuje vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SharePointu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby SharePoint v místním prostředí, dokončete tyto stavební bloky:

- [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
- [Nakonfigurujte místní SharePoint](#configure-sharepoint-on-premises) tak, aby na straně aplikace nakonfiguroval nastavení jednotného přihlašování.
- [Vytvořte v Azure Portal testovacího uživatele Azure AD](#create-an-azure-ad-test-user-in-the-azure-portal) a vytvořte nového uživatele ve službě Azure AD pro jednotné přihlašování.
- [Vytvořte v Azure Portal skupinu zabezpečení Azure AD](#create-an-azure-ad-security-group-in-the-azure-portal) , která vytvoří novou skupinu zabezpečení ve službě Azure AD pro jednotné přihlašování.
- [Udělení oprávnění k účtu Azure AD v SharePointu v místním počítači](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) pro udělení oprávnění uživateli Azure AD.
- [Udělení oprávnění skupině Azure AD v místní službě SharePoint](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) pro udělení oprávnění skupině Azure AD.
- [Udělte účtu Guest přístup k místnímu serveru SharePoint v Azure Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) a udělte mu oprávnění k účtu hosta ve službě Azure AD pro místní SharePoint.
- [Nakonfigurujte důvěryhodného zprostředkovatele identity pro více webových aplikací](#configure-the-trusted-identity-provider-for-multiple-web-applications) tak, aby používal stejného důvěryhodného poskytovatele identity pro více webových aplikací.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Konfigurace jednotného přihlašování Azure AD pomocí služby SharePoint v místním prostředí:

1. V Azure Portal vyberte **Azure Active Directory**  >  **podnikových aplikací**. Vyberte dříve vytvořený název podnikové aplikace a vyberte **jednotné přihlašování**.

1. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML** , aby bylo možné povolit jednotné přihlašování.
 
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu **Upravit** a otevřete tak základní dialogové okno **Konfigurace SAML** .

1. V části **základní konfigurace SAML** postupujte následovně:

    ![Informace o jednotném přihlašování k místní doméně a adresám URL služby SharePoint](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Do pole **identifikátor** zadejte adresu URL pomocí tohoto vzoru: `urn:<sharepointFarmName>:<federationName>` .

    1. Do pole **Adresa URL odpovědi** zadejte adresu URL pomocí tohoto vzoru: `https://<YourSharePointSiteURL>/_trust/` .

    1. Do pole **přihlašovací adresa URL** zadejte adresu URL pomocí tohoto vzoru: `https://<YourSharePointSiteURL>/` .
    1. Vyberte **Uložit**.

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru a adresy URL odpovědi.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. V části **nastavit místní SharePoint** zkopírujte příslušné adresy URL podle vašeho požadavku:
    
    - **Přihlašovací adresa URL**
    
        Zkopírujte adresu URL pro přihlášení a nahraďte **/Saml2** na konci pomocí **/wsfed** , aby vypadala jako https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed . (Tato adresa URL není přesná.)

    - **Identifikátor Azure AD**
    - **Odhlašovací adresa URL**

    > [!NOTE]
    > Tato adresa URL se nedá použít, protože je na SharePointu. **/Saml2** je nutné nahradit **/wsfed**. Místní aplikace SharePoint používá token SAML 1,1, takže Azure AD očekává od SharePointového serveru požadavek WS. Po ověření vydá token SAML 1,1.

### <a name="configure-sharepoint-on-premises"></a>Místní konfigurace SharePointu

1. Vytvoří nového důvěryhodného zprostředkovatele identity v SharePoint serveru 2016.

    Přihlaste se k serveru SharePoint a otevřete prostředí pro správu služby SharePoint. Vyplňte hodnoty:
    - **$Realm** je hodnota identifikátoru z části místní doména a adresy URL služby SharePoint v Azure Portal.
    - **$wsfedurl** je adresa URL služby jednotného přihlašování.
    - **$FilePath** je cesta k souboru, do které jste stáhli soubor certifikátu z Azure Portal.

    Spusťte následující příkazy, abyste nakonfigurovali nového důvěryhodného zprostředkovatele identity.

    > [!TIP]
    > Pokud s prostředím PowerShell ještě začínáte a chcete získat další informace o tom, jak prostředí PowerShell funguje, podívejte se na téma [SharePoint PowerShell](/powershell/sharepoint/overview).


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
1. Povolte důvěryhodného zprostředkovatele identity pro vaši aplikaci.

    1. V **centrální správě**, přejít na **Správa webové aplikace** a vyberte webovou aplikaci, kterou chcete zabezpečit pomocí Azure AD.

    1. Na pásu karet vyberte možnost **poskytovatelé ověřování** a zvolte zónu, kterou chcete použít.

    1. Vyberte **Důvěryhodný zprostředkovatel identity** a vyberte poskytovatele identifikace, kterého jste právě zaregistrovali s názvem *AzureAD*.

    1. Vyberte **OK**.

    ![Konfigurace poskytovatele ověřování](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Vytvoření testovacího uživatele Azure AD v Azure Portal

Cílem této části je vytvořit testovacího uživatele v Azure Portal.

1. V podokně Azure Portal v levém podokně vyberte možnost **Azure Active Directory**. V podokně **Správa** vyberte **Uživatelé**.

1. V horní části obrazovky vyberte **Všichni uživatelé**  >  **Nový uživatel** .

1. Vyberte **vytvořit uživatele** a ve vlastnostech uživatele postupujte podle těchto kroků. Můžete být schopni vytvářet uživatele v Azure AD pomocí vaší přípony tenanta nebo jakékoli ověřené domény. 

    1. Do pole **název** zadejte uživatelské jméno. Použili jsme k použití **testuser**.
  
    1. Do pole **Uživatelské jméno** zadejte `TestUser@yourcompanydomain.extension`. Tento příklad ukazuje `TestUser@contoso.com` .

       ![Uživatelský dialog](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

    1. Teď můžete web sdílet s TestUser@contoso.com a povolit tomuto uživateli přístup k němu.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Vytvoření skupiny zabezpečení Azure AD v Azure Portal

1. Vyberte **Azure Active Directory**  >  **skupiny**.

1. Vyberte možnost **Nová skupina**.

1. Vyplňte pole **typ skupiny**, **název skupiny**, **Popis skupiny** a **typ členství** . Vyberte šipky pro výběr členů a potom vyhledejte nebo vyberte členy, které chcete přidat do skupiny. Zvolte **Vybrat** pro přidání vybraných členů a pak vyberte **vytvořit**.

![Vytvoření skupiny zabezpečení Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Udělení oprávnění k účtu Azure AD ve službě SharePoint v místním prostředí

Pokud chcete udělit přístup k uživateli Azure AD v místní službě SharePoint, sdílejte kolekci webů nebo přidejte uživatele Azure AD k jedné ze skupin kolekce webů. Uživatelé se teď můžou ke službě SharePoint 201x přihlašovat pomocí identit z Azure AD, ale stále existují příležitosti pro zlepšení uživatelského prostředí. Například vyhledávání uživatele v nástroji pro výběr osob představuje více výsledků hledání. U každého typu deklarace identity, který je vytvořený v mapování deklarací, se vyskytuje výsledek hledání. Chcete-li vybrat uživatele pomocí výběru osob, je nutné zadat své uživatelské jméno přesně a zvolit **výsledek deklarace identity** .

![Výsledky hledání deklarací identity](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

U hledaných hodnot neexistuje žádné ověření, což může vést k nesprávnému pravopisu nebo uživatelům omylem vybrat špatný typ deklarace identity. Tato situace může uživatelům zabránit v úspěšném přístupu k prostředkům.

Pokud chcete tento scénář vyřešit pomocí výběru osob, open source řešení s názvem [AzureCP](https://yvand.github.io/AzureCP/) poskytuje vlastního zprostředkovatele deklarací identity pro SharePoint 2013, 2016 a 2019. Používá rozhraní Microsoft Graph API k vyřešení toho, co uživatelé zadávají a provádějí ověřování. Další informace najdete v tématu [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Bez AzureCP můžete přidat skupiny přidáním ID skupiny Azure AD, ale tato metoda není uživatelsky přívětivá a spolehlivá. Jak vypadá:
  > 
  >![Přidání skupiny Azure AD do skupiny služby SharePoint podle ID](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Udělení oprávnění skupině Azure AD ve službě SharePoint v místním prostředí

Chcete-li přiřadit skupiny zabezpečení Azure AD k místnímu serveru SharePoint, je nutné použít vlastního zprostředkovatele deklarací pro server SharePoint. V tomto příkladu se používá AzureCP.

 > [!NOTE]
 > AzureCP není produktem Microsoftu a nepodporuje ho podpora Microsoftu. Pokud chcete stáhnout, nainstalovat a nakonfigurovat AzureCP na místní farmě služby SharePoint, přečtěte si web [AzureCP](https://yvand.github.io/AzureCP/) . 

1. Nakonfigurujte AzureCP pro místní farmu služby SharePoint nebo alternativní řešení vlastního zprostředkovatele deklarací identity. Pokud chcete nakonfigurovat AzureCP, přečtěte si tento web [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html) .

1. V Azure Portal vyberte **Azure Active Directory**  >  **podnikových aplikací**. Vyberte dříve vytvořený název podnikové aplikace a vyberte **jednotné přihlašování**.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML upravte **atributy uživatele & deklarace identity** .

1. Vyberte **přidat deklaraci skupiny**.

1. Vyberte, které skupiny přidružené k uživateli by měly být vráceny v deklaraci identity. V takovém případě vyberte **všechny skupiny**. V části **zdrojový atribut** vyberte položku **ID skupiny** a vyberte **Uložit**.

Pokud chcete udělit přístup ke skupině zabezpečení Azure AD v místním SharePointu, sdílejte kolekci webů nebo přidejte skupinu zabezpečení Azure AD do jedné ze skupin kolekce webů.

1. Přejděte do **kolekce webů služby SharePoint**. V části **nastavení lokality** pro kolekci webů vyberte **osoby a skupiny**. 

1. Vyberte skupinu SharePoint a potom vyberte **Nový**  >  **Přidat uživatele do této skupiny**. Při zadávání názvu skupiny se pro výběr osob zobrazuje skupina zabezpečení Azure AD.

    ![Přidání skupiny Azure AD do skupiny služby SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Udělte přístup k účtu Guest k místnímu SharePointu v Azure Portal

Konzistentním způsobem můžete udělit přístup k webu služby SharePoint k účtu Guest, protože hlavní název uživatele teď bude změněn. Uživatel `jdoe@outlook.com` je například reprezentován jako `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` . Chcete-li sdílet web s externími uživateli, je třeba přidat některé změny do **atributů uživatele & v části deklarace identity** v Azure Portal.

1. V Azure Portal vyberte **Azure Active Directory**  >  **podnikových aplikací**. Vyberte dříve vytvořený název podnikové aplikace a vyberte **jednotné přihlašování**.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML upravte **atributy uživatele & deklarace identity** .

1. V **požadované zóně deklarací identity** vyberte **jedinečný identifikátor uživatele (ID názvu)**.

1. Změňte vlastnost **atributu source** na hodnotu **User. Localuserprincipalname** a vyberte **Save (Uložit**).

    ![Atributy uživatele & atribut deklarace identity počáteční zdroj](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Pomocí pásu karet se vraťte k **přihlašování založenému na SAML**. Nyní jsou **atributy uživatele & části deklarace identity** vypadat takto: 

    ![Atributy uživatele & deklarace identity jako konečný](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > V této instalaci není vyžadováno příjmení a křestní jméno.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory** a pak vyberte **Uživatelé**.

1. Vyberte možnost **Nový uživatel typu Host**.

1. Vyberte možnost **pozvat uživatele** . Vyplňte vlastnosti uživatele a vyberte **pozvat**.

1. Teď můžete web sdílet s MyGuestAccount@outlook.com a povolit tomuto uživateli přístup k němu.

    ![Sdílení lokality s účtem hosta](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurace důvěryhodného zprostředkovatele identity pro více webových aplikací

Konfigurace funguje pro jednu webovou aplikaci, ale pokud chcete použít stejného důvěryhodného poskytovatele identity pro více webových aplikací, je nutná další konfigurace. Předpokládejme například, že jste rozšířili webovou aplikaci, aby používala adresu URL `https://sales.contoso.com` a nyní chcete ověřovat uživatele `https://marketing.contoso.com` . Provedete to tak, že aktualizujete zprostředkovatele identity a splníte parametr WReply a aktualizujete registraci aplikace ve službě Azure AD, aby se přidala adresa URL odpovědi.

1. V Azure Portal vyberte **Azure Active Directory**  >  **podnikových aplikací**. Vyberte dříve vytvořený název podnikové aplikace a vyberte **jednotné přihlašování**.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML upravte **základní konfiguraci SAML**.

    ![Základní konfigurace SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. Pro **adresu URL odpovědi (adresa URL služby vyhodnocení zákazníka)** přidejte adresu URL pro další webové aplikace a vyberte **Uložit**.

    ![Úprava základní konfigurace SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. Na serveru SharePoint otevřete prostředí pro správu SharePoint 201x a spusťte následující příkazy. Použijte název vystavitele důvěryhodného tokenu identity, který jste použili dříve.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. V **centrální správě** přejdete do webové aplikace a povolíte stávajícího důvěryhodného zprostředkovatele identity.

Můžete mít další scénáře, ve kterých chcete pro interní uživatele poskytnout přístup k místní instanci služby SharePoint. V tomto scénáři je nutné nasadit Microsoft Azure Active Directory Connect, aby bylo možné synchronizovat místní uživatele s Azure AD. Tato instalace je popsána v jiném článku.

## <a name="next-steps"></a>Další kroky

Po nakonfigurování služby SharePoint v místním prostředí můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
