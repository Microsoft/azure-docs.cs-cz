---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím nástroje SDS & chemických informací | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SDS & správu chemických informací.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: e83274c404980549063b9a94a9118540f1ec3bb3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509322"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sds--chemical-information-management"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím nástroje SDS & Chemical Information Management

V tomto kurzu se dozvíte, jak integrovat SDS & správu chemických informací pomocí Azure Active Directory (Azure AD). Když integrujete SDS & chemická Správa informací pomocí Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SDS & správě chemických informací.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SDS & správy chemických informací pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SDS & odběr s povoleným jednotným přihlašováním (SSO) pro správu chemických informací.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SDS & Chemical Information Management podporuje jednotné přihlašování spouštěné v **SP** .

* SDS & Chemical Information Management podporuje **jenom v čase** zřizování uživatelů.


## <a name="adding-sds--chemical-information-management-from-the-gallery"></a>Přidání SDS & správy chemických informací z Galerie

Pokud chcete nakonfigurovat integraci SDS & správy chemických informací do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat SDS & Správa chemických informací z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SDS & Chemical Information Management** .
1. Na panelu výsledků vyberte **SDS & chemická Správa informací** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-sds--chemical-information-management"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro SDS & správy chemických informací

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SDS & Správa chemických informací pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SDS & chemických informacích o správě.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SDS & Správa chemických informací, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE SDS & jednotné PŘIhlašování pro správu chemických informací](#configure-sds--chemical-information-management-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte SDS & testovacího uživatele pro správu chemických informací](#create-sds--chemical-information-management-test-user)** – Pokud chcete mít protějšek B. Simon v SDS & správě chemických informací, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikace **SDS & Chemical Information Management** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://cs.cloudsds.com/saml/<ID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://cs.cloudsds.com/saml/<ID>/consumeAssertion`
    
    c. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://cs.cloudsds.com/saml/<ID>/Login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory SDS & Chemical Information Management pro klienty](mailto:info@cloudsds.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k SDS & správy chemických informací.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SDS & Správa chemických informací**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sds--chemical-information-management-sso"></a>Konfigurace SDS & jednotného přihlašování pro Chemical Information Management

Pokud chcete nakonfigurovat jednotné přihlašování na **SDS &i na straně správy chemických informací** , musíte poslat **adresu URL federačních metadat aplikace** [& SDS týmu podpory správy chemických informací](mailto:info@cloudsds.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-sds--chemical-information-management-test-user"></a>Vytvoření SDS & pro testovacího uživatele pro správu chemických informací

V této části se uživatel s názvem Britta Simon vytvoří v SDS & chemických informacích o správě. SDS & Chemical Information Management podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě neexistuje v SDS & chemická Správa informací, vytvoří se nový, který se vytvoří po ověření.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na SDS & přihlašovací adresa URL pro správu chemických informací, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlašování k SDS & Chemical Information Management přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SDS & chemická Správa informací v nabídce Moje aplikace, přesměruje se na SDS & se přihlašovací adresa URL pro správu chemických informací. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete SDS & chemická Správa informací, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


