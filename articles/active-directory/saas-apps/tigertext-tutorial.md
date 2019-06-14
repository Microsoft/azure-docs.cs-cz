---
title: 'Kurz: Integrace Azure Active Directory s TigerText zabezpečení Messenger | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TigerText zabezpečení Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088660"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Kurz: Integrace Azure Active Directory s TigerText zabezpečení Messenger

V tomto kurzu se dozvíte, jak integrovat Messenger TigerText zabezpečení Azure Active Directory (Azure AD).

Integrace zabezpečení Messenger TigerText s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke službě Messenger zabezpečení TigerText.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k TigerText zabezpečení Messenger (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Podrobnosti o softwaru, integraci služby (SaaS) aplikací s Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TigerText zabezpečení Messenger, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Předplatné TigerText zabezpečení Messenger pomocí jednotného přihlašování povolená.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu se konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování a zabezpečení Messenger TigerText integrace s Azure AD.

Zabezpečení Messenger TigerText podporuje iniciovaného Zprostředkovatelem přihlašování jednotné přihlašování (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Přidejte zabezpečenou Messenger TigerText na webu Azure Marketplace

Ke konfiguraci integrace Messenger TigerText zabezpečení do služby Azure AD, budete muset přidat Messenger TigerText zabezpečení z Azure Marketplace na váš seznam spravovaných aplikací SaaS:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com?azure-portal=true).
1. V levém podokně vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, **+ nová aplikace** v horní části podokna.

    ![Nová možnost aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **TigerText zabezpečení Messenger**. Ve výsledcích hledání vyberte **TigerText zabezpečení Messenger**a pak vyberte **přidat** pro přidání aplikace.

    ![TigerText zabezpečení Messenger v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Messenger TigerText zabezpečení na základě testovací uživatele s názvem **Britta Simon**. Pro jednotné přihlašování pro práci je potřeba vytvořit propojení mezi uživatele služby Azure AD a související uživatelské TigerText zabezpečení Messenger.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Messenger TigerText zabezpečení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  aby uživatelé mohli tuto funkci používat.
1. **[Konfigurace zabezpečení Messenger TigerText jednotného přihlašování](#configure-tigertext-secure-messenger-single-sign-on)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující Britta Simon používat Azure AD jednotného přihlašování.
1. **[Vytvoření zkušebního uživatele TigerText zabezpečení Messenger](#create-a-tigertext-secure-messenger-test-user)**  tak, aby se uživatel s názvem Britta Simon TigerText zabezpečení Messenger, který je spojen s uživatele Azure AD s názvem Britta Simon.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s TigerText zabezpečení Messenger, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **TigerText zabezpečení Messenger** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurujte možnost přihlašování](common/select-sso.png)

1. Na **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

1. Na **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit** (ikonu tužky) Chcete-li otevřít **základní konfiguraci SAML** podokně.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. V **základní konfiguraci SAML** podokno, proveďte následující kroky:

    ![Messenger TigerText zabezpečení domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    1. V **přihlašovací adresa URL** pole, zadejte adresu URL:

       `https://home.tigertext.com`

    1. V **identifikátor (Entity ID)** pole, zadejte adresu URL pomocí následujícího vzorce:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > **Identifikátor (Entity ID)** není skutečnou hodnotu. Aktualizujte tuto hodnotu skutečné identifikátor. K získání hodnoty, obraťte se [tým podpory zabezpečení Messenger TigerText](mailto:prosupport@tigertext.com). Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** podokně webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** podokno v **podpisový certifikát SAML** vyberte **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi a uložit je ve vašem počítači.

    ![Možnost stažení kód XML metadat federace](common/metadataxml.png)

1. V **nastavení zabezpečení Messenger TigerText** tématu, zkopírujte adresu URL nebo adresy URL, které budete potřebovat:

   * **Adresa URL pro přihlášení**
   * **Azure AD Identifier**
   * **Odhlašovací adresa URL**

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurace zabezpečení Messenger TigerText jednotného přihlašování

Konfigurace jednotného přihlašování na straně TigerText zabezpečení Messenger, je nutné odeslat stažené kód XML metadat federace a příslušné adresy URL zkopírovanou ze na webu Azure portal [tým podpory zabezpečení Messenger TigerText](mailto:prosupport@tigertext.com). Tým zabezpečení Messenger TigerText zajistit, aby že SAML SSO připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

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

V této části je povolit Britta Simon poskytováním přístupu ke službě Messenger zabezpečení TigerText používat Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **TigerText zabezpečení Messenger**.

    ![Podokno podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **TigerText zabezpečení Messenger**.

    ![TigerText zabezpečení Messenger v seznamu aplikací](common/all-applications.png)

1. V levém podokně v části **SPRAVOVAT**vyberte **uživatelů a skupin**.

    ![Možnost "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **+ přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** podokně.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V **uživatelů a skupin** vyberte **Britta Simon** v **uživatelé** seznamu a klikněte na tlačítko **vyberte** v dolní části podokna.

1. Pokud očekáváte hodnotu do role kontrolní výraz SAML, pak v **vybrat roli** podokně, vyberte vhodnou roli pro uživatele ze seznamu. V dolní části podokna, zvolte **vyberte**.

1. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Vytvoření zkušebního uživatele TigerText zabezpečení Messenger

V této části vytvořte uživatele Britta Simon TigerText zabezpečení Messenger. Práce s [tým podpory zabezpečení Messenger TigerText](mailto:prosupport@tigertext.com) přidat jako uživatel ve službě Messenger zabezpečení TigerText Britta Simon. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu Moje aplikace.

Když vyberete **TigerText zabezpečení Messenger** na portálu Moje aplikace vám by měl být automaticky přihlášeni k zabezpečení Messenger TigerText předplatného, u kterého nastavíte jednotné přihlašování. Další informace o portálu Moje aplikace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
