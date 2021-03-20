---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí centra Planneru | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a centrem plánování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2020
ms.author: jeedes
ms.openlocfilehash: 2d9dc483a9d60dc395c0aff52b721690d4fc701d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442401"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hub-planner"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí centra Planneru

V tomto kurzu se dozvíte, jak integrovat centrum plánovače s Azure Active Directory (Azure AD). Když integrujete centrum plánovače s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k centru Planner.
* Umožněte uživatelům, aby se automaticky přihlásili k centru Planner pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné centra plánovače jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Centrum plánovače podporuje jednotné přihlašování iniciované v **SP** .
* Po konfiguraci plánovače centra můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-hub-planner-from-the-gallery"></a>Přidání plánovače centra z Galerie

Pokud chcete nakonfigurovat integraci centra plánovače do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat centra Planneru z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **centrum Planner** .
1. Z panelu výsledků vyberte **Plánovač centra** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-hub-planner"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Plánovač centra

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí centra Planner pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v centru Planner.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí centra Planneru, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování centra Planneru](#configure-hub-planner-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření testovacího uživatele plánovače centra](#create-hub-planner-test-user)** – Pokud chcete mít protějšek B. Simon v centru plánovače, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **plánovače centra** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.hubplanner.com`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://app.hubplanner.com/sso/metadata`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://app.hubplanner.com/sso/callback`

    > [!NOTE]
    > Tyto hodnoty jsou ty, které budete používat. Jediná změna, kterou potřebujete udělat, je nahradit \<SUBDOMAIN\> v **přihlašovací adrese URL** poddoménou, kterou jste dostali při registraci k plánovači centra. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení plánovače centra** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k centru Planner.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **centrum Planner**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-hub-planner-sso"></a>Konfigurace jednotného přihlašování centra Planneru

Pokud chcete nakonfigurovat jednotné přihlašování na straně **plánovače** , musíte se přihlásit ke svému účtu Planneru centra a dokončit následující úlohy. 

### <a name="install-the-extension-in-hub-planner"></a>Instalace rozšíření v centru Planner

Pokud chcete povolit funkci jednotného přihlašování, musíte nejdřív povolit rozšíření. Jako vlastník účtu nebo s ekvivalentními oprávněními proveďte tyto kroky:

1. Přejít na **Nastavení**.
1. V postranní nabídce vyberte **Spravovat rozšíření**  >  **Přidat nebo odebrat rozšíření**.
1. Najděte rozšíření pro jednotné přihlašování a přidejte nebo Vyzkoušejte zdarma.
1. Po zobrazení výzvy vyjádřete souhlas s podmínkami a ujednáními a pak vyberte **Přidat nyní**.

### <a name="enable-sso"></a>Povolení jednotného přihlašování

Po povolení rozšíření musíte pro svůj účet povolit jednotné přihlašování. 

1. Přejít na **Nastavení**.
1. V postranní nabídce vyberte **ověřování**.
1. Vyberte **jednotné přihlašování (jednotné přihlašování)**.
1. Zadejte další ověřovací informace, jak je znázorněno na následujícím obrázku, a pak vyberte **Uložit**.

![Snímek obrazovky s nastavením jednotného přihlašování](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>Vytvořit testovacího uživatele plánovače centra

Pokud chcete přidat další uživatele, přečtěte si **Nastavení**  >  **Správa prostředků** a přidejte uživatele odsud. Ujistěte se, že jste přidali e-mailovou adresu a pozvali je. Po pozvání obdrží e-mail a bude možné ho zadat pomocí jednotného přihlašování. 

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici centra plánovače na přístupovém panelu byste měli být automaticky přihlášeni k plánovači centra, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si centrum Planner s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Postup ochrany centra plánovače s pokročilou viditelností a ovládacími prvky](/cloud-app-security/proxy-intro-aad)