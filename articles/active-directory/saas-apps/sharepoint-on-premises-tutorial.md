---
title: 'Kurz: Azure Active Directory integraci s místní SharePoint | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a místní SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 8cae1e2670ba449c0db28209c54b740e927bbb73
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210373"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Kurz: Azure Active Directory integraci s místní SharePoint

V tomto kurzu zjistěte, jak integrovat místní SharePoint se službou Azure Active Directory (Azure AD).

Integrace místní SharePoint se službou Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k místní SharePoint.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k místní SharePoint (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s místní SharePoint, potřebujete následující položky:

- Předplatné služby Azure AD
- Místní SharePoint jednoho přihlášení povoleno předplatného

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání místní SharePoint z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Přidání místní SharePoint z Galerie
Chcete-li nakonfigurovat integraci místní SharePoint do služby Azure AD, přidejte místní SharePoint z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat místní SharePoint z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **místní SharePoint**, vyberte **místní SharePoint** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Místní SharePoint v seznamu výsledků](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s místní SharePoint podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v místní SharePoint je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské ve službě SharePoint místní musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s místní SharePoint, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele místní služby SharePoint](#create-a-sharePoint-on-premises-test-user)**  – Pokud chcete mít protějšek z Britta Simon v místní SharePoint propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci místní služby SharePoint.

**Ke konfiguraci Azure AD jednotné přihlašování s místní SharePoint, proveďte následující kroky:**

1. Na portálu Azure na **místní SharePoint** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. Na **SharePoint místní domény a adresy URL** část, proveďte následující kroky:

    ![SharePoint místní domény a adresy URL jeden přihlašování informace](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `urn:sharepoint:<YourSharePointServerURL>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [místní SharePoint tým podpory klienta](https://support.office.com/) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat v počítači s příponou CER. Zkopírujte a vložte soubor stažený metadat úplnou cestu do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. Na **místní SharePoint konfigurace** klikněte na tlačítko **místní konfiguraci SharePoint** otevřete **konfigurovat přihlášení** okno. Kopírování **jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace místní služby SharePoint](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > Místní SharePoint aplikace používá SAML 1.1 token, takže Azure AD očekává žádost WS Fed ze serveru SharePoint server a po ověření vystavuje SAML 1.1. token.

7. V okně prohlížeče jiný web Přihlaste se k webu služby SharePoint místní společnosti jako správce.

8. **Konfigurace nového poskytovatele důvěryhodné identity v SharePoint Server 2016**

    Přihlaste se k serveru SharePoint Server 2016 a otevřete prostředí pro správu služby SharePoint 2016. Vyplňte hodnoty $realm, $wsfedurl a $filepath z portálu Azure a spusťte následující příkazy ke konfiguraci nového poskytovatele důvěryhodné identity.

    > [!TIP]
    > Pokud jste ještě nikdy nepracovali pomocí prostředí PowerShell nebo chcete další informace o tom, jak funguje prostředí PowerShell najdete v tématu [prostředí PowerShell služby SharePoint](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Potom použijte následující postup povolení důvěryhodný poskytovatel identity pro aplikaci:

    a. V Centrální správy, přejděte na **Správa webové aplikace** a vyberte aplikaci, který chcete zabezpečit s Azure AD.

    b. Na pásu karet klikněte na tlačítko **zprostředkovatele ověřování** a zvolte zónu, kterou chcete použít.

    c. Vyberte **zprostředkovatele Identity důvěryhodné** a zvolte identifikace zprostředkovatele jste právě zaregistrovali s názvem *AzureAD*.

    d. Na nastavení adresy URL přihlašovací stránky, vyberte **vlastní stránku pro přihlášení** a zadejte hodnotu "/_trust/".

    e. Klikněte na **OK**.

    ![Konfigurace poskytovatele ověřování](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-sharepoint-on-premises-test-user"></a>Vytvoření zkušebního uživatele místní služby SharePoint

1. V Centru pro správu, klikněte na tlačítko **Správa aplikací**.

2. Na **Správa aplikací** stránky v **webových aplikací** klikněte na tlačítko **správu webových aplikací**.

3. Klikněte na příslušnou webovou aplikaci a pak klikněte na **zásady uživatele**.

4. V zásadách pro webovou aplikaci, klikněte na tlačítko **přidat uživatele**.

    ![Vyhledat uživatele podle jejich deklarace identity názvu](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. V **přidat uživatele** dialogovém okně klikněte na příslušnou zónu v **zón**a potom klikněte na **Další**.

6. V **zásady pro webovou aplikaci** v dialogovém **výběr uživatelů** klikněte na položku **Procházet** ikonu.

7. V **najít** textovému poli, zadejte přihlašovací jméno pro uživatele v adresáři a klikněte na **vyhledávání**. </br>Příklad: *demouser@blueskyabove.onmicrosoft.com*.

8. V části AzureAD v zobrazení seznamu, vyberte vlastnosti name a klikněte na **přidat** klikněte **OK** zavřete dialogové okno.

9. V části oprávnění **úplné řízení**.

    ![Udělení úplného řízení uživateli deklarace identity](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Klikněte na tlačítko **Dokončit**a potom klikněte na **OK**.

### <a name="fixing-people-picker"></a>Oprava výběr osob.

Uživatelé teď může přihlásit k SharePoint 2016 pomocí identit z Azure AD, ale stále existují příležitosti pro zlepšení uživatelského rozhraní. Například hledání uživatele uvede více výsledků vyhledávání v dialogu pro výběr osob. Je výsledek hledání pro jednotlivé typy 3 deklarací, které byly vytvořeny v mapování deklarace identity. Vybrat uživatele s využitím nástroje pro výběr osob, musí přesně zadejte své uživatelské jméno a zvolte **název** deklarace identity výsledek.

![Výsledky hledání deklarací identity](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Neexistuje žádné ověření hodnot vyhledávání, které může vést k překlepy nebo typ přiřadit jako deklarace identity uživatelů omylem výběr chybnou **Přezdívka** deklarací identity. To může zabránit uživatelům v úspěšně přístupu k prostředkům.

Jako pomoc s Tento scénář, je open-source řešení názvem [AzureCP](https://yvand.github.io/AzureCP/) poskytující zprostředkovatele vlastních deklarací identity pro SharePoint 2016. Azure AD Graph se bude používat pro vyřešení co uživatelé zadat a provést ověření. Další informace na [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k místní SharePoint.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon místní SharePoint, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **místní SharePoint**.

    ![V seznamu aplikací na odkaz služby SharePoint](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici místní služby SharePoint na přístupovém panelu, měli byste obdržet automaticky přihlášeného k vaší aplikaci místní služby SharePoint.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/sharepoint-on-premises-tutorial/tutorial_general_203.png

