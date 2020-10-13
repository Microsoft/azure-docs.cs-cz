---
title: 'Kurz: Azure Active Directory integrace s Palo Alto Networks – clona | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks – clona.
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
ms.openlocfilehash: 8f2603ce055d18e1ad7e202e0c662e042a1d1d48
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996111"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Kurz: Azure Active Directory integrace s Palo Alto Networks – clona

V tomto kurzu se naučíte, jak integrovat Palo Alto Networks-otvor s Azure Active Directory (Azure AD).
Integrace Palo Alto Networks – clona s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k sítím Palo Alto Networks – clona.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Palo Alto Networks – clonu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Palo Alto Networks – clonu potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Palo Alto Networks – odběr s povoleným jednotným přihlašováním pro otvory

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Palo Alto Networks – clona podporuje **aktualizace SP** a **IDP** , iniciované jednotné přihlašování

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Přidávání Palo Alto Networks – clona z Galerie

Pokud chcete nakonfigurovat integraci sítí Palo Alto do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Palo Alto Networks – clona z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Palo Alto Networks-otvor** .
1. Vyberte **Palo Alto Networks-clona** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Palo Alto Networks – clona na základě testovacího uživatele s názvem **B. Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Palo Alto Networks.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Palo Alto Networks – clona, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte Palo Alto Networks – clonový SSO](#configure-palo-alto-networks---aperture-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    * **[Vytvořte Palo Alto Networks – clonový uživatel pro test](#create-palo-alto-networks---aperture-test-user)** , který bude mít protějšek Britta Simon v Palo Alto Networks-otvor propojený s reprezentací uživatele Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **Palo Alto Networks-** rozhraní Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky zobrazující konfiguraci "základní S A M L" se zvýrazněnými textovými poli "identifikátor" a "Reply U R L" a vybranou akci Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Palo Alto Networks – informace o jednotném přihlašování v doméně a adrese URL](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, kontaktujte [Palo Alto Networks – tým podpory klienta pro clonu](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **nastavení sítě Palo Alto Networks –** rozhraní zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Palo Alto Networks – clona.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Palo Alto Networks-clona**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-palo-alto-networks---aperture-sso"></a>Konfigurace Palo Alto Networks – otvor SSO

1. V jiném okně webového prohlížeče se přihlaste k Palo Alto Networks-clona jako správce.

2. V horním řádku nabídek klikněte na **Nastavení**.

    ![Karta nastavení](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Přejít k části **aplikace** kliknutím na formulář **ověřování** na levé straně nabídky.

    ![Karta ověřování](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Na stránce **ověřování** proveďte následující kroky:
    
    ![Karta ověřování](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Zaškrtněte políčko **Povolit jednotné přihlašování (podporované zprostředkovatele SSP jsou okta, jedno přihlášení)** z pole **jednotného přihlašování** .

    b. Do textového pole **ID zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    c. Kliknutím na **zvolit soubor** nahrajte stažený certifikát z Azure AD do pole **certifikát poskytovatele identity** .

    d. Do textového pole **Adresa URL jednotného přihlašování zprostředkovatele identity** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    e. Přečtěte si část informace o IdP z **clonu** a Stáhněte si certifikát z pole **klíče clony** .

    f. Klikněte na **Uložit**.


### <a name="create-palo-alto-networks---aperture-test-user"></a>Vytvoření Palo Alto Networks – clonový uživatel testu

V této části vytvoříte uživatele s názvem Britta Simon v Palo Alto Networks – clona. Práce s [Palo Alto Networks – tým podpory klientů](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) s rozhraním clony k přidání uživatelů v Palo Alto Networks – rozhraní clony. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Palo Alto Networks – adresa URL pro přihlášení k rozhraní clony, kde můžete spustit tok přihlášení.  

* Přejít na Palo Alto Networks – adresa URL pro přihlášení k rozhraní clony přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Palo Alto Networks – otvor, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici Palo Alto Networks-clon na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit ke Palo Alto Networks – otvor, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Palo Alto Networks – clona můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
