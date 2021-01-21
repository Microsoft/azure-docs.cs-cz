---
title: 'Kurz: Azure Active Directory integraci s cloudem Splunk Enterprise a Splunk | Microsoft Docs'
description: Naučte se konfigurovat jednotné přihlašování mezi Azure Active Directory a Splunk Enterprise a Splunk cloudem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 18d64e2b2ac359a9036ebd548ef886cb0efd3405
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622193"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Kurz: Azure Active Directory integrací s cloudem pro Splunk Enterprise a Splunk

V tomto kurzu se dozvíte, jak integrovat Splunk Enterprise a Splunk Cloud s Azure Active Directory (Azure AD). Když integrujete Cloud Splunk Enterprise a Splunk s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke cloudu Splunk Enterprise a Splunk.
* Umožněte uživatelům, aby se automaticky přihlásili ke cloudu Splunk Enterprise a Splunk pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s cloudem Splunk Enterprise a Splunk potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné Splunk Enterprise a Splunk pro jednotné přihlašování s povoleným cloudem

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloud Splunk Enterprise a Splunk podporuje jednotné přihlašování (SSO) iniciované v **SP**

## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Přidání cloudu Splunk Enterprise a Splunk z Galerie

Pokud chcete nakonfigurovat integraci cloudu Splunk Enterprise a Splunk do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie Splunk Enterprise a Splunk Cloud.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Splunk Enterprise a Splunk Cloud** .
1. Z panelu výsledků vyberte **Splunk Enterprise a Splunk Cloud** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Splunk Enterprise a Splunk Cloud

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí cloudu Splunk Enterprise a Splunk s využitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudu Splunk Enterprise a Splunk.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudu Splunk Enterprise a Splunk, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[KONFIGURUJTE jednotné přihlašování Splunk Enterprise a Splunk pro Cloud](#configure-splunk-enterprise-and-splunk-cloud-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si uživatele cloudového testu Splunk Enterprise a Splunk](#create-splunk-enterprise-and-splunk-cloud-test-user)** , abyste měli protějšky B. Simon v Splunk Enterprise a Splunk cloudu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace **cloudových aplikací Splunk Enterprise a Splunk** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)
4. V části **základní konfigurace SAML** proveďte následující vzor:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<splunkserverUrl>/app/launcher/home`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<splunkserverUrl>`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory Splunk Enterprise a Splunk Cloud Client support](https://www.splunk.com/en_us/about-splunk/contact-us.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Splunk Enterprise a Splunk cloudu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Splunk Enterprise a Splunk Cloud**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-splunk-enterprise-and-splunk-cloud-sso"></a>Konfigurace jednotného přihlašování Splunk Enterprise a Splunk pro Cloud

  Ke konfiguraci jednotného přihlašování na straně **cloudu Splunk Enterprise a Splunk** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Splunk Enterprise a Splunk](https://www.splunk.com/en_us/about-splunk/contact-us.html). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.


### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Vytvoření uživatele Cloud test Splunk Enterprise a Splunk

V této části vytvoříte uživatele s názvem Britta Simon v cloudu Splunk Enterprise a Splunk. Pokud chcete přidat uživatele na cloudovou platformu Splunk Enterprise a Splunk, pracujte s [týmem Splunk Enterprise a Splunk Cloud Support](https://www.splunk.com/en_us/about-splunk/contact-us.html) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL Splunk Enterprise a Splunk pro přihlášení do cloudu, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL cloudového přihlašování Splunk Enterprise a Splunk a spustit tok přihlášení přímo z tohoto účtu.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Cloud Splunk Enterprise a Splunk v části Moje aplikace, přesměruje se na adresu URL Splunk Enterprise a Splunk Cloud Signing on. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování cloudu Splunk Enterprise a Splunk můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)