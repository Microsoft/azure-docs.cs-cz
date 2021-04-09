---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s platformou analýzy Looker | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a analytickou platformou Looker.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: dbb6f6d278256730e77677e78f452615fe4b611e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180735"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-looker-analytics-platform"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s platformou Looker Analytics

V tomto kurzu se dozvíte, jak integrovat platformu Looker Analytics s Azure Active Directory (Azure AD). Když integrujete platformu Looker Analytics s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k platformě Looker Analytics.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Looker analytické platformě pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Looker Analytics Platform (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Platforma Looker Analytics podporuje jednotné přihlašování (SSO) **a IDP** .
* Looker Analytics Platform podporuje **jenom časové** zřizování uživatelů


## <a name="adding-looker-analytics-platform-from-the-gallery"></a>Přidání platformy Looker Analytics z Galerie

Pokud chcete nakonfigurovat integraci Looker Analytics Platform do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat platformu Looker Analytics z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Looker Analytics Platform** .
1. Na panelu výsledků vyberte **Looker Analytics Platform** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-looker-analytics-platform"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Looker Analytics Platform

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s Looker Analytics platformou pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Looker Analytics Platform.

K nakonfigurování a testování jednotného přihlašování Azure AD s Looker Analytics platformou proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Looker Analytics Platform](#configure-looker-analytics-platform-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele platformy Looker Analytics](#create-looker-analytics-platform-test-user)** , abyste měli protějšek B. Simon v platformě Looker Analytics, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **platformy Looker Analytics** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<SPN>_looker`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.looker.com/samlcallback`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.looker.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory Looker Analytics Platform Client](mailto:support@looker.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení platformy Looker Analytics** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k platformě Looker Analytics.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Looker Analytics Platform**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-looker-analytics-platform-sso"></a>Konfigurace jednotného přihlašování Looker Analytics Platform

1. V jiném okně webového prohlížeče se přihlaste k webu Looker Analytics Platform jako správce.

1. Přejít na protokol   ->    ->  **SAML** pro ověřování správců

    ![snímek obrazovky s možností SAML](./media/looker-analytics-platform-tutorial/admin.png)

1. Vložte informace o **federačních metadatech** , které jste zkopírovali z Azure Portal do textového pole **metadat IDP** a klikněte na **načíst**.

    ![snímek obrazovky pro nahrání metadat](./media/looker-analytics-platform-tutorial/metadata.png)

1. V části **nastavení atributu uživatele** proveďte následující kroky.

    ![snímek obrazovky s nastavením atributu uživatele](./media/looker-analytics-platform-tutorial/user-attribute-settings.png)

    a. Do pole ATTR e-mailu přidejte následující hodnotu: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    b. Do pole fname ATTR přidejte následující hodnotu: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    c. Do pole LName ATTR přidejte následující hodnotu: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    d. V **ověřování uživatele otestujte** kliknutím na **test ověřování SAML**. Pokud se stránka, která načte říká "odpověď serveru úspěšně ověřila", úspěšně nastavila instanci pro integraci SAML.
    
    e. V části **Uložit a použít nastavení** zaškrtněte políčko **potvrzuji konfiguraci výše a chcete povolit její použití globálně**.

    f. Klikněte na tlačítko **aktualizovat nastavení** .

### <a name="create-looker-analytics-platform-test-user"></a>Vytvořit testovacího uživatele platformy Looker Analytics

V této části se na platformě Looker Analytics vytvoří uživatel s názvem Britta Simon. Looker Analytics Platform podporuje zřizování uživatelů za běhu, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě na platformě Looker Analytics neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k platformě Looker Analytics, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro Looker Analytics Platform pro přímý zápis a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k platformě Looker Analytics, pro kterou jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít panel Microsoft Access. Když kliknete na dlaždici Looker Analytics Platform na přístupovém panelu, pokud se nakonfiguruje v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k platformě Looker Analytics, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete platformu Looker Analytics, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).