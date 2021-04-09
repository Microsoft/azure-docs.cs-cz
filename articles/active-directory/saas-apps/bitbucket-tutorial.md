---
title: 'Kurz: Azure Active Directory integrace se službou SAML SSO pro Bitbucket podle rezoluce GmbH | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAML SSO pro Bitbucket pomocí rezoluce GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: aa5e3e88ceb957728799f27482de7477ba6b7b48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621243"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Kurz: Azure Active Directory integrace se službou SAML SSO pro Bitbucket podle rezoluce GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování SAML pro Bitbucket pomocí řešení GmbH s Azure Active Directory (Azure AD). Při integraci jednotného přihlašování SAML pro Bitbucket pomocí řešení GmbH s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup toSAML SSO pro Bitbucket podle rezoluce GmbH.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k toSAML SSO pro Bitbucket pomocí rezoluce GmbH s jejich účty Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.


## <a name="prerequisites"></a>Požadavky

K nakonfigurování integrace služby Azure AD pomocí jednotného přihlašování SAML pro Bitbucket podle rezoluce GmbH budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* JEDNOTNÉ přihlašování SAML pro Bitbucket podle řešení GmbH s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jednotné přihlašování SAML pro Bitbucket podle rezoluce GmbH podporuje **aktualizace SP a IDP,** které iniciovaly jednotné přihlašování
* Jednotné přihlašování SAML pro Bitbucket podle rezoluce GmbH podporuje **jenom při** zřizování uživatelů.


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Přidání jednotného přihlašování SAML pro Bitbucket podle rezoluce GmbH z Galerie

Pokud chcete nakonfigurovat integraci SAML SSO pro Bitbucket podle rezoluce GmbH na Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat jednotné přihlašování SAML pro Bitbucket podle rezoluce GmbH z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **SAML SSO pro Bitbucket by Resolution GmbH** .
1. Z výsledků vyberte **SAML SSO pro Bitbucket podle rezoluce GmbH** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro SAML SSO for Bitbucket podle rezoluce GmbH

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro Bitbucket podle rezoluce GmbH pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci SAML SSO pro Bitbucket podle rezoluce GmbH.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování služby Azure AD pomocí jednotného přihlašování SAML pro Bitbucket podle rezoluce GmbH, proveďte následující kroky:


1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte jednotné přihlašování SAML pro Bitbucket podle rezoluce GmbH SSO](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvořte jednotné přihlašování SAML pro Bitbucket podle řešení GmbH Test User](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** – Pokud chcete mít protějšek Britta Simon v jednotném přihlašování SAML pro Bitbucket podle rezoluce GmbH, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.
 
1. V Azure Portal na stránce rozhraní **SAML SSO pro Bitbucket podle** článku Application Integration GmbH najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **vybrat jednu Sign-On metodu** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** :


    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :
    
    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [podporu SAML SSO pro Bitbucket podle týmu podpory pro klienty](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom heslo zapište.
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k JEDNOTNÉmu přihlašování SAML pro Bitbucket podle rezoluce GmbH.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte položku **SAML SSO pro Bitbucket podle rezoluce GmbH**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte možnost **Přidat uživatele**. Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **B. Simon** . Pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>Konfigurace jednotného přihlašování SAML pro Bitbucket podle rezoluce GmbH SSO

1. Přihlaste se ke svému jednotnému přihlašování SAML pro Bitbucket pomocí webu usnesení GmbH jako správce.

2. Na pravé straně hlavního panelu nástrojů klikněte na **Nastavení**.

3. Přejděte na část účty a na řádku nabídek klikněte na **SingleSignon SAML** .

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Na **stránce konfigurace modulu plug-in SAML SIngleSignOn** klikněte na **Přidat IDP**. 

    ![Přidat IDP](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Na stránce **Vyberte poskytovatele identity SAML** proveďte následující kroky:

    ![Zprostředkovatel identity](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Jako **Azure AD** vyberte **typ IDP** .

    b. Do textového pole **název** zadejte název.

    c. Do textového pole **Popis** zadejte popis.

    d. Klikněte na **Next** (Další).

6. Na **stránce Konfigurace zprostředkovatele identity** klikněte na **Další**.

    ![Konfigurace identity](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Na stránce **importovat metadata IDP SAML** klikněte na **načíst soubor** a nahrajte soubor **XML s metadaty** , který jste stáhli z Azure Portal.

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Klikněte na **Next** (Další).

9. Klikněte na **Uložit nastavení**.

    ![Uložit](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Vytvoření jednotného přihlašování SAML pro Bitbucket podle řešení GmbH Test User

Cílem této části je vytvořit uživatele s názvem Britta Simon v jednotném přihlašování SAML pro Bitbucket podle rezoluce GmbH. Jednotné přihlašování SAML pro Bitbucket podle rezoluce GmbH podporuje zřizování za běhu a také uživatele je možné vytvořit ručně, obraťte se na [tým podpory SAML pro Bitbucket podle](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) vašeho požadavku.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL jednotného přihlašování SAML pro Bitbucket podle adresy URL pro podpis GmbH, kde můžete spustit tok přihlášení.  

* Přihlaste se k SAML SSO pro Bitbucket podle adresy URL pro přihlašování na základě překladu GmbH a potom z něj zahajte tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni ke službě SAML SSO pro Bitbucket podle rezoluce GmbH, pro kterou jste si nastavili jednotné přihlašování.

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SAML SSO for Bitbucket by v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlašovacích údajů a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit ke službě SAML SSO pro Bitbucket podle rezoluce GmbH, pro kterou jste si nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete jednotné přihlašování SAML pro Bitbucket podle rezoluce GmbH, můžete vynutili řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace se rozšíří z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).