---
title: 'Kurz: Azure Active Directory integrace s Kontiki | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kontiki.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 9e0b438e0cfa729300099463086248780c69484c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95019836"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Kurz: Azure Active Directory integrace s Kontiki

V tomto kurzu se dozvíte, jak integrovat Kontiki s Azure Active Directory (Azure AD).

Integrace Kontiki s Azure AD přináší následující výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k Kontiki.
* Uživatelé můžou být automaticky přihlášení k Kontiki pomocí svých účtů Azure AD (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění, Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Kontiki potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte předplatné služby Azure AD, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Předplatné Kontiki s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí a integrujete Kontiki se službou Azure AD.

Kontiki podporuje následující funkce:

* **Jednotné přihlašování inicializované v SP**
* **Zřizování uživatelů za běhu**

## <a name="add-kontiki-in-the-azure-portal"></a>Přidat Kontiki do Azure Portal

Pokud chcete integrovat Kontiki s Azure AD, musíte přidat Kontiki do seznamu spravovaných aplikací SaaS.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte možnost **Nová aplikace**.

    ![Možnost nové aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Kontiki**. Ve výsledcích hledání vyberte **Kontiki** a pak vyberte **Přidat**.

    ![Kontiki v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Kontiki na základě testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v Kontiki.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Kontiki, musíte dokončit tyto stavební bloky:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům používat tuto funkci. |
| **[Konfigurace jednotného přihlašování Kontiki](#configure-kontiki-single-sign-on)** | Nakonfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Testuje jednotné přihlašování Azure AD pro uživatele s názvem Britta Simon. |
| **[Přiřazení testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** | Povolí službě Britta Simon používat jednotné přihlašování Azure AD. |
| **[Vytvořit testovacího uživatele v Kontiki](#create-a-kontiki-test-user)** | Vytvoří protějšek Britta Simon v Kontiki, která je propojená se zastoupením uživatele v Azure AD. |
| **[Test jednotného přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nakonfigurujete jednotné přihlašování Azure AD pomocí Kontiki v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)v podokně integrace aplikace **Kontiki** vyberte **jednotné přihlašování**.

    ![Konfigurovat možnost jednotného přihlašování](common/select-sso.png)

1. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost **SAML** nebo **SAML/WS-nakrmený** režim pro povolení jednotného přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte **Upravit** (ikona tužky) a otevřete základní podokno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **základní konfigurace SAML** v textovém poli **Adresa URL pro přihlášení** zadejte adresu URL, která má následující vzor: `https://<companyname>.mc.eval.kontiki.com`

    ![Informace o jednotném přihlašování v doméně Kontiki a adresách URL](common/sp-signonurl.png)

    > [!NOTE]
    > Pokud chcete získat správnou hodnotu, obraťte se na [tým podpory klienta Kontiki](https://kollective.com/support/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. V podokně **nastavit jednu Sign-On s** podoknem SAML vyberte v části **podpisový certifikát SAML** možnost **Stáhnout** vedle **metadat federace XML**. Vyberte možnost stažení podle vašich požadavků. Uložte certifikát do počítače.

    ![Možnost stažení certifikátu XML federačních metadat](common/metadataxml.png)

1. V části **Nastavení Kontiki** zkopírujte na základě vašich požadavků následující adresy URL:

    * Přihlašovací adresa URL
    * Identifikátor Azure AD
    * Odhlašovací adresa URL

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurace jednotného přihlašování Kontiki

Ke konfiguraci jednotného přihlašování na straně Kontiki odešlete stažený soubor XML federačních metadat a příslušné adresy URL, které jste zkopírovali z Azure Portal do [týmu podpory Kontiki](https://kollective.com/support/). Tým podpory Kontiki používá informace, které odesíláte, aby se zajistilo, že připojení jednotného přihlašování pomocí protokolu SAML je na obou stranách správně nastavené.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.

    ![Možnosti uživatelé a všichni uživatelé](common/users.png)

1. Vyberte **Nový uživatel**.

    ![Možnost Nový uživatel](common/new-user.png)

1. V podokně **uživatel** proveďte následující kroky:

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **brittasimon \@ \<your-company-domain> . \<extension>**. Například **brittasimon \@ contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** . Zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

    ![Podokno uživatele](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části udělíte Britta Simon přístup k Kontiki, aby mohli používat jednotné přihlašování Azure.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**  >  **Kontiki**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **Kontiki**.

    ![Kontiki v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **Uživatelé a skupiny**.

    ![Možnost Uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. Pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Podokno přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** . Zvolte **Vybrat**.

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-kontiki-test-user"></a>Vytvořit testovacího uživatele v Kontiki

Pro konfiguraci zřizování uživatelů v Kontiki není k dispozici žádná položka akce. Když se přiřazený uživatel pokusí přihlásit k Kontiki pomocí portálu moje aplikace, Kontiki zkontroluje, jestli uživatel existuje. Pokud se nenajde žádný uživatelský účet, Kontiki automaticky vytvoří uživatelský účet.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Po nastavení jednotného přihlašování se při výběru **Kontiki** na portálu moje aplikace automaticky přihlásíte k Kontiki. Další informace o portálu moje aplikace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc, přečtěte si tyto články:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)