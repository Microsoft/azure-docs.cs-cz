---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s nástrojem Saba Cloud | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Saba Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 5ce9eb41755d7faa2ce00b38dfd971313443bfb7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572611"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s Saba Cloud

V tomto kurzu se dozvíte, jak integrovat službu Saba Cloud s Azure Active Directory (Azure AD). Když integrujete službu Saba Cloud s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k aplikaci Saba Cloud.
* Umožněte, aby se vaši uživatelé automaticky přihlásili do aplikace Saba Cloud se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zasaba předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Saba Cloud podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* Saba Cloud podporuje zřizování uživatelů **jenom v čase** .
* Pro povolení jednotného přihlašování se teď dá pro službu Azure AD nakonfigurovat možnost Saba cloudová mobilní aplikace. V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

## <a name="adding-saba-cloud-from-the-gallery"></a>Přidání aplikace Saba Cloud z Galerie

Pokud chcete nakonfigurovat integraci aplikace Saba Cloud do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Saba Cloud z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole příkaz **Saba Cloud** .
1. Vyberte možnost **Saba Cloud** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Saba Cloud

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí aplikace Saba Cloud pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji Saba Cloud.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí aplikace Saba Cloud, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace Saba SSO cloud SSO](#configure-saba-cloud-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. Vytvořte nenáročného **[cloudového testu uživatele](#create-saba-cloud-test-user)** – Pokud chcete mít protějšek B. Simon v Saba cloudu, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.
1. **[Otestujte jednotné přihlašování pro Saba Cloud (mobilní zařízení)](#test-sso-for-saba-cloud-mobile)** , abyste ověřili, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **Saba Cloud** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<CUSTOMER_NAME>_SPLN_PRINCIPLE`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SIGN-ON URL>/Saba/saml/SSO/alias/<ENTITY_ID>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<CUSTOMER_NAME>.sabacloud.com`

    b. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` nebo v případě, že je pro MICROSITE nakonfigurován protokol SAML, zadejte adresu URL pomocí následujícího vzoru: `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > Další informace o konfiguraci RelayState najdete na [tomto](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html) odkazu.

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi, přihlašovací adresou URL a stavem přenosu. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory společnosti Saba cloudových klientů](mailto:support@saba.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části nastavení nedostatečného **cloudu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Saba cloudu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Saba Cloud**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-saba-cloud-sso"></a>Konfigurovat Saba cloud SSO

1. Přihlaste se k webu společnosti Saba cloudové společnosti jako správce.
1. Klikněte na ikonu **nabídky** , klikněte na **správce** a pak vyberte kartu **správce systému** .

    ![snímek obrazovky pro správce systému](./media/saba-cloud-tutorial/system.png)

1. V **konfiguraci systému** vyberte možnost **Nastavení SAML SSO Setup** a klikněte na tlačítko **nastavit jednotné přihlašování SAML** . 

    ![snímek obrazovky pro konfiguraci](./media/saba-cloud-tutorial/configure.png)

1. V překryvném okně vyberte v rozevíracím seznamu **microsite** a klikněte na **Přidat a nakonfigurovat**.

    ![snímek obrazovky pro přidání webu/microsite](./media/saba-cloud-tutorial/microsite.png)

1. V části **Konfigurace IDP** klikněte na **Procházet** a nahrajte soubor **XML federačních metadat** , který jste stáhli z Azure Portal. Povolte zaškrtávací políčko **IDP pro konkrétní lokalitu** a klikněte na **importovat**.

    ![snímek obrazovky s importem certifikátu](./media/saba-cloud-tutorial/certificate.png) 

1. V části **Konfigurovat SP** Zkopírujte hodnotu **alias entity** a vložte tuto hodnotu do TEXTOVÉHO pole **identifikátor (ID entity)** v části **základní konfigurace SAML** v Azure Portal. Klikněte na **Generovat**.

    ![snímek obrazovky pro konfiguraci SP](./media/saba-cloud-tutorial/generate-metadata.png) 

1. V části **Konfigurace vlastností** ověřte naplněná pole a klikněte na **Uložit**. 

    ![snímek obrazovky pro konfiguraci vlastností](./media/saba-cloud-tutorial/configure-properties.png) 

### <a name="create-saba-cloud-test-user"></a>Vytvořit Saba test uživatele pro Cloud

V této části se ve Saba cloudu vytvoří uživatel s názvem Britta Simon. Saba Cloud podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v nástroji Saba Cloud ještě neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud chcete povolit protokol SAML jenom při zřizování uživatelů pomocí Saba cloudu, přečtěte si prosím [tuto](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) dokumentaci.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k systému Saba, kde můžete spustit tok přihlášení.  

* Můžete přímo přejít na adresu URL pro přihlášení k cloudu a spustit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni ke službě Saba Cloud, pro kterou jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici zahodit Cloud v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit ke službě Saba Cloud, pro kterou jste si nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> Pokud přihlašovací adresa URL není naplněná ve službě Azure AD, aplikace se považuje za spuštěný režim IDP a pokud se naplní přihlašovací adresa URL, služba Azure AD vždycky přesměruje uživatele na nenáročné cloudovou aplikaci pro vyvolaný tok poskytovatele služby.

## <a name="test-sso-for-saba-cloud-mobile"></a>Testování jednotného přihlašování pro Saba Cloud (mobilní zařízení)

1. Otevřete možnost Saba cloudová mobilní aplikace, zadejte **název lokality** do textového pole a klikněte na **ENTER**.

    ![Snímek obrazovky s názvem webu](./media/saba-cloud-tutorial/site-name.png)

1. Zadejte svou **e-mailovou adresu** a klikněte na **Další**.

    ![Snímek obrazovky s e-mailovou adresou](./media/saba-cloud-tutorial/email-address.png)

1. Nakonec po úspěšném přihlášení se zobrazí stránka aplikace.

    ![Snímek obrazovky pro úspěšné přihlášení](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete aplikaci Saba Cloud, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


