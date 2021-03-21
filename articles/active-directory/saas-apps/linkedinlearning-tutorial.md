---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí programu LinkedIn Learning | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a učením LinkedInu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: e5c6bf41e1a3bf92c9141c0d3b54dd58ead2bf3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727296"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí programu LinkedIn Learning

V tomto kurzu se dozvíte, jak integrovat výuku LinkedInu pomocí Azure Active Directory (Azure AD). Při integraci LinkedInového učení s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k LinkedIn Learning.
* Umožněte uživatelům, aby se přihlásili do výuky LinkedIn pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné LinkedIn Learning s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LinkedIn Learning podporuje **SP a IDP** iniciované jednotné přihlašování.
* Učení LinkedIn podporuje **při zřizování uživatelů jenom včas**


## <a name="adding-linkedin-learning-from-the-gallery"></a>Přidání učení LinkedInu z Galerie

Pokud chcete nakonfigurovat integraci LinkedInového učení do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat LinkedIn Learning z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **LinkedIn Learning** .
1. Vyberte **LinkedIn Learning** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-learning"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro učení LinkedIn

Pomocí testovacího uživatele s názvem **B. Simon** nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí LinkedIn learningu. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v učení LinkedIn.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí LinkedIn Learning, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování **[LinkedInu](#configure-linkedin-learning-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření testovacího uživatele pro LinkedIn Learning](#create-linkedin-learning-test-user)** – Pokud chcete mít protějšek B. Simon ve výukovém programu LinkedIn, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace **LinkedIn Learning** Application Integration, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

     a. Do textového pole **identifikátoru** zadejte **ID entity** zkopírované z portálu LinkedIn. 

    b. Do textového pole **Adresa URL odpovědi** zadejte **adresu URL služby assertion Consumer Service (ACS)** zkopírovanou z portálu LinkedIn.

    c. Pokud chcete nakonfigurovat aplikaci v režimu **iniciované SP** , klikněte na možnost **nastavit další adresy URL** v **základní části Konfigurace SAML**  , kde budete zadávat přihlašovací adresu URL. Pokud chcete vytvořit přihlašovací adresu URL, zkopírujte **adresu URL služby assertion Consumer Service (ACS)** a nahraďte/SAML/pomocí/Login/. Až to bude hotové, přihlašovací adresa URL by měla mít následující vzor:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Informace o jednotném přihlašování k doméně výuky LinkedIn a adres URL](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Tyto hodnoty nejsou reálné hodnoty. Tyto hodnoty aktualizujete skutečným identifikátorem a adresou URL odpovědi, která se vysvětluje později v kurzu **Konfigurace jednotného přihlašování k LinkedInu** v tématu.

1. Aplikace LinkedIn Learning očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Aplikace LinkedIn Learning očekává, že **NameIdentifier** budou mapovány pomocí **User. mail**, takže je nutné upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení učení LinkedInu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k učení LinkedIn.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **LinkedIn Learning**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-linkedin-learning-sso"></a>Konfigurace jednotného přihlašování k LinkedInu

1. V jiném okně webového prohlížeče se přihlaste ke svému tenantovi LinkedIn Learning jako správce.

2. V **centru účtů** klikněte na **globální nastavení** v části **Nastavení**. V rozevíracím seznamu vyberte také možnost **učení – výchozí** .

    ![Snímek obrazovky zobrazuje globální nastavení, kde můžete vybrat výchozí.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Klikněte **nebo klikněte sem, abyste načetli a zkopírovali jednotlivá pole z formuláře** a zkopírovali **ID entity** a **adresu URL služby kontrolního výrazu (ACS)** a vložili je do části **základní konfigurace SAML** v Azure Portal.

    ![Snímek obrazovky zobrazuje jednu Sign-On, kde můžete zadat hodnoty, které jsou popsány.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Přejít na část **Nastavení správce LinkedIn** . Nahrajte soubor XML, který jste stáhli z Azure Portal kliknutím na možnost **nahrát soubor XML** .

    ![Snímek obrazovky ukazuje konfiguraci nastavení poskytovatele služby LinkedIn Service S příponami v, kde můžete nahrát soubor X M L.](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Kliknutím **na** povolíte jednotné přihlašování. Změna stavu jednotného přihlašování z **nepřipojeného** k **připojenému**

    ![Snímek obrazovky zobrazuje jednu Sign-On, kde můžete povolit ověřování uživatelů pomocí s S S S.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Vytvořit testovacího uživatele pro LinkedIn Learning

Aplikace LinkedIn Learning podporuje při zřizování uživatelů jenom v čase a po ověření, že se uživatelé budou automaticky vytvářet v aplikaci. Na stránce nastavení pro správu na portálu LinkedIn Learning přepínejte přepínač **automaticky přiřazovat licence** k aktivnímu způsobu zřizování a k tomu se také přiřadí licence uživateli.

   ![Vytvoření testovacího uživatele Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro učení výuky, kde můžete spustit tok přihlášení.  

* Přejít přímo na adresu URL pro přihlášení k LinkedInu a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni ke vzdělávacímu programu LinkedIn, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici učení LinkedInu v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k učení LinkedIn, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete výuku LinkedInu, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).