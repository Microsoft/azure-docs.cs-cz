---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím přehledů uživatelských zkušeností v Aruba | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Aruba Přehled uživatelského prostředí.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2020
ms.author: jeedes
ms.openlocfilehash: 144e121b140e47ebda62530354943d5d27cce094
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735372"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aruba-user-experience-insight"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s uživatelským prostředím Aruba – přehled

V tomto kurzu se dozvíte, jak integrovat přehled o uživatelském prostředí Aruba s využitím Azure Active Directory (Azure AD). Když integrujete Aruba uživatelských zkušeností s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k přehledům Aruba uživatelského prostředí.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Aruba uživatelského prostředí pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Aruba uživatelského prostředí Insightly předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Přehled uživatelského prostředí Aruba podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* Přehled prostředí Aruba pro uživatele podporuje vytváření uživatelských zkušeností **jenom v čase** .


## <a name="adding-aruba-user-experience-insight-from-the-gallery"></a>Přidání přehledu o zkušenostech uživatele Aruba z Galerie

Pokud chcete nakonfigurovat integraci Aruba uživatelského prostředí do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat přehled o uživatelském prostředí Aruba z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Aruba User Experience Insight** .
1. Vyberte **Aruba uživatelského prostředí přehled** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-aruba-user-experience-insight"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Aruba uživatele přehled zkušeností uživatelů

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Aruba uživatelského prostředí přehled pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v přehledu zkušeností uživatelů v Aruba.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí přehledu zkušeností uživatele Aruba, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Aruba uživatelského prostředí Insightly SSO](#configure-aruba-user-experience-insight-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření Aruba uživatelského prostředí Insight Test User](#create-aruba-user-experience-insight-test-user)** – Pokud chcete mít protějšek B. Simon ve službě Aruba User Experience Insight, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **Přehled zkušeností uživatele s Aruba** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `urn:auth0:cape:<CUSTOMER_SSO_UID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://cape.auth0.com/login/callback?connection=<CUSTOMER_SSO_UID>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://dashboard.capenetworks.com/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pro získání těchto hodnot kontaktujte [tým podpory Aruba uživatele](mailto:support@capenetworks.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aruba uživatelského prostředí Insight očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho aplikace Aruba pro koncové uživatelské prostředí očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name | Zdrojový atribut|
    | ------------ | --------- |
    | názvu | User. userPrincipalName |


1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení přehledu zkušeností uživatele Aruba** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Aruba uživatelského rozhraní pro přehled.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Aruba User Experience Insight**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-aruba-user-experience-insight-sso"></a>Konfigurace uživatelského rozhraní Aruba – přehled jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování na straně **Aruba uživatelského prostředí** , je potřeba odeslat stažený **soubor XML s federačními metadaty** a příslušné zkopírované adresy URL z Azure Portal do [Aruba uživatelského prostředí Insight](mailto:support@capenetworks.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-aruba-user-experience-insight-test-user"></a>Vytvoření Aruba uživatele Insightly Test User Experience

V této části se uživatel s názvem Britta Simon vytvoří v tématu Přehled uživatelského prostředí Aruba. Aruba uživatelského prostředí podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě neexistuje v přehledu zkušeností uživatelů Aruba, vytvoří se nový, který se vytvoří po ověření.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na Aruba uživatelského prostředí – přihlašovací adresa URL, kde můžete spustit tok přihlášení.  

* Přejít na Aruba uživatelské prostředí přehled o přihlašovací adrese URL přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Aruba uživatelského prostředí, pro které jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když v okně moje aplikace kliknete na dlaždici přehled zkušeností uživatele Aruba, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k funkci Aruba uživatelského rozhraní, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Až nakonfigurujete přehled o zkušenostech s uživatelem Aruba, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).