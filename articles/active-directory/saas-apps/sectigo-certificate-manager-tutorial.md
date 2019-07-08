---
title: 'Kurz: Integrace s Azure Active Directory pomocí Správce certifikátů Sectigo | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sectigo správce certifikátů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588241"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Kurz: Integrace s Azure Active Directory pomocí Správce certifikátů Sectigo

V tomto kurzu se dozvíte, jak integrovat Sectigo správce certifikátů Azure Active Directory (Azure AD).

Správce certifikátů Sectigo integrace s Azure AD poskytuje následující výhody:

* Můžete řídit, kdo má přístup správce certifikátů Sectigo k Azure AD.
* Uživatelům můžete být automaticky přihlášeni do Správce certifikátů Sectigo pomocí jejich účtů služby Azure AD (jednotné přihlašování).
* Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí Správce certifikátů Sectigo, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure AD, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.
* Správce certifikátů Sectigo předplatné s single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování a integrace správce certifikátů Sectigo s Azure AD.

Správce certifikátů Sectigo podporuje následující funkce:

* **Iniciovaného Zprostředkovatelem přihlašování jednotného přihlašování**
* **Zahájené pomocí IDP jednotného přihlašování**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Přidat správce certifikátů Sectigo na webu Azure Portal

Správce certifikátů Sectigo integrovat Azure AD, musíte přidat správce certifikátů Sectigo na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte **novou aplikaci**.

    ![Nová možnost aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **správce certifikátů Sectigo**. Ve výsledcích hledání vyberte **správce certifikátů Sectigo**a pak vyberte **přidat**.

    ![Správce certifikátů Sectigo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Sectigo správce certifikátů na základě testovací uživatele s názvem **Britta Simon**. Pro jednotné přihlašování pro práci je potřeba vytvořit propojené vztah mezi uživatele služby Azure AD a související uživatelské ve Správci certifikátů Sectigo.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Správce certifikátů Sectigo, je nutné dokončit následující stavebních bloků:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům tuto funkci používat. |
| **[Konfigurace správce certifikátů Sectigo jednotného přihlašování](#configure-sectigo-certificate-manager-single-sign-on)** | Konfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Zkoušky Azure AD jednotného přihlašování pro uživatele s názvem Britta Simon. |
| **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)** | Umožňuje Britta Simon používat Azure AD jednotného přihlašování. |
| **[Vytvoření zkušebního uživatele Sectigo správce certifikátů](#create-a-sectigo-certificate-manager-test-user)** | Vytvoří protějšek Britta Simon v Sectigo správce certifikátů, který je propojený s Azure AD zastoupení uživatele. |
| **[Otestovat jednotné přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části nakonfigurujete služby Azure AD jednotné přihlašování pomocí Správce certifikátů Sectigo na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)v **správce certifikátů Sectigo** podokno integrace aplikací, vyberte **jednotného přihlašování**.

    ![Nakonfigurujte možnost přihlašování](common/select-sso.png)

1. V **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML** nebo **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

1. V **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit** (ikonu tužky) Chcete-li otevřít **základní konfiguraci SAML** podokně.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. V **základní konfiguraci SAML** podokně nakonfigurovat *zahájené pomocí IDP režimu*, proveďte následující kroky:

    1. V **identifikátor** zadejte jednu z těchto adres URL:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. V **adresy URL odpovědi** zadejte jednu z těchto adres URL:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Vyberte **nastavit další adresy URL**.

    1. V **stav přenosu** zadejte jednu z těchto adres URL:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Správce certifikátů Sectigo domény a adresy URL jednotného přihlašování – informace](common/idp-relay.png)

1.  Konfigurace aplikace v *iniciovaného Zprostředkovatelem přihlašování režimu*, proveďte následující kroky:

    * V **přihlašovací adresa URL** zadejte jednu z těchto adres URL:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Správce certifikátů Sectigo domény a adresy URL jednotného přihlašování – informace](common/both-signonurl.png)

1. V **nastavte si jednotné přihlašování pomocí SAML** podokno v **podpisový certifikát SAML** vyberte **Stáhnout** vedle **certifikát (Base64)** . Možnost stažení na základě vašich požadavků. Uložte si certifikát ve vašem počítači.

    ![Možnost stažení certifikát (Base64)](common/certificatebase64.png)

1. V **nastavte Sectigo certifikát Manager** tématu, zkopírujte následující adresy URL na základě vašich požadavků:

    * Přihlašovací adresa URL
    * Identifikátor Azure AD
    * Adresa URL – odhlášení

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurace správce certifikátů Sectigo jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování na straně Sectigo správce certifikátů, odeslat stažený soubor certifikátu (Base64) a příslušné adresy URL, které jste zkopírovali z portálu Azure portal k [tým podpory správce certifikátů Sectigo](https://sectigo.com/support). Tým podpory správce certifikátů Sectigo používá informace, které pošlete je zajistit, že SAML jednotné přihlašování – nastavení připojení správně na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.

    ![Uživatelé a všechny možnosti uživatele](common/users.png)

1. Vyberte **nového uživatele**.

    ![Nová možnost uživatele](common/new-user.png)

1. V **uživatele** podokno, proveďte následující kroky:

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **brittasimon\@\<your domény společnosti >.\< rozšíření\>** . Například **brittasimon\@contoso.com**.

    1. Vyberte **zobrazit heslo** zaškrtávací políčko. Zapište hodnotu, která se zobrazí **heslo** pole.

    1. Vyberte **Vytvořit**.

    ![V podokně uživatele](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části udělíte Britta Simon přístup k Sectigo správce certifikátů, které můžete použít Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **správce certifikátů Sectigo**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **správce certifikátů Sectigo**.

    ![Správce certifikátů Sectigo v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **uživatelů a skupin**.

    ![Možnost uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**. Potom v **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V **uživatelů a skupin** vyberte **Britta Simon** v seznamu uživatelů. Zvolte **Vybrat**.

1. Pokud se očekává hodnotu kontrolního výrazu SAML, do role v **vybrat roli** podokně, vyberte odpovídající roli pro uživatele ze seznamu. Zvolte **Vybrat**.

1. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Vytvoření zkušebního uživatele Sectigo správce certifikátů

V této části vytvoříte uživatele s názvem Britta Simon ve Správci certifikátů Sectigo. Práce s [tým podpory správce certifikátů Sectigo](https://sectigo.com/support) přidejte uživatele na platformě Sectigo správce certifikátů. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu Moje aplikace.

Jakmile nastavíte jednotné přihlašování, když vyberete **správce certifikátů Sectigo** na portálu Moje aplikace se automaticky přihlásíte k Sectigo správce certifikátů. Další informace o portálu Moje aplikace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další postup

Další informace, projděte si tyto články:

- [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


