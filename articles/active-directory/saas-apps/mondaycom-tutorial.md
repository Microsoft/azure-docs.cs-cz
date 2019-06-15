---
title: 'Kurz: Integrace Azure Active Directory s monday.com | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08108a1718ee917a317b4864de81959ae139eaa5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097017"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>Kurz: Integrace Azure Active Directory s monday.com

V tomto kurzu se dozvíte, jak integrovat monday.com s Azure Active Directory (Azure AD).

Monday.com integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit, kdo má přístup k monday.com Azure AD.
* Uživatelům můžete automaticky přihlášeni k monday.com pomocí jejich účtů služby Azure AD (jednotné přihlašování).
* Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s monday.com, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure AD, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.
* Předplatné monday.com pomocí jednotného přihlašování povolená.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování a monday.com integrace s Azure AD.

Monday.com podporuje následující funkce:

* **Iniciovaného Zprostředkovatelem přihlašování jednotného přihlašování**
* **Zahájené pomocí IDP jednotného přihlašování**
* **Zřizování uživatelů just-in-time**

## <a name="add-mondaycom-in-the-azure-portal"></a>Přidat monday.com na webu Azure Portal

Monday.com integrovat Azure AD, je nutné přidat monday.com na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte **novou aplikaci**.

    ![Nová možnost aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **monday.com**. Ve výsledcích hledání vyberte **monday.com**a pak vyberte **přidat**.

    ![Monday.com v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s monday.com podle testovacího uživatele s názvem **Britta Simon**. Pro jednotné přihlašování pro práci je potřeba vytvořit v monday.com propojené vztah mezi uživatele služby Azure AD a související uživatel.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s monday.com, je nutné dokončit následující stavebních bloků:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům tuto funkci používat. |
| **[Konfigurace monday.com jednotného přihlašování](#configure-mondaycom-single-sign-on)** | Konfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Zkoušky Azure AD jednotného přihlašování pro uživatele s názvem Britta Simon. |
| **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)** | Umožňuje Britta Simon používat Azure AD jednotného přihlašování. |
| **[Vytvoření zkušebního uživatele monday.com](#create-a-mondaycom-test-user)** | Vytvoří protějšek Britta Simon monday.com, který je propojený s Azure AD zastoupení uživatele. |
| **[Otestovat jednotné přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete nakonfigurovat služby Azure AD jednotného přihlašování monday.com na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)v **monday.com** podokno integrace aplikací, vyberte **jednotného přihlašování**.

    ![Nakonfigurujte možnost přihlašování](common/select-sso.png)

1. V **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML** nebo **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

1. V **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit** (ikonu tužky) Chcete-li otevřít **základní konfiguraci SAML** podokně.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. V **základní konfiguraci SAML** podokně, pokud máte soubor zprostředkovatele metadat služby a chcete provést konfiguraci *zahájené pomocí IDP režimu*, proveďte následující kroky:

    1. Vyberte **nahrát soubor metadat**.

       ![Možnosti nahrání metadat souboru](common/upload-metadata.png)

    1. Pokud chcete vybrat soubor metadat, vyberte ikonu složky a pak vyberte **nahrát**.

       ![Vyberte soubor metadat a pak vyberte tlačítko Nahrát](common/browse-upload-metadata.png)

    1. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty se automaticky vyplní v **základní konfiguraci SAML** podokna:

       ![Hodnoty zprostředkovatele identity v podokně základní konfiguraci SAML](common/idp-intiated.png)

       > [!Note]
       > Pokud **identifikátor** a **adresy URL odpovědi** nejsou automaticky vyplněné hodnoty, zadejte hodnoty ručně.

1. Konfigurace aplikace v *iniciovaného Zprostředkovatelem přihlašování režimu*:

    1. Vyberte **nastavit další adresy URL**.
    
    1. V **přihlašovací adresa URL** zadejte adresu URL, která má následující vzor: https:\//\<Vaše_Doména >. monday.com. Obraťte se [tým podpory klienta monday.com](mailto:support@monday.com) získat adresu URL přihlašování.

        ![Další adresy URL možnost Set](common/metadata-upload-additional-signon.png)

1. Aplikace monday.com očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Ke správě těchto atributů v **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit** otevřít **atributy uživatele** podokně.

    ![Podokno atributy uživatele](common/edit-attribute.png)

1. V části **deklarace identity uživatelů**vyberte **upravit** k úpravě deklarace identity. Chcete-li přidat deklaraci identity, **přidat novou deklaraci**a potom nakonfigurujte atribut tokenu SAML, jak je znázorněno na předchozím obrázku. Potom proveďte následující kroky: 

    1. Vyberte **přidat novou deklaraci**.

        ![Přidat novou deklaraci identity v podokně deklarace identity uživatele](common/new-save-attribute.png)

    1. V **spravovat deklarace identity uživatelů** podokno, nastavte následující hodnoty:
        
       1. V **název** zadejte název atributu pro řádek deklarace identity uživatele.

       1. Ponechte **Namespace** prázdné.

       1. Pro **zdroj**vyberte **atribut**.

       1. V **zdrojový atribut** vyberte hodnotu atributu pro řádek deklarace identity uživatele.

       1. Vyberte **OK**a pak vyberte **Uložit**.

       ![Deklarace identity uživatelů spravovat](common/new-attribute-details.png)

1. V **nastavte si jednotné přihlašování pomocí SAML** podokně v části **podpisový certifikát SAML**vyberte **Stáhnout** vedle **certifikát (Base64)** . Možnost stažení na základě vašich požadavků. Uložte si certifikát ve vašem počítači.

    ![Možnost stažení certifikát (Base64)](common/certificatebase64.png)

1. V **nastavení monday.com** tématu, zkopírujte následující adresy URL na základě vašich požadavků:

    * Přihlašovací adresa URL
    * Identifikátor Azure AD
    * Adresa URL – odhlášení

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>Konfigurace monday.com jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování na straně monday.com, odeslat stažený soubor certifikátu (Base64) a příslušné adresy URL, které jste zkopírovali z portálu Azure portal k [tým podpory monday.com](mailto:support@monday.com). Tým podpory monday.com používá informace, které pošlete je zajistit, že SAML jednotné přihlašování – nastavení připojení správně na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.

    ![Uživatelé a všechny možnosti uživatele](common/users.png)

1. Vyberte **nového uživatele**.

    ![Nová možnost uživatele](common/new-user.png)

1. V **uživatele** podokno, proveďte následující kroky:

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **brittasimon\@\<your domény společnosti >.\< Rozšíření >** . Například **brittasimon\@contoso.com**.

    1. Vyberte **zobrazit heslo** zaškrtávací políčko. Zapište hodnotu, která se zobrazí **heslo** pole.

    1. Vyberte **Vytvořit**.

    ![V podokně uživatele](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části udělíte přístup Britta Simon k monday.com, které můžete použít Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **monday.com**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **monday.com**.

    ![Monday.com v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **uživatelů a skupin**.

    ![Možnost uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**. Potom v **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V **uživatelů a skupin** vyberte **Britta Simon** v seznamu uživatelů. Zvolte **Vybrat**.

1. Pokud se očekává hodnotu kontrolního výrazu SAML, do role v **vybrat roli** podokně, vyberte odpovídající roli pro uživatele ze seznamu. Zvolte **Vybrat**.

1. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-mondaycom-test-user"></a>Vytvoření zkušebního uživatele monday.com

V této části se vytvoří uživatele s názvem Britta Simon monday.com aplikace. Monday.com podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi monday.com, vytvoří se nový po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu Moje aplikace.

Jakmile nastavíte jednotné přihlašování, když vyberete **monday.com** na portálu Moje aplikace, budete automaticky přihlášeni k monday.com. Další informace o portálu Moje aplikace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další postup

Další informace, projděte si tyto články:

- [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
