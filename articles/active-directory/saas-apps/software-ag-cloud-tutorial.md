---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s nástrojem Software AG Cloud | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cloudem Software AG.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: 3c381d8000ce21e72d6426ea924d0a3dc467e9ef
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592003"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Kurz: Azure Active Directory integraci jednotného přihlašování do programu Software AG Cloud

V tomto kurzu se dozvíte, jak integrovat Software AG Cloud s Azure Active Directory (Azure AD). Když integruje Cloud Software AG s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke cloudu Software AG.
* Umožněte, aby se vaši uživatelé automaticky přihlásili do cloudu Software AG pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr povoleného cloudového jednotného přihlašování (SSO) softwaru AG.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloud AG pro software **společnosti podporuje jednotné** přihlašování.
* Cloud AG pro software je podporovaný **jenom při** zřizování uživatelů

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Přidání cloudu Software AG z Galerie

Pokud chcete nakonfigurovat integraci softwaru software AG Cloud do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat software AG Cloud z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **software software AG Cloud** .
1. Z panelu výsledků vyberte **Software AG Cloud** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro software AG Cloud

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí programu Software AG Cloud s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudu Software AG.

Ke konfiguraci a testování jednotného přihlašování služby Azure AD pomocí cloudu Software AG proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace nástroje Software AG cloud SSO](#configure-software-ag-cloud-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte si uživatele Cloud test software AG](#create-software-ag-cloud-test-user)** , který bude mít protějšek B. Simon v cloudu Software AG, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace se **cloudovou aplikací Software AG** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory pro cloudového klienta softwaru AG](mailto:support@softwareag.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit cloudový software AG** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke službě Software AG Cloud.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **program Software AG Cloud**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-software-ag-cloud-sso"></a>Konfigurace programu Software AG cloud SSO

1. V jiném okně webového prohlížeče se přihlaste do cloudového webu Software AG jako správce.

1.  Klikněte na **Správa** .

    ![Konfigurace cloudové správy softwaru AG](./media/software-ag-cloud-tutorial/admin.png)

1. Přejít na **jednotné přihlašování > přidat zprostředkovatele identity**

    ![Konfigurace poskytovatele cloudových identit softwaru AG](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Na následující stránce proveďte následující kroky.

    ![Konfigurace softwaru software AG Cloud postupujte podle kroků](./media/software-ag-cloud-tutorial/saml-1.png)

    a. Do textového pole **Zobrazovaný název zprostředkovatele identity** zadejte název jako `azure ad` .

    b. V rámci **jedinečného identifikátoru zprostředkovatele identity pro použití v textovém poli s identifikátorem URI pro přesměrování cloudu softwaru AG** zadejte jedinečný název poskytovatele identity. Pole **identifikátor URI pro přesměrování cloudu Software AG** se aktualizuje a NAplní identifikátorem URI. Zkopírujte tento identifikátor URI a použijte ho ke konfiguraci **ID entity** a dalších informací v Azure Portal podle definovaných schémat.

    c. Importujte soubor **XML federačních metadat** v **konfiguraci zprostředkovatele identity** a klikněte na **Další**.

    d. Přejít na stránku **Konfigurace** a podle potřeby pole vyplňte.

### <a name="create-software-ag-cloud-test-user"></a>Vytvořit uživatele cloudového testu softwaru AG

V této části se v cloudu Software AG vytvoří uživatel s názvem Britta Simon. Software AG Cloud podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v cloudu Software AG ještě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následující možnosti. 

Za předpokladu, že Microsoft Azure je v cloudu Software AG nakonfigurovaný jako poskytovatel, přejděte na `www.softwareag.cloud` tlačítko přihlásit a klikněte na tlačítko pro přihlášení a zadejte název prostředí. Na další obrazovce klikněte na odkaz Přihlásit se pomocí <IDP NAME> a zadejte přihlašovací údaje. Po ověření se budete přihlásíte k domovské stránce cloudu Software AG a přejdete na ni.

## <a name="next-steps"></a>Další kroky

Po nakonfigurování cloudu Software AG můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


