---
title: 'Kurz: Integrace Azure Active Directory s PageDNA | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227468"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Kurz: Integrace Azure Active Directory s PageDNA

V tomto kurzu se dozvíte, jak integrovat PageDNA s Azure Active Directory (Azure AD).

PageDNA integraci se službou Azure AD poskytuje následující výhody:

* Ve službě Azure AD můžete řídit, kdo má přístup k PageDNA.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k PageDNA (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Podrobnosti o softwaru, integraci služby (SaaS) aplikací s Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s PageDNA, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Předplatné PageDNA pomocí jednotného přihlašování povolená.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování a PageDNA integrace s Azure AD.

PageDNA podporuje následující funkce:

* Iniciovaného Zprostředkovatelem přihlašování jednotné přihlašování (SSO).

* Zřizování uživatelů just-in-time.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Přidat PageDNA na webu Azure Marketplace

Konfigurace integrace PageDNA do služby Azure AD, budete muset přidat PageDNA z Azure Marketplace na váš seznam spravovaných aplikací SaaS:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com?azure-portal=true).
1. V levém podokně vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, **+ nová aplikace** v horní části podokna.

    ![Nová možnost aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **PageDNA**. Ve výsledcích hledání vyberte **PageDNA**a pak vyberte **přidat** pro přidání aplikace.

    ![PageDNA v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s PageDNA podle testovacího uživatele s názvem **Britta Simon**. Pro jednotné přihlašování pro práci je potřeba vytvořit propojení mezi uživatele služby Azure AD a související uživatel v PageDNA.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s PageDNA, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  aby uživatelé mohli tuto funkci používat.
1. **[Konfigurace PageDNA jednotného přihlašování](#configure-pagedna-single-sign-on)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující Britta Simon používat Azure AD jednotného přihlašování.
1. **[Vytvoření zkušebního uživatele PageDNA](#create-a-pagedna-test-user)**  tak, aby se uživatel s názvem Britta Simon v PageDNA, který je spojen s uživatele Azure AD s názvem Britta Simon.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s PageDNA, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **PageDNA** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurujte možnost přihlašování](common/select-sso.png)

1. V **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

1. Na **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit** (ikonu tužky) Chcete-li otevřít **základní konfiguraci SAML** podokně.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. V **základní konfiguraci SAML** podokno, proveďte následující kroky:

    ![PageDNA domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    1. V **přihlašovací adresa URL** pole, zadejte adresu URL pomocí jedné z následujících vzorů:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. V **identifikátor (Entity ID)** pole, zadejte adresu URL pomocí jedné z následujících vzorů:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. K získání těchto hodnot, obraťte se [tým podpory PageDNA](mailto:success@pagedna.com). Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** podokně webu Azure Portal.

1. V **nastavte si jednotné přihlašování pomocí SAML** podokno v **podpisový certifikát SAML** vyberte **Stáhnout** Stáhnout **certifikát (Raw)** z se zadanými možnostmi a uložit je ve vašem počítači.

    ![Certifikát (nekódovaný) možnost](common/certificateraw.png)

1. V **nastavení PageDNA** tématu, zkopírujte adresu URL nebo adresy URL, které budete potřebovat:

   * **Adresa URL pro přihlášení**
   * **Identifikátor služby Azure AD**
   * **Odhlašovací adresa URL**

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Konfigurace PageDNA jednotného přihlašování

Konfigurace jednotného přihlašování na straně PageDNA, odeslat staženého certifikátu (Raw) a odpovídající zkopírovaný adresy URL z webu Azure portal [tým podpory PageDNA](mailto:success@pagedna.com). Tým PageDNA zajistit, aby že SAML SSO připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal s názvem Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**   > **uživatelé** > **všichni uživatelé**.

    ![Uživatele a "Všechny uživatele" možnosti](common/users.png)

1. V horní části obrazovky vyberte **+ nový uživatel**.

    ![Nová možnost uživatele](common/new-user.png)

1. V **uživatele** podokno, proveďte následující kroky:

    ![V podokně uživatele](common/user-properties.png)

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **BrittaSimon\@\<doména_společnosti >.\< Rozšíření >** . Například **BrittaSimon\@contoso.com**.

    1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup uživatelů k PageDNA.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **PageDNA**.

    ![Podokno podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **PageDNA**.

    ![PageDNA v seznamu aplikací](common/all-applications.png)

1. V levém podokně v části **SPRAVOVAT**vyberte **uživatelů a skupin**.

    ![Možnost "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **+ přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** podokně.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V **uživatelů a skupin** vyberte **Britta Simon** v **uživatelé** seznamu a klikněte na tlačítko **vyberte** v dolní části podokna.

1. Pokud očekáváte hodnotu do role kontrolní výraz SAML, pak v **vybrat roli** podokně, vyberte vhodnou roli pro uživatele ze seznamu. V dolní části podokna, zvolte **vyberte**.

1. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-pagedna-test-user"></a>Vytvoření zkušebního uživatele PageDNA

Uživatel se jménem Britta Simon se teď vytvoří v PageDNA. Nemusíte dělat nic k vytvoření tohoto uživatele. PageDNA podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Pokud uživatel se jménem Britta Simon již neexistuje mezi PageDNA, vytvoří se nový po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu Moje aplikace.

Když vyberete **PageDNA** na portálu Moje aplikace vám by měl být automaticky přihlášeni k PageDNA předplatného, u kterého nastavíte jednotné přihlašování. Další informace o portálu Moje aplikace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

