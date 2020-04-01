---
title: 'Kurz: Integrace služby Azure Active Directory s vykazováním cen Predictix | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Predictix Price Reporting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 808b2d964bb39af6b410a84563717102ebece454
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094114"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Kurz: Integrace služby Azure Active Directory s vykazováním cen Predictix

V tomto kurzu se dozvíte, jak integrovat Predictix Vykazování cen s Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Azure AD můžete použít k řízení, kdo má přístup k Predictix vykazování cen.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k Predictix Price Reporting (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí predictix price reportingu, potřebujete:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete si zaregistrovat [jednoměsíční zkušební](https://azure.microsoft.com/pricing/free-trial/) předplatné.
* Předplatné Predictix Price Reporting, které má povoleno jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí.

* Predictix Price Reporting podporuje sp-iniciované sso.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Přidání predictix ustavičné ceny z galerie

Chcete-li nastavit integraci Predictix Price Reporting do Azure AD, musíte přidat Predictix Vykazování cen z galerie do seznamu spravovaných aplikací SaaS.

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Službu Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít na **podnikové aplikace** > **Všechny aplikace**:

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace:**

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Predictix Price Reporting**. Ve výsledcích hledání vyberte **Predictix Price Reporting** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Predictix Price Reporting pomocí testovacího uživatele s názvem Britta Simon.
Chcete-li povolit jednotné přihlašování, musíte vytvořit vztah mezi uživatelem Azure AD a odpovídajícím uživatelem v Predictix Price Reporting.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí predictix price reportingu, musíte provést následující kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby byla tato funkce povolena pro vaše uživatele.
2. **[Nakonfigurujte jednotné přihlašování Predictix](#configure-predictix-price-reporting-single-sign-on)** na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlášení Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD,](#assign-the-azure-ad-test-user)** aby uživatel povolil jednotné přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele Predictix Price Reporting,](#create-a-predictix-price-reporting-test-user)** který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí vykazování cen Predictix, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikace **Predictix Price Reporting** vyberte Jednotné **přihlašování**:

    ![Vybrat jednotné přihlašování](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování:

    ![Výběr metody jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu **Upravit** a otevřete dialogové okno Základní **konfigurace SAML:**

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **Základní konfigurace SAML** proveďte následující kroky.

    ![Dialogové okno Základní konfigurace SAML](common/sp-identifier.png)

    1. Do pole **Přihlásit se na adresu URL** zadejte adresu URL v tomto vzoru:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. Do pole **Identifikátor (ID entity)** zadejte adresu URL v tomto vzoru:

        | |
        |--|
        | `https://<companyname-pricing>.predictix.com` |
        | `https://<companyname-pricing>.dev.predictix.com` |
        | |

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Musíte použít skutečnou přihlašovací adresu URL a identifikátor. Chcete-li získat hodnoty, obraťte se na [tým podpory Predictix Price Reporting.](https://www.infor.com/company/customer-center/) Můžete také odkazovat na vzory zobrazené v dialogovém okně **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** odkaz **Ke stažení** vedle **certifikátu (Base64)** podle vašich požadavků a uložte certifikát do počítače:

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit vykazování cen predictix** zkopírujte příslušné adresy URL na základě vašich požadavků.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    1. **Adresa URL přihlášení**.

    1. **Identifikátor služby Azure .**

    1. **Adresa URL pro odhlášení**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Konfigurace jednotného přihlášení vykazování cen služby Predictix

Chcete-li nakonfigurovat jednotné přihlašování na straně Predictix Price Reporting, musíte odeslat certifikát, který jste stáhli, a adresy URL, které jste zkopírovali z portálu Azure, [týmu podpory Predictix Price Reporting](https://www.infor.com/company/customer-center/). Tento tým zajišťuje, že připojení jednotného přistajení SAML je správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal vyberte v levém podokně **Službu Azure Active Directory,** vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel:**

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    1. Do pole **Název** zadejte **BrittaSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **BrittaSimon@\<\<> vaší firemní domény. prodloužení>**. (Například BrittaSimon@contoso.com.)

    1. Vyberte **Zobrazit heslo**a poznamenejte si hodnotu, která je v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části umožníte Britta Simon používat Azure AD jednotné přihlášení tím, že jí přístup k Predictix Vykazování cen.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Predictix Price Reporting**.

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Predictix Price Reporting**.

    ![Seznam žádostí](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé **Brittu Simonovou** a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. Klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-predictix-price-reporting-test-user"></a>Vytvoření testovacího uživatele predictix

Dále je třeba vytvořit uživatele s názvem Britta Simon v Predictix Vykazování cen. Spolupracujte s [týmem podpory Predictix Price Reporting](https://www.infor.com/company/customer-center/) a přidejte uživatele. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když vyberete dlaždici Predictix Price Reporting na přístupovém panelu, měli byste být automaticky přihlášeni k instanci Predictix Price Reporting, pro kterou nastavíte automatické přihlašování. Další informace najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)