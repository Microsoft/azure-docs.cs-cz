---
title: 'Kurz: Integrace Azure Active Directory s TurboRater | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TurboRater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 3777cf09ec669fe3df6bca13f6960f53c689767c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088278"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Kurz: Integrace Azure Active Directory s TurboRater

V tomto kurzu se dozvíte, jak integrovat TurboRater s Azure Active Directory (Azure AD).

TurboRater integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TurboRater.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k TurboRater (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Podrobnosti o softwaru, integraci služby (SaaS) aplikací s Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TurboRater, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Předplatné TurboRater pomocí jednotného přihlašování povolená.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

TurboRater podporuje zahájené pomocí IDP jednotné přihlašování (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Přidat TurboRater na webu Azure Marketplace

Konfigurace integrace TurboRater do služby Azure AD, budete muset přidat TurboRater z Azure Marketplace na váš seznam spravovaných aplikací SaaS:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com?azure-portal=true).
1. V levém podokně vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Možnost aplikace organizace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, **+ nová aplikace** v horní části podokna.

    ![Nová možnost aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **TurboRater**. Ve výsledcích hledání vyberte **TurboRater**a pak vyberte **přidat** pro přidání aplikace.

    ![TurboRater v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s TurboRater podle testovacího uživatele s názvem **B Simon**. Pro jednotné přihlašování pro práci je potřeba vytvořit propojení mezi uživatele služby Azure AD a související uživatel v TurboRater.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TurboRater, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  aby uživatelé mohli tuto funkci používat.
1. **[Konfigurace TurboRater jednotného přihlašování](#configure-turborater-single-sign-on)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B. Simon.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B. Simon používat Azure AD jednotného přihlašování.
1. **[Vytvoření zkušebního uživatele TurboRater](#create-a-turborater-test-user)**  tak, aby se uživatel s názvem Simon B. v TurboRater, který je spojen s uživatele Azure AD s názvem B. Simon.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s TurboRater, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **TurboRater** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurujte možnost přihlašování](common/select-sso.png)

1. Na **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránce **upravit** (ikonu tužky) Chcete-li otevřít **základní konfiguraci SAML** podokně.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. V **základní konfiguraci SAML** podokno, proveďte následující kroky:

    ![TurboRater domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    1. V **identifikátor (Entity ID)** pole, zadejte adresu URL:

       `https://www.itcdataservices.com`

    1. V **adresy URL odpovědi (adresa URL služby příjemce kontrolního výrazu)** pole, zadejte adresu URL pomocí následujícího vzorce:

       | Prostředí | zprostředkovatele identity |
       | ---------------| --------------- |
       | Test  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Živé  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. K získání těchto hodnot, obraťte se [tým podpory TurboRater](https://www.getitc.com/support). Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** podokně webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** podokno v **podpisový certifikát SAML** vyberte **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi a uložit je ve vašem počítači.

    ![Možnost stažení kód XML metadat federace](common/metadataxml.png)

1. V **nastavení TurboRater** tématu, zkopírujte adresu URL nebo adresy URL, které budete potřebovat:

   * **Adresa URL pro přihlášení**
   * **Azure AD Identifier**
   * **Odhlašovací adresa URL**

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Konfigurace TurboRater jednotného přihlašování

Konfigurace jednotného přihlašování na straně TurboRater, je nutné odeslat stažené kód XML metadat federace a příslušné adresy URL zkopírovanou ze na webu Azure portal [tým podpory TurboRater](https://www.getitc.com/support). Tým TurboRater zajistit, aby že SAML SSO připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**   > **uživatelé** > **všichni uživatelé**.

    ![Uživatele a "Všechny uživatele" možnosti](common/users.png)

1. V horní části obrazovky vyberte **+ nový uživatel**.

    ![Nová možnost uživatele](common/new-user.png)

1. V **uživatele** podokno, proveďte následující kroky:

    ![V podokně uživatele](common/user-properties.png)

    1. V **název** zadejte **BSimon**.
  
    1. V **uživatelské jméno** zadejte **BSimon\@\<doména_společnosti >.\< Rozšíření >** . Například **BSimon\@contoso.com**.

    1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování Azure tím, že udělíte přístup k TurboRater.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **TurboRater**.

    ![Podokno podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **TurboRater**.

    ![TurboRater v seznamu aplikací](common/all-applications.png)

1. V levém podokně v části **SPRAVOVAT**vyberte **uživatelů a skupin**.

    ![Možnost "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **+ přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** podokně.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V **uživatelů a skupin** vyberte **B. Simon** v **uživatelé** seznamu a klikněte na tlačítko **vyberte** v dolní části podokna.

1. Pokud očekáváte hodnotu do role kontrolní výraz SAML, pak v **vybrat roli** podokně, vyberte vhodnou roli pro uživatele ze seznamu. V dolní části podokna, zvolte **vyberte**.

1. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-turborater-test-user"></a>Vytvoření zkušebního uživatele TurboRater

V této části vytvořte uživatele B. Simon v TurboRater. Práce s [tým podpory TurboRater](https://www.getitc.com/support) přidání B. Simon jako uživatele v TurboRater. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu Moje aplikace.

Když vyberete **TurboRater** na portálu Moje aplikace vám by měl být automaticky přihlášeni k TurboRater předplatného, u kterého nastavíte jednotné přihlašování. Další informace o portálu Moje aplikace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
