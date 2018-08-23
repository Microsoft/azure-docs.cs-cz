---
title: 'Kurz: Integrace Azure Active Directory s místní SharePoint | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a místní SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: jeedes
ms.openlocfilehash: cd1e4b376b634a3e3c7fa2c87723aff05f431a25
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42060590"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Kurz: Integrace Azure Active Directory s místní SharePoint

V tomto kurzu se dozvíte, jak integrovat místní SharePoint se službou Azure Active Directory (Azure AD).

Integrace místní SharePoint s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k místní SharePoint.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k místní SharePoint (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s místní SharePoint, potřebujete následující položky:

- Předplatné Azure AD
- SharePoint v místním jednotné přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání místní SharePoint z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Přidání místní SharePoint z Galerie
Pokud chcete nakonfigurovat integraci služby SharePoint v místním do služby Azure AD, budete muset přidat místní SharePoint z Galerie na váš seznam spravovaných aplikací SaaS.

**Pokud chcete přidat místní SharePoint z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **místní SharePoint**vyberte **místní SharePoint** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Místní SharePoint v seznamu výsledků](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí služby SharePoint v místním podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v místní SharePoint je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě SharePoint místní musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s místní SharePoint, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Udělit přístup pro SharePoint v místním testovacího uživatele](#grant-access-to-sharePoint-on-premises-test-user)**  – Pokud chcete mít protějšek Britta Simon v místní SharePoint, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a nakonfigurovat jednotné přihlašování v místní aplikace SharePoint.

**Ke konfiguraci Azure AD jednotné přihlašování s místní SharePoint, proveďte následující kroky:**

1. Na webu Azure Portal na **místní SharePoint** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. Na **SharePoint místní domény a adresy URL** části, proveďte následující kroky:

    ![Místní SharePoint domény a adresy URL jednotné přihlašování – informace](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. V **identifikátor** textového pole zadejte adresu URL: `urn:sharepoint:federation`

4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

    > [!Note]
    > Mějte prosím na paměti dolů cesta k souboru, do které jste stáhli soubor certifikátu je potřeba pro použití v pozdější části skriptu prostředí PowerShell pro konfiguraci.

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media\sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. Na **místní SharePoint konfigurace** klikněte na tlačítko **konfigurovat službu SharePoint v místním** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID** z **Stručná referenční příručka oddílu.** Pro **jednotné přihlašování – adresa URL služby**, použijte hodnotu následujícímu vzoru: `https://login.microsoftonline.com/_my_directory_id_/wsfed` 

    > [!Note]
    > _my_directory_id_ je id tenanta Azure Ad předplatné.

    ![Konfigurace místní služby SharePoint](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > Aplikace služby SharePoint v místním používá SAML 1.1 tokenu, aby Azure AD očekává žádost WS Fed ze serveru SharePoint server a po ověření, vystavuje SAML 1.1. token.

7. V okně jiné webové prohlížeče Přihlaste se k webu služby SharePoint v místním společnosti jako správce.

8. **Konfigurace nového důvěryhodného zprostředkovatele identity v SharePoint serveru 2016**

    Přihlásit k serveru SharePoint Server 2016 a otevřete prostředí pro správu služby SharePoint 2016. Zadejte hodnoty $realm (hodnota identifikátoru SharePoint místní domény a adresy URL v části webu Azure portal), $wsfedurl (jednotné přihlašování – adresa URL služby) a $filepath (cesta k souboru do které jste stáhli soubor certifikátu) z webu Azure portal a spuštění Následující příkazy, které nakonfigurují nový důvěryhodného zprostředkovatele identity.

    > [!TIP]
    > Pokud pro vás nová pomocí Powershellu nebo chcete další informace o tom, jak funguje Powershellu, přečtěte si téma [SharePoint Powershellu](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

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
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    V dalším kroku použijte následující postup povolení důvěryhodného zprostředkovatele identity pro vaši aplikaci:

    a. V Centru pro správu, přejděte na **webovou aplikaci spravovat** a vyberte webovou aplikaci, kterou chcete zabezpečit pomocí služby Azure AD.

    b. Na pásu karet klikněte na tlačítko **zprostředkovatelé ověřování** a vyberte zónu, kterou chcete použít.

    c. Vyberte **zprostředkovatele Identity pro důvěryhodného** a vyberte identifikovat zprostředkovatele jste právě zaregistrovali, s názvem *AzureAD*.

    d. V nastavení adresy URL přihlašovací stránky, vyberte **vlastní přihlašovací stránku** a zadejte hodnotu "/_trust/".

    e. Klikněte na **OK**.

    ![Konfigurace poskytovatele ověřování](./media\sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Někteří uživatelé externí není schopna použít tuto integraci jednotné přihlašování, protože jejich hlavní název uživatele bude mít pozměnění něco jako hodnota `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Brzy budeme zákazníkům umožní konfigurace aplikace o tom, jak zpracovat hlavní název uživatele v závislosti na typu uživatele. Po, který by měl být všichni uživatelé typu Host moct používat jednotné přihlašování bez problémů jako zaměstnance organizace.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media\sharepoint-on-premises-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media\sharepoint-on-premises-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media\sharepoint-on-premises-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media\sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Udělení přístupu k SharePoint v místním testovacího uživatele

Uživatelé, kteří budou protokolovat do služby Azure AD a přístup k Sharepointu musí mít udělen přístup k aplikaci pomocí následujícího postupu k nastavení oprávnění pro přístup k webové aplikaci.

1. V Centru pro správu, klikněte na tlačítko **správy aplikací**.

2. Na **správy aplikací** stránku, **webových aplikací** klikněte na tlačítko **spravovat webové aplikace**.

3. Klikněte na příslušnou webovou aplikaci a pak klikněte na tlačítko **zásady uživatele**.

4. V zásadách pro webovou aplikaci, klikněte na tlačítko **Add Users**.

    ![Hledání pro uživatele podle své deklarace názvu](./media\sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. V **Add Users** dialogového okna klikněte na příslušnou zónu v **zóny**a potom klikněte na tlačítko **Další**.

6. V **zásady pro webovou aplikaci** v dialogu **výběr uživatelů** klikněte na tlačítko **Procházet** ikonu.

7. V **najít** textové pole, typ **uživatel instančního objektu name(UPN)** hodnota, pro který jste nakonfigurovali místní aplikace SharePoint ve službě Azure AD a klikněte na tlačítko **hledání**. </br>Příklad: *brittasimon@contoso.com*.

8. V části Azure AD v zobrazení seznamu, vyberte název vlastnosti a klikněte na tlačítko **přidat** klikněte **OK** zavřete dialogové okno.

9. V části oprávnění **úplné řízení**.

    ![Udělení úplného řízení uživateli deklarací identity](./media\sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

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

![Výsledky hledání deklarací identity](./media\sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Neexistuje žádné ověřování na tyto hodnoty vyhledat, což může vést k překlepy nebo typ přiřazení jako deklarace identity uživatele nechtěně výběru chybného **příjmení** deklarací identity. To může zabránit uživatelům v úspěšně přístup k prostředkům.

Abychom v tomto scénáři je open source řešení [AzureCP](https://yvand.github.io/AzureCP/) zprostředkovatele vlastní deklarace identity, která poskytuje pro SharePoint 2016. Azure AD Graph se bude používat pro vyřešení co uživatelé zadat a provést ověření. Další informace najdete na [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k místní SharePoint.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit místní SharePoint, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **místní SharePoint**.

    ![Propojení služby SharePoint v seznamu aplikací](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SharePoint v místním na přístupovém panelu, měli byste obdržet automaticky přihlášeného k místní aplikace SharePoint.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Používání služby Azure AD pro ověřování serveru SharePoint](https://docs.microsoft.com/en-us/office365/enterprise/using-azure-ad-for-sharepoint-server-authentication)

<!--Image references-->

[1]: ./media\sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media\sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media\sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media\sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media\sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media\sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media\sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media\sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media\sharepoint-on-premises-tutorial/tutorial_general_203.png

