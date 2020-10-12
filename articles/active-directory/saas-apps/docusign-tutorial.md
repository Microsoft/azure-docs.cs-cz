---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s DocuSign | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování (SSO) mezi Azure Active Directory a DocuSign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 51ad28319a372cd791575b5a3e67cc0fd6934be2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981644"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s DocuSign

V tomto kurzu se dozvíte, jak integrovat DocuSign s Microsoft Azure Active Directory (Azure AD). Když integrujete DocuSign s Azure AD, můžete:

* Pomocí Azure AD můžete řídit, kdo má přístup k DocuSign.
* Povolte pro uživatele automatické přihlašování prostřednictvím svých účtů Azure AD a DocuSign je uživatelům.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné DocuSign s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí, abyste ověřili, že:

* DocuSign podporuje poskytovatele služeb **SP** iniciované jednotného přihlašování.

* DocuSign podporuje zřizování uživatelů **za běhu** .

* DocuSign podporuje [Automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).

* Po nakonfigurování DocuSign můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>Přidání DocuSign z Galerie

Pokud chcete nakonfigurovat integraci DocuSign do služby Azure AD, musíte přidat DocuSign z Galerie do svého seznamu spravovaných aplikací pro SaaS:

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V navigačním podokně na levé straně vyberte službu **Azure Active Directory** .
1. Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Docusign** .
1. Na panelu výsledků vyberte **Docusign** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-docusign"></a>Konfigurace a testování jednotného přihlašování Azure AD pro DocuSign

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí DocuSign pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a odpovídajícím uživatelem v DocuSign.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí DocuSign postupujte takto:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) tak, aby uživatelé mohli tuto funkci používat.
    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) .
1. Nakonfigurujte jednotné přihlašování [DOCUSIGN SSO](#configure-docusign-sso) pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte Docusign testovacího uživatele](#create-docusign-test-user) , který vygeneruje protějšek B. Simon v Docusign, která je propojená se zastoupením uživatele v Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování služby Azure AD v Azure Portal, postupujte následovně:

1. V Azure Portal na stránce integrace aplikací **Docusign** najděte část **Správa** a pak vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu pera pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** postupujte následovně:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    c. Do textového pole **Adresa URL odpovědi** zadejte kdokoli z následujících vzorů adresy URL:
    
    | Adresa URL odpovědi |
    |-------------|
    |`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/<IDPID>`|
    |`https://<subdomain>.docusign.net/SAML/`|

    > [!NOTE]
    > Tyto hodnoty v závorkách jsou zástupné symboly. Nahraďte je hodnotami v poli vlastní přihlašovací adresa URL, identifikátor a adresa URL odpovědi. Tyto podrobnosti jsou vysvětleny v části "zobrazení koncových bodů SAML 2,0" dále v tomto kurzu.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (Base64)**. Vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Docusign** zkopírujte na základě vašich požadavků příslušnou adresu URL (nebo adresy URL).

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel**.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **název** zadejte **B. Simon**.  
   1. Do pole **uživatelské jméno** zadejte `<username>@<companydomain>.<extension>` . Například: `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom si poznamenejte hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte B. Simon přístup k DocuSign, aby tento uživatel mohl používat jednotné přihlašování Azure.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Docusign**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a potom v dolní části obrazovky klikněte na tlačítko **Vybrat** .
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

## <a name="configure-docusign-sso"></a>Konfigurace jednotného přihlašování DocuSign

1. K automatizaci konfigurace v DocuSign je nutné nainstalovat rozšíření prohlížeče pro zabezpečené přihlašování k aplikacím výběrem možnosti **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče vyberte **nastavit Docusign**. Budete přesměrováni na aplikaci DocuSign. Odtud zadejte přihlašovací údaje správce pro přihlášení k DocuSign. Rozšíření prohlížeče automaticky nakonfiguruje aplikaci a automatizuje kroky 3 až 5.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit DocuSign ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu DocuSign společnosti jako správce.

4. V pravém horním rohu stránky vyberte logo profilu a pak vyberte **Přejít na správce**.
  
    ![V části profil otevřete Správce.][51]

5. Na stránce řešení domény vyberte **domény**.

    ![Doménová řešení/domény][50]

6. V části **domény** vyberte možnost **doména deklarace identity**.

    ![Možnost domény deklarace identity][52]

7. V dialogovém okně **deklarace identity a domény** zadejte do pole **název domény** doménu vaší společnosti a vyberte možnost **deklarace identity**. Ujistěte se, že jste ověřili doménu a je její stav aktivní.

    ![Dialogová okna pro název domény nebo domény][53]

8. Na stránce řešení domény vyberte **Zprostředkovatelé identity**.
  
    ![Zprostředkovatel identity – možnost][54]

9. V části **Zprostředkovatelé identity** vyberte **Přidat poskytovatele identity**.

    ![Přidat možnost zprostředkovatele identity][55]

10. Na stránce **nastavení zprostředkovatele identity** proveďte tyto kroky:

    ![Pole nastavení zprostředkovatele identity][56]

    a. Do pole **název** zadejte jedinečný název pro vaši konfiguraci. Nepoužívejte mezery.

    b. V **poli Vystavitel zprostředkovatele identity**vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do pole **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. V poli **Adresa URL pro odhlášení zprostředkovatele identity** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    e. Vyberte **Sign Authn Request (podepsat požadavek na podpis**).

    f. V případě, **že chcete odeslat žádost o odeslání Authn**, vyberte **post**.

    například Pro **Odeslat žádost o odhlášení**vyberte **získat**.

    h. V oddílu **mapování vlastních atributů** vyberte možnost **Přidat nové mapování**.

       ![Uživatelské rozhraní Mapování vlastního atributu][62]

    i. Vyberte pole, které chcete namapovat na deklaraci identity Azure AD. V tomto příkladu je deklarace identity **EmailAddress** namapovaná s hodnotou `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` . To je výchozí název deklarace identity z Azure AD pro deklaraci e-mailu. Vyberte **Uložit**.

       ![Pole mapování vlastního atributu][57]

       > [!NOTE]
       > Použijte odpovídající **identifikátor uživatele** k mapování uživatele z Azure AD na Docusign mapování uživatelů. Vyberte správné pole a zadejte odpovídající hodnotu na základě nastavení vaší organizace.

    j. V části **certifikáty poskytovatele identity** vyberte **Přidat certifikát**, nahrajte certifikát, který jste stáhli z portálu Azure AD, a vyberte **Uložit**.

       ![Certifikáty poskytovatele identity/přidat certifikát][58]

    k. V části **Zprostředkovatelé identity** vyberte **Akce**a potom vyberte **koncové body**.

       ![Zprostředkovatelé identity/koncové body][59]

    l. V části **zobrazení koncových bodů SAML 2,0** na portálu pro správu Docusign postupujte takto:

       ![Zobrazit koncové body SAML 2,0][60]
       
       1. Zkopírujte **adresu URL vystavitele poskytovatele služby**a vložte ji do pole **identifikátor** v **základní části Konfigurace SAML** v Azure Portal.
       
       1. Zkopírujte **adresu URL služby vyhodnocení poskytovatele služby**a vložte ji do pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.
       
       1. Zkopírujte **přihlašovací adresu URL poskytovatele služby**a pak ji vložte do pole **Adresa URL pro přihlášení** v základní části **Konfigurace SAML** v Azure Portal. Na konci **přihlašovací adresy URL poskytovatele služeb** se zobrazí hodnota IDPID.

       1. Vyberte **Zavřít**.

### <a name="create-docusign-test-user"></a>Vytvořit testovacího uživatele DocuSign

V této části se v DocuSign vytvoří uživatel s názvem B. Simon. DocuSign podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v DocuSign neexistuje, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Docusign](https://support.docusign.com/).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k DocuSign, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL pro přihlášení k DocuSign přímo a zahájit tok přihlášení.

3. Můžete použít panel Microsoft Access. Když na přístupovém panelu kliknete na dlaždici DocuSign, měli byste se automaticky přihlásit k DocuSign, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování DocuSign můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
