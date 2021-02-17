---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SurveyMonkey Enterprise | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SurveyMonkey Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: fe5f53d2d00b5bceb2298e1aba837e65b1f2927c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555318"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-surveymonkey-enterprise"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SurveyMonkey Enterprise

V tomto kurzu se dozvíte, jak integrovat SurveyMonkey Enterprise s Azure Active Directory (Azure AD). Když integrujete SurveyMonkey Enterprise s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SurveyMonkey Enterprise.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SurveyMonkey Enterprise pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné SurveyMonkey Enterprise s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SurveyMonkey Enterprise podporuje **IDP** iniciované jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-surveymonkey-enterprise-from-the-gallery"></a>Přidání SurveyMonkey Enterprise z Galerie

Pokud chcete nakonfigurovat integraci SurveyMonkey Enterprise do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat SurveyMonkey Enterprise z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SurveyMonkey Enterprise** .
1. Z panelu výsledků vyberte **SurveyMonkey Enterprise** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-surveymonkey-enterprise"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SurveyMonkey Enterprise

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s SurveyMonkey Enterprise pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SurveyMonkey Enterprise.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SurveyMonkey Enterprise, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SurveyMonkey Enterprise SSO](#configure-surveymonkey-enterprise-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte SurveyMonkey Enterprise Test User](#create-surveymonkey-enterprise-test-user)** – abyste měli protějšek B. Simon v SurveyMonkey Enterprise, který se odkazuje na reprezentaci uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace **podnikových aplikací SurveyMonkey** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. SurveyMonkey Enterprise Application očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

6. Kromě toho očekává SurveyMonkey podniková aplikace několik atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut|
    | ---------------| --------------- |
    | E-mail | uživatel. pošta |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení organizace SurveyMonkey Enterprise** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SurveyMonkey Enterprise.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SurveyMonkey Enterprise**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-surveymonkey-enterprise-sso"></a>Konfigurace SurveyMonkey Enterprise SSO

Ke konfiguraci jednotného přihlašování na straně **SurveyMonkey Enterprise** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu SurveyMonkey Enterprise support](mailto:support@selerix.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-surveymonkey-enterprise-test-user"></a>Vytvořit SurveyMonkey Enterprise Test User

V SurveyMonkey Enterprise není nutné vytvářet testovacího uživatele. Uživatelské účty se zřídí, pokud se uživatel rozhodne vytvořit nový účet na základě kontrolního výrazu SAML. Váš SurveyMonkey Enterprise Customer Success vám poskytne kroky k dokončení tohoto procesu po přidání metadat Azure do konfigurace SurveyMonkey Enterprise a je připravená k ověření.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k SurveyMonkey podniku, pro který jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SurveyMonkey Enterprise v okně moje aplikace, měli byste se automaticky přihlásit k SurveyMonkey podniku, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování SurveyMonkey Enterprise můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).