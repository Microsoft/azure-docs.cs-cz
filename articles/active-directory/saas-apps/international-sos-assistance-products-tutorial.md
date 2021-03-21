---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s produkty pro mezinárodní SOS pomoc | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a mezinárodními produkty SOS pomoci.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 77d5ace7ce45ed820053ce8c52d375868c8de305
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736911"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-international-sos-assistance-products"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s produkty pro mezinárodní SOS pomoc

V tomto kurzu se dozvíte, jak integrovat mezinárodní produkty SOS pomoci s Azure Active Directory (Azure AD). Když integrujete mezinárodní produkty SOS pomoci s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k mezinárodním produktům SOS pomoci.
* Umožněte uživatelům, aby se automaticky přihlásili k mezinárodním produktům SOS pomoci prostřednictvím svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Mezinárodní SOS pomocných produktů s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Mezinárodní produkty SOS pomoc podporují jednotné přihlašování v **SP**

* Mezinárodní produkty SOS pomoci podporují zřizování uživatelů **jenom v čase**


## <a name="adding-international-sos-assistance-products-from-the-gallery"></a>Přidání produktů mezinárodní SOS pomoci z Galerie

Pokud chcete nakonfigurovat integraci mezinárodních produktů SOS pomoci do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat mezinárodní produkty SOS pomoci z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **mezinárodní produkty SOS pomoc** .
1. Z panelu výsledků vyberte **mezinárodní produkty SOS pomoci** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-international-sos-assistance-products"></a>Konfigurace a testování jednotného přihlašování Azure AD pro mezinárodní produkty SOS Assistance

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí mezinárodních produktů SOS pomoci pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v mezinárodních produktech SOS pomoci.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí mezinárodních SOS pomocných produktů, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurovat mezinárodní SOS pomocných produktů](#configure-international-sos-assistance-products-sso)** v rámci jednotného přihlašování – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte si mezinárodní produkty pro SOS pomoc s testovacím uživatelem](#create-international-sos-assistance-products-test-user)** , abyste měli protějšek B. Simon v mezinárodních produktech SOS pomoci, které jsou propojeny s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce mezinárodní integrace aplikace **SOS Assistance Products** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.outsystemsenterprise.com/myassist`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.internationalsos.com/sso/saml2/<CUSTOM_ID>`

    c. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://www.okta.com/saml2/service-provider/<IN>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, adresy URL odpovědi a identifikátoru. Pokud chcete získat tyto hodnoty, kontaktujte [mezinárodní tým podpory SOS Assistance](mailto:onlinehelp@internationalsos.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)
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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k mezinárodním produktům pro SOS pomoc.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **mezinárodní produkty SOS pomoci**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-international-sos-assistance-products-sso"></a>Konfigurace mezinárodních SOS pomocných produktů jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování na straně **mezinárodních SOS pomocných produktů** , je potřeba odeslat **adresu URL federačních metadat aplikace** [týmu podpory pro mezinárodní technickou podporu SOS](mailto:onlinehelp@internationalsos.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-international-sos-assistance-products-test-user"></a>Vytvořit mezinárodní produkt SOS pomoci pro testování uživatelů

V této části se v mezinárodní produktech SOS pomoci vytvoří uživatel s názvem Britta Simon. Mezinárodní produkt pro SOS pomoc podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel už v mezinárodním SOS pomocných produktech ještě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na mezinárodní přihlašovací adresu URL produktů SOS Assistance, kde můžete spustit tok přihlášení. 

* Přejít na mezinárodní přihlašování k produktům SOS pomoc na adrese URL přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici mezinárodní produkty SOS pomoc v části Moje aplikace, přesměruje se na mezinárodní přihlašovací adresu pro produkty SOS Assistance. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete mezinárodní produkty pro SOS pomoc, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).