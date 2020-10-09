---
title: 'Kurz: Azure Active Directory integrace se SAP Qualtrics | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Qualtrics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 170997099f1194bbc75d6d61a7c29fc1d18b462a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552210"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí SAP Qualtrics

V tomto kurzu se dozvíte, jak integrovat SAP Qualtrics s Azure Active Directory (Azure AD). Když integrujete SAP Qualtrics s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SAP Qualtrics.
* Umožněte uživatelům, aby se do SAP Qualtrics automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Na začátek budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné SAP Qualtrics je povolené pro jednotné přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAP Qualtrics podporuje jednotné přihlašování (SSO) pro **aktualizace SP** a **IDP** .
* SAP Qualtrics podporuje zřizování uživatelů **jenom v čase** .
* Po nakonfigurování SAP Qualtrics můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. Další informace najdete v tématu [vymáhání řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-sap-qualtrics-from-the-gallery"></a>Přidání SAP Qualtrics z Galerie

Pokud chcete nakonfigurovat integraci SAP Qualtrics do služby Azure AD, musíte přidat SAP Qualtrics z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **SAP Qualtrics** .
1. Z výsledků vyberte **SAP Qualtrics** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SAP Qualtrics

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SAP Qualtrics pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v SAP Qualtrics.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SAP Qualtrics, dokončete následující stavební bloky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) .
1. [NAKONFIGURUJTE SAP QUALTRICS SSO](#configure-sap-qualtrics-sso) pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte testovacího uživatele SAP Qualtrics](#create-sap-qualtrics-test-user) , který bude mít protějšek B. Simon v SAP Qualtrics, který je propojený s Předprezentací Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **SAP Qualtrics** najděte část **Správa** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky nastavení jednoho Sign-On se stránkou SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu **IDPed** , zadejte na stránce **nastavení jednotného přihlašování v SAML** hodnoty následujících polí:
    
    a. Do textového pole **identifikátor** zadejte adresu URL, která používá následující vzor:

    `https://< DATACENTER >.qualtrics.com`
   
    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, která používá následující vzor:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Do textového pole **stav přenosu** zadejte adresu URL, která používá následující vzor:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Vyberte **nastavit další adresy URL**a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL, která používá následující vzor:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru, adresy URL odpovědi a stavu přenosu. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta Qualtrics](https://www.qualtrics.com/support/). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyberte ikonu kopírování a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Snímek pro podpisový certifikát SAML se zvýrazněnou ikonou kopírování](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k SAP Qualtrics.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **SAP Qualtrics**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky oddílu Správa se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Snímek stránky uživatelů a skupin se zvýrazněným možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **B. Simon** . Pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-sap-qualtrics-sso"></a>Konfigurace protokolu SAP Qualtrics SSO

Pokud chcete nakonfigurovat jednotné přihlašování na straně SAP Qualtrics, odešlete zkopírovanou **adresu URL federačních metadat aplikace** z Azure Portal do [týmu podpory SAP Qualtrics](https://www.qualtrics.com/support/). Tým podpory zajišťuje správné nastavení připojení SAML SSO na obou stranách.

### <a name="create-sap-qualtrics-test-user"></a>Vytvořit testovacího uživatele SAP Qualtrics

SAP Qualtrics podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. Neexistují žádné další akce, které byste mohli provést. Pokud uživatel ještě v SAP Qualtrics neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici SAP Qualtrics, budete automaticky přihlášeni ke SAP Qualtrics, pro který jste nastavili jednotné přihlašování. Další informace najdete v tématu [přihlášení a spouštění aplikací na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte SAP Qualtrics s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Ochrana SAP Qualtrics s pokročilou viditelností a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

