---
title: 'Kurz: Integrace Azure Active Directory s Kontiki | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098490"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Kurz: Integrace Azure Active Directory s Kontiki

V tomto kurzu se dozvíte, jak integrovat Kontiki s Azure Active Directory (Azure AD).

Kontiki integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit, kdo má přístup k Kontiki Azure AD.
* Uživatelé mohou být automaticky přihlášeni ke Kontiki pomocí jejich účtů služby Azure AD (jednotné přihlašování).
* Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Kontiki, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure AD, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.
* Předplatné Kontiki pomocí jednotného přihlašování povolená.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování a Kontiki integrace s Azure AD.

Kontiki podporuje následující funkce:

* **Iniciovaného Zprostředkovatelem přihlašování jednotného přihlašování**
* **Zřizování uživatelů just-in-time**

## <a name="add-kontiki-in-the-azure-portal"></a>Přidat Kontiki na webu Azure Portal

Kontiki integrovat Azure AD, je nutné přidat Kontiki na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte **novou aplikaci**.

    ![Nová možnost aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Kontiki**. Ve výsledcích hledání vyberte **Kontiki**a pak vyberte **přidat**.

    ![Kontiki v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Kontiki podle testovacího uživatele s názvem **Britta Simon**. Pro jednotné přihlašování pro práci je potřeba vytvořit v Kontiki propojené vztah mezi uživatele služby Azure AD a související uživatel.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Kontiki, je nutné dokončit následující stavebních bloků:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům tuto funkci používat. |
| **[Konfigurace Kontiki jednotného přihlašování](#configure-kontiki-single-sign-on)** | Konfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Zkoušky Azure AD jednotného přihlašování pro uživatele s názvem Britta Simon. |
| **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)** | Umožňuje Britta Simon používat Azure AD jednotného přihlašování. |
| **[Vytvoření zkušebního uživatele Kontiki](#create-a-kontiki-test-user)** | Vytvoří protějšek Britta Simon Kontiki, který je propojený s Azure AD zastoupení uživatele. |
| **[Otestovat jednotné přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části nakonfigurujete služby Azure AD jednotné přihlašování s Kontiki na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)v **Kontiki** podokno integrace aplikací, vyberte **jednotného přihlašování**.

    ![Nakonfigurujte možnost přihlašování](common/select-sso.png)

1. V **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML** nebo **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

1. V **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit** (ikonu tužky) Chcete-li otevřít **základní konfiguraci SAML** podokně.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. V **základní konfiguraci SAML** podokno v **přihlašovací adresa URL** text, zadejte adresu URL, která má následující vzor: `https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    > [!NOTE]
    > Obraťte se [tým podpory Kontiki klienta](https://customersupport.kontiki.com/enterprise/contactsupport.html) získat správnou hodnotu použití. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. V **nastavte si jednotné přihlašování pomocí SAML** podokno v **podpisový certifikát SAML** vyberte **Stáhnout** vedle **kód XML metadat federace**. Možnost stažení na základě vašich požadavků. Uložte si certifikát ve vašem počítači.

    ![Možnost stažení certifikátu kód XML metadat federace](common/metadataxml.png)

1. V **nastavení Kontiki** tématu, zkopírujte následující adresy URL na základě vašich požadavků:

    * Přihlašovací adresa URL
    * Identifikátor Azure AD
    * Adresa URL – odhlášení

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurace Kontiki jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování na straně Kontiki, odeslat stažený soubor XML metadat federace a příslušné adresy URL, které jste zkopírovali z portálu Azure portal k [tým podpory Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). Tým podpory Kontiki používá informace, které pošlete je zajistit, že SAML jednotné přihlašování – nastavení připojení správně na obou stranách.

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

V této části udělíte přístup Britta Simon k Kontiki, které můžete použít Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **Kontiki**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **Kontiki**.

    ![Kontiki v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **uživatelů a skupin**.

    ![Možnost uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**. Potom v **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V **uživatelů a skupin** vyberte **Britta Simon** v seznamu uživatelů. Zvolte **Vybrat**.

1. Pokud se očekává hodnotu kontrolního výrazu SAML, do role v **vybrat roli** podokně, vyberte odpovídající roli pro uživatele ze seznamu. Zvolte **Vybrat**.

1. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-kontiki-test-user"></a>Vytvoření zkušebního uživatele Kontiki

Neexistuje žádná položka akce můžete nakonfigurovat v Kontiki zřizování uživatelů. Přiřazený uživatel se pokusí přihlásit k Kontiki na portálu Moje aplikace, Kontiki kontroluje, zda uživatel existuje. Pokud se nenajde žádný uživatelský účet, Kontiki automaticky vytvoří uživatelský účet.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu Moje aplikace.

Jakmile nastavíte jednotné přihlašování, když vyberete **Kontiki** na portálu Moje aplikace, budete automaticky přihlášeni k Kontiki. Další informace o portálu Moje aplikace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další postup

Další informace, projděte si tyto články:

- [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
