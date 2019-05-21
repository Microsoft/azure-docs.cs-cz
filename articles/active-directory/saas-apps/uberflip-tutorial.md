---
title: 'Kurz: Integrace Azure Active Directory s Uberflip | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 55fa42eb1c35025b1008b3b5d26f61c243ff2b97
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956664"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Kurz: Integrace Azure Active Directory s Uberflip

V tomto kurzu se dozvíte, jak integrovat Uberflip s Azure Active Directory (Azure AD).

Uberflip integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Uberflip.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Uberflip (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Podrobnosti o softwaru, integraci služby (SaaS) aplikací s Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Uberflip, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Předplatné Uberflip pomocí jednotného přihlašování povolená.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

Uberflip podporuje následující funkce:

* Iniciovaného Zprostředkovatelem přihlašování a zahájené pomocí IDP jednotné přihlašování (SSO).
* Zřizování uživatelů just-in-time.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Přidat Uberflip na webu Azure Marketplace

Konfigurace integrace Uberflip do služby Azure AD, budete muset přidat Uberflip z Azure Marketplace na váš seznam spravovaných aplikací SaaS:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levém podokně vyberte **Azure Active Directory**.

   ![Možnost Azure Active Directory](common/select-azuread.png)

1. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

   ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, **+ nová aplikace** v horní části podokna.

   ![Nová možnost aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Uberflip**. Ve výsledcích hledání vyberte **Uberflip**a pak vyberte **přidat** pro přidání aplikace.

   ![Uberflip v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Uberflip podle testovacího uživatele s názvem **B Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatelské v Uberflip.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Uberflip, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  aby uživatelé mohli tuto funkci používat.
1. **[Konfigurace Uberflip jednotného přihlašování](#configure-uberflip-single-sign-on)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B. Simon.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B. Simon používat Azure AD jednotného přihlašování.
1. **[Vytvořit testovacího uživatele Uberflip](#create-an-uberflip-test-user)**  tak, aby se uživatel s názvem Simon B. v Uberflip, který je spojen s uživatele Azure AD s názvem B. Simon.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Uberflip, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Uberflip** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurujte možnost přihlašování](common/select-sso.png)

1. V **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

1. Na **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit** (ikonu tužky) Chcete-li otevřít **základní konfiguraci SAML** podokně.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** podokno, proveďte jednu z následujících kroků, v závislosti na režimu jednotného přihlašování, který chcete nakonfigurovat:

   * Konfigurace aplikace v režimu jednotného přihlašování zahájené pomocí IDP v **adresy URL odpovědi (adresa URL služby příjemce kontrolního výrazu)** pole, zadejte adresu URL pomocí následujícího vzorce:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip domény a adresy URL jednotného přihlašování – informace](common/both-replyurl.png)

     > [!NOTE]
     > Tato hodnota není skutečný. Aktualizujte tuto hodnotu adresy URL skutečné odpovědi. Pokud chcete získat skutečnou hodnotu, obraťte se [tým podpory Uberflip](mailto:support@uberflip.com). Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** podokně webu Azure Portal.

   * Konfigurace aplikace v režimu iniciovaného Zprostředkovatelem přihlašování jednotného přihlašování, vyberte **nastavit další adresy URL**a **přihlašovací adresa URL** zadejte tuto adresu URL:

     `https://app.uberflip.com/users/login`

     ![Uberflip domény a adresy URL jednotného přihlašování – informace](common/both-signonurl.png)

1. Na **nastavte si jednotné přihlašování pomocí SAML** podokno v **podpisový certifikát SAML** vyberte **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi a uložit je ve vašem počítači.

   ![Možnost stažení kód XML metadat federace](common/metadataxml.png)

1. V **nastavení Uberflip** podokně zkopírujte adresu URL nebo adresy URL, které potřebujete:

   * **Adresa URL pro přihlášení**
   * **Azure AD Identifier**
   * **Odhlašovací adresa URL**

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurace Uberflip jednotného přihlašování

Konfigurace jednotného přihlašování na straně Uberflip, je nutné odeslat stažené kód XML metadat federace a příslušné adresy URL zkopírovanou ze na webu Azure portal [tým podpory Uberflip](mailto:support@uberflip.com). Tým Uberflip zajistit, aby že SAML SSO připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon na webu Azure Portal.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.

    ![Uživatele a "Všechny uživatele" možnosti](common/users.png)

1. V horní části obrazovky vyberte **+ nový uživatel**.

    ![Nová možnost uživatele](common/new-user.png)

1. V **uživatele** podokno, proveďte následující kroky:

    ![V podokně uživatele](common/user-properties.png)

    1. V **název** zadejte **BSimon**.
  
    1. V **uživatelské jméno** zadejte **BSimon\@\<doména_společnosti >.\< Rozšíření >**. Například **BSimon\@contoso.com**.

    1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Uberflip.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **Uberflip**.

    ![Podokno podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **Uberflip**.

    ![Uberflip v seznamu aplikací](common/all-applications.png)

1. V levém podokně v části **SPRAVOVAT**vyberte **uživatelů a skupin**.

    ![Možnost "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **+ přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** podokně.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V **uživatelů a skupin** vyberte **B Simon** v **uživatelé** seznamu a klikněte na tlačítko **vyberte** v dolní části podokna.

1. Pokud očekáváte hodnotu do role kontrolní výraz SAML, pak v **vybrat roli** podokně, vyberte vhodnou roli pro uživatele ze seznamu. V dolní části podokna, zvolte **vyberte**.

1. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-an-uberflip-test-user"></a>Vytvořit testovacího uživatele Uberflip

Uživatel se jménem B. Simon je nyní v Uberflip vytvořili. Nemusíte dělat nic k vytvoření tohoto uživatele. Uberflip podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Pokud uživatele s názvem B. Simon v Uberflip ještě neexistuje, vytvoří se nový po ověření.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se [tým podpory Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu Moje aplikace.

Když vyberete **Uberflip** na portálu Moje aplikace vám by měl být automaticky přihlášeni k Uberflip předplatného, u kterého nastavíte jednotné přihlašování. Další informace o portálu Moje aplikace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
