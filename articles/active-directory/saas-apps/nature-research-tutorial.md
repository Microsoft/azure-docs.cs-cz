---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k výzkumu přírody | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a povahou výzkumu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: d68305087264d6ba2306ba4c345064693b595c94
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234014"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nature-research"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k výzkumu přírody

V tomto kurzu se dozvíte, jak integrovat výzkum pomocí Azure Active Directory (Azure AD). Při integraci druhu výzkumu s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k výzkumu povahy.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k povaze výzkumu s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Možnost prozkoumat předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Výzkum s povahou podporuje **SP a IDP** iniciované jednotné přihlašování

## <a name="adding-nature-research-from-the-gallery"></a>Přidání výzkumu povahy z Galerie

Pokud chcete nakonfigurovat integraci druhu výzkumu do služby Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat z Galerie svůj druh výzkumu.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace** .
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **příroda Research** .
1. Vyberte možnost **prozkoumat** výsledky z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-nature-research"></a>Konfigurace a testování jednotného přihlašování Azure AD pro účely výzkumu

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s povazem výzkumu pomocí testovacího uživatele s názvem **B. Simon** . Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci povahy výzkumu.

Ke konfiguraci a testování jednotného přihlašování služby Azure AD s povazem výzkumu proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte si jednotné přihlašování k výzkumu](#configure-nature-research-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce **příroda Research** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování** .
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML** .
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované v **IDP** , můžete v **základním oddílu konfigurace SAML** předem vyplněné hodnoty adresy URL identifikátoru a odpovědi. je třeba zadat hodnotu stavu přenosu.

    Do textového pole **stav přenosu** zadejte adresu URL: `https://www.nature.com`  
    klikněte na **Uložit** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://sp.nature.com/saml/login?idp=<IDP_ENTITY_ID>`

    > [!NOTE]
    > Hodnota URL Sign-On není reálné číslo. `<IDP_ENTITY_ID>` je identifikátor Azure AD zkopírovaný z oddílu **Nastavení výzkumu pro povaze** . Můžete se také podívat na vzory uvedené v části základní konfigurace SAML v Azure Portal.

1. Klikněte na **Uložit** .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k výzkumu povahy.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. V seznamu aplikace vyberte možnost **výzkum druhu** .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny** .
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-nature-research-sso"></a>Konfigurace jednotného přihlašování k výzkumu

Pokud chcete nakonfigurovat jednotné přihlašování na straně **výzkumu s Povazem** , musíte poslat **adresu URL federačních metadat aplikace** pro [tým technické podpory pro výzkum](mailto:onlineservice@springernature.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro označení výzkumu, kde můžete spustit tok přihlášení.  

* Můžete přímo přejít na adresu URL pro přihlašování k výzkumu a spustit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k výzkumu povahy, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když na přístupovém panelu kliknete na dlaždici prozkoumat výzkum, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit ke zdroji povaze, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete kontrolu přírody, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


