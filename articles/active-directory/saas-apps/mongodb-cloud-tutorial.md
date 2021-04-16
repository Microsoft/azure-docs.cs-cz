---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s cloudem MongoDB | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MongoDB cloudem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: 5904d3eeec3f5880213f8a8c6a41cefbe76801b3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520084"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s cloudem MongoDB

V tomto kurzu se dozvíte, jak integrovat MongoDB Cloud s Azure Active Directory (Azure AD). Když integrujete Cloud MongoDB s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke cloudu MongoDB, MongoDB Atlas, komunitě MongoDB, MongoDB University a MongoDB podpoře.
* Umožněte uživatelům, aby se do cloudu MongoDB automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné MongoDB Cloud s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* MongoDB Cloud podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování.
* MongoDB Cloud podporuje **jenom dobu** zřizování uživatelů.

## <a name="add-mongodb-cloud-from-the-gallery"></a>Přidání cloudu MongoDB z Galerie

Pokud chcete nakonfigurovat integraci MongoDB cloudu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat MongoDB Cloud z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **MongoDB Cloud** .
1. Z panelu výsledků vyberte **Cloud MongoDB** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-mongodb-cloud"></a>Konfigurace a testování jednotného přihlašování Azure AD pro MongoDB Cloud

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí cloudu MongoDB s využitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v MongoDB cloudu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudu MongoDB, proveďte následující kroky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) .
1. Nakonfigurujte jednotné přihlašování [MongoDB do cloudu](#configure-mongodb-cloud-sso) a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte uživatele MongoDB Cloud test](#create-a-mongodb-cloud-test-user) , který bude mít protějšek B. Simon v cloudu MongoDB, který je propojený s Předprezentací Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **cloudových aplikací MongoDB** najděte část **Správa** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednoho Sign-On se stránkou SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v části **základní konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL, která používá následující vzor: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která používá následující vzor: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Vyberte **nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL, která používá následující vzor:  `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory MongoDB Cloud Client](https://support.mongodb.com/). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Cloudová aplikace MongoDB očekává, že kontrolní výrazy SAML mají být v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Snímek obrazovky s výchozími atributy](common/default-attributes.png)

1. Kromě předchozích atributů očekává cloudová aplikace MongoDB několik dalších atributů, které se vrátí zpět v odpovědi SAML. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle svých požadavků.
    
    | Name | Zdrojový atribut|
    | ---------------| --------- |
    | e-mail | User. userPrincipalName |
    | firstName | User. křestní jméno |
    | lastName | User. příjmení |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML federačních metadat**. Vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Snímek obrazovky s oddílem podpisového certifikátu SAML se zvýrazněným odkazem pro stažení](common/metadataxml.png)

1. V části **Nastavení cloudu MongoDB** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Snímek obrazovky s vybraným oddílem cloudu pro Mongo DB se zvýrazněnými adresami URL](common/copy-configuration-urls.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke cloudu MongoDB.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cloud MongoDB**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-mongodb-cloud-sso"></a>Konfigurace jednotného přihlašování MongoDB Cloud

Ke konfiguraci jednotného přihlašování na straně cloudu MongoDB potřebujete příslušné adresy URL zkopírované z Azure Portal. Musíte také nakonfigurovat Federační aplikaci pro cloudovou organizaci MongoDB. Postupujte podle pokynů v [dokumentaci ke cloudu MongoDB](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Pokud máte problém, obraťte se na [tým podpory MongoDB Cloud Support](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Vytvořit uživatele cloudového testu MongoDB

MongoDB Cloud podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. Neexistují žádné další akce, které byste mohli provést. Pokud uživatel ještě v MongoDB cloudu neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na MongoDB Cloud Signing on, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení ke cloudu MongoDB přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni ke cloudu MongoDB, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici MongoDB Cloud v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit ke cloudu MongoDB, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování MongoDB cloudu můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
