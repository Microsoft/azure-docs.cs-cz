---
title: 'Kurz: Azure Active Directory integrace s TurboRater | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TurboRater.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 7483e7a9300088d07e85d238ad897522d59c5464
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519380"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Kurz: Azure Active Directory integrace s TurboRater

V tomto kurzu se dozvíte, jak integrovat TurboRater s Azure Active Directory (Azure AD).

Integrace TurboRater s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k TurboRater.
* Uživatelům můžete povolit, aby se automaticky přihlásili k TurboRater (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Podrobnosti o integraci aplikací SaaS (software jako služba) se službou Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s TurboRater potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné TurboRater s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

TurboRater podporuje jednotné přihlašování (SSO) iniciované IDP.

## <a name="add-turborater-from-the-azure-marketplace"></a>Přidat TurboRater z Azure Marketplace

Pokud chcete nakonfigurovat integraci TurboRater do služby Azure AD, musíte přidat TurboRater z Azure Marketplace do seznamu spravovaných aplikací SaaS:

1. Přihlaste se na [Azure Portal](https://portal.azure.com?azure-portal=true).
1. V levém podokně vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Možnost Enterprise Applications](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, v horní části podokna vyberte **+ Nová aplikace** .

    ![Možnost nové aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **TurboRater**. Ve výsledcích hledání vyberte **TurboRater**a pak vyberte **Přidat** , aby se aplikace přidala.

    ![TurboRater v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TurboRater na základě testovacího uživatele s názvem  **B Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TurboRater.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TurboRater, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , aby mohli vaši uživatelé používat tuto funkci.
1. **[Nakonfigurujte jednotné přihlašování TurboRater](#configure-turborater-single-sign-on)** ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[Vytvořte testovacího uživatele TurboRater](#create-a-turborater-test-user)** , aby byl uživatel s názvem b. Simon v TurboRater, který je propojený s uživatelem služby Azure AD s názvem b. Simon.
1. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí TurboRater, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **TurboRater** vyberte **jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlašování](common/select-sso.png)

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. Na stránce **nastavit jeden Sign-On s SAML** vyberte **Upravit** (ikona tužky) a otevřete **základní podokno konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně TurboRater a adresách URL](common/idp-intiated.png)

    1. Do pole **identifikátor (ID entity)** zadejte adresu URL:

       `https://www.itcdataservices.com`

    1. Do pole **Adresa URL odpovědi (adresa URL služby vyhodnocení zákazníka)** zadejte adresu URL pomocí následujícího vzoru:

       | Prostředí | URL |
       | ---------------| --------------- |
       | Test  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Živě  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory TurboRater](https://www.getitc.com/support). Můžete také odkazovat na vzory zobrazené v podokně **základní konfigurace SAML** v Azure Portal.

1. V podokně **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** a ze daných možností stáhněte **XML federačních metadat** a uložte ho do svého počítače.

    ![Možnost stažení XML federačních metadat](common/metadataxml.png)

1. V části **Nastavení TurboRater** zkopírujte adresy URL nebo adresy URL, které potřebujete:

   * **Přihlašovací adresa URL**
   * **Identifikátor Azure AD**
   * **Odhlašovací adresa URL**

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Konfigurace jednotného přihlašování TurboRater

Ke konfiguraci jednotného přihlašování na straně TurboRater je potřeba odeslat stažený soubor XML federačních metadat a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory TurboRater](https://www.getitc.com/support). Tým TurboRater zajistí, že připojení SAML SSO je na obou stranách správně nastavené.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory**    >  **Uživatelé**  >  **Všichni uživatelé**.

    ![Možnosti uživatelé a všichni uživatelé](common/users.png)

1. V horní části obrazovky vyberte **+ Nový uživatel**.

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **uživatel** proveďte následující kroky:

    ![Podokno uživatele](common/user-properties.png)

    1. Do pole **název** zadejte **BSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **BSimon \@ \<yourcompanydomain> . \<extension> **. Například **BSimon \@ contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k TurboRater.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  >  **TurboRater**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **TurboRater**.

    ![TurboRater v seznamu aplikací](common/all-applications.png)

1. V levém podokně v části **Spravovat**vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **+ Přidat uživatele**a pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak zvolte **Vybrat** v dolní části podokna.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. V dolní části podokna zvolte **možnost vybrat**.

1. V podokně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-turborater-test-user"></a>Vytvořit testovacího uživatele v TurboRater

V této části vytvoříte uživatele s názvem B. Simon v TurboRater. Pracujte s [týmem podpory TurboRater](https://www.getitc.com/support) a přidejte B. Simon jako uživatele v TurboRater. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Když vyberete **TurboRater** na portálu moje aplikace, měli byste být automaticky přihlášeni k předplatnému TurboRater, pro které jste nastavili jednotné přihlašování. Další informace o portálu moje aplikace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

* [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)