---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s DocuSign | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe1f018612e889f49993895d88cdaf9ad732b393
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306299"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s DocuSign

V tomto kurzu se dozvíte, jak integrovat DocuSign s Azure Active Directory (Azure AD). Když integrujete DocuSign s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k DocuSign.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k DocuSign svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* DocuSign odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* DocuSign podporuje jednotné přihlašování iniciované v **SP**

* DocuSign podporuje zřizování uživatelů **za běhu**

* DocuSign podporuje [Automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial) .

## <a name="adding-docusign-from-the-gallery"></a>Přidání DocuSign z Galerie

Pokud chcete nakonfigurovat integraci DocuSign do služby Azure AD, musíte přidat DocuSign z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Docusign** .
1. Na panelu výsledků vyberte **Docusign** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Konfigurace a testování jednotného přihlašování Azure AD pro DocuSign

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí DocuSign pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v DocuSign.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí DocuSign, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte DOCUSIGN SSO](#configure-docusign-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Docusign Test User](#create-docusign-test-user)** -to, abyste měli protějšek B. Simon v Docusign, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Docusign** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru, který je vysvětlen později v tomto kurzu v části **koncové body SAML 2,0** .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Docusign** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k DocuSign.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Docusign**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-docusign-sso"></a>Konfigurace jednotného přihlašování DocuSign

1. Pokud chcete automatizovat konfiguraci v rámci DocuSign, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Docusign nastavení** a nasměrujte vás na aplikaci Docusign. Odtud zadejte přihlašovací údaje správce, které se přihlásí k DocuSign. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit DocuSign ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu DocuSign společnosti jako správce a proveďte následující kroky:

4. V pravém horním rohu stránky klikněte na **logo** Profile a potom klikněte na **Přejít na správce**.
  
    ![Konfigurace jednotného přihlašování][51]

5. Na stránce řešení domény klikněte na **domény** .

    ![Konfigurace jednotného přihlašování][50]

6. V části **domény** klikněte na možnost **doména deklarací identity**.

    ![Konfigurace jednotného přihlašování][52]

7. V dialogovém okně **deklarace identity a domény** zadejte do textového pole **název domény** doménu vaší společnosti a potom klikněte na **deklarace identity**. Ujistěte se, že jste ověřili doménu a je stav aktivní.

    ![Konfigurace jednotného přihlašování][53]

8. Na stránce řešení domény klikněte na **Zprostředkovatelé identity**.
  
    ![Konfigurace jednotného přihlašování][54]

9. V části **Zprostředkovatelé identity** klikněte na **Přidat zprostředkovatele identity**. 

    ![Konfigurace jednotného přihlašování][55]

10. Na stránce **nastavení zprostředkovatele identity** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování][56]

    a. Do textového pole **název** zadejte jedinečný název pro vaši konfiguraci. Nepoužívejte mezery.

    b. Do **textového pole vystavitele zprostředkovatele identity**vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL pro odhlášení zprostředkovatele identity** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    e. Vyberte **Sign Authn Request (podepsat požadavek na podpis**).

    f. Jako **odešlete žádost o Authn podle**, vyberte **post**(Odeslat).

    g. Jako **Odeslat žádost o odhlášení**vyberte **získat**.

    h. V oddílu **mapování vlastních atributů** klikněte na **Přidat nové mapování**.

    ![Konfigurace jednotného přihlašování][62]

    i. Vyberte pole, které chcete namapovat na deklaraci identity Azure AD. V tomto příkladu je deklarace identity **EmailAddress** namapovaná s hodnotou **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Jedná se o výchozí název deklarace identity z Azure AD pro e-mailovou deklaraci identity a pak klikněte na **Uložit**.

    ![Konfigurace jednotného přihlašování][57]

    > [!NOTE]
    > Použijte odpovídající **identifikátor uživatele** k mapování uživatele z Azure AD na Docusign mapování uživatelů. Vyberte správné pole a zadejte odpovídající hodnotu na základě nastavení vaší organizace.

    j. V části **certifikáty poskytovatele identity** klikněte na **Přidat certifikát**a pak Nahrajte certifikát, který jste stáhli z portálu Azure AD, a klikněte na **Uložit**.

    ![Konfigurace jednotného přihlašování][58]

    k. V části **Zprostředkovatelé identity** klikněte na **Akce**a potom na **koncové body**.

    ![Konfigurace jednotného přihlašování][59]

    l. V části **zobrazení koncových bodů SAML 2,0** na **portálu pro správu Docusign**proveďte následující kroky:

    ![Konfigurace jednotného přihlašování][60]

    * Zkopírujte **adresu URL vystavitele poskytovatele služby**a vložte ji do textového pole **identifikátoru** v **základní části Konfigurace SAML** na Azure Portal.

    * Zkopírujte **přihlašovací adresu URL poskytovatele služby**a vložte ji do textového pole pro **přihlašovací adresu URL** v **základní části Konfigurace SAML** na Azure Portal.

    * Klikněte na **Zavřít** .

### <a name="create-docusign-test-user"></a>Vytvořit testovacího uživatele DocuSign

V této části se v DocuSign vytvoří uživatel s názvem B. Simon. DocuSign podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v DocuSign neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Docusign](https://support.docusign.com/).

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici DocuSign, měli byste se automaticky přihlásit k DocuSign, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si DocuSign s Azure AD](https://aad.portal.azure.com/)

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