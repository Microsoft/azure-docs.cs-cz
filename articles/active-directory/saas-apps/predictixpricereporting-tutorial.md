---
title: 'Kurz: Integrace Azure Active Directory s vytvářením sestav cena Predictix | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vytváření sestav Predictix cena.
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
ms.openlocfilehash: 8074919a61ed4f2e3d0b127c13e733b0c485bf62
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890657"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Kurz: Integrace Azure Active Directory s vytvářením sestav Predictix cena

V tomto kurzu se dozvíte, jak integrovat Predictix cena generování sestav Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Můžete řídit, kdo má přístup k vytváření sestav cena Predictix Azure AD.
* Můžete povolit uživatelům být automaticky přihlášeni k vytváření sestav cena Predictix (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před zahájením.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vytvářením sestav cena Predictix, budete potřebovat:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete si zaregistrovat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/) předplatného.
* Vytváření sestav cena Predictix předplatné, které má single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* Vytváření sestav cena Predictix podporuje jednotné iniciovaného Zprostředkovatelem přihlašování.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Přidání vytváření sestav cena Predictix z Galerie

Postup nastavení integrace generování sestav cena Predictix do služby Azure AD, musíte doplnit Predictix cena Reporting z Galerie váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **všechny aplikace**:

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

    ![Vyberte novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Reporting cena Predictix**. Vyberte **Predictix cena Reporting** ve výsledcích hledání a pak vyberte **přidat**.

     ![Výsledky vyhledávání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a Azure AD jednotné přihlašování s vytvářením sestav cena Predictix test pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, budete muset vytvořit vztah mezi uživatele služby Azure AD a odpovídajícího uživatele ve službě Reporting Predictix cena.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s vytvářením sestav Predictix ceny, je potřeba provést tyto kroky:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  k povolení této funkce pro vaše uživatele.
2. **[Konfigurace vytváření sestav cena Predictix jednotného přihlašování](#configure-predictix-price-reporting-single-sign-on)**  na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotného přihlašování.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolení služby Azure AD jednotného přihlašování pro uživatele.
5. **[Vytvoření zkušebního uživatele Predictix cena Reporting](#create-a-predictix-price-reporting-test-user)**  připojený k Azure AD zastoupení uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části budete povolení služby Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s vytvářením sestav cena Predictix, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Predictix cena Reporting** integrace stránce aplikace vyberte **jednotného přihlašování**:

    ![Vyberte jednotného přihlašování](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno:

    ![Upravit ikonu](common/edit-urls.png)

4. V **základní konfiguraci SAML** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno základní konfigurace SAML](common/sp-identifier.png)

    1. V **přihlašovací adresa URL** pole, zadejte adresu URL v tomto vzoru:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. V **identifikátor (Entity ID)** pole, zadejte adresu URL v tomto vzoru:

        | |
        |--|
        | `https://<companyname-pricing>.predictix.com` |
        | `https://<companyname-pricing>.dev.predictix.com` |
        | |

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Budete muset použít skutečné přihlašovací adresu URL a identifikátor. Obraťte se [Reporting cena Predictix tým podpory](https://www.infor.com/company/customer-center/) k získání hodnoty. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** dialogové okno na webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** odkaz **certifikát (Base64)** , podle požadavků vaší a uložte certifikát v počítači:

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V **nastavení vykazování cena Predictix** tématu, zkopírujte příslušné adresy URL, na základě vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    1. **Adresa URL pro přihlášení**.

    1. **Identifikátor služby Azure AD**.

    1. **Odhlašovací adresa URL**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Konfigurace vytváření sestav cena Predictix jednotného přihlašování

Konfigurace jednotného přihlašování na straně Predictix cena vykazování, budete muset odeslat certifikát, který jste stáhli a adresy URL, které jste zkopírovali z portálu Azure portal k [Reporting cena Predictix tým podpory](https://www.infor.com/company/customer-center/). Tento tým zajistí, že je správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** v levém podokně vyberte **uživatelé**a pak vyberte **všichni uživatelé**:

    ![Vyberte možnost Všichni uživatelé](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky:

    ![Vyberte nového uživatele](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatelského](common/user-properties.png)

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **BrittaSimon @\<doména_společnosti >.\< Rozšíření >**. (Například BrittaSimon@contoso.com.)

    1. Vyberte **zobrazit heslo**a zapište si hodnotu, která je v **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části se budou moci používat Azure AD jednotného přihlašování tak, že udělíte přístup k vytváření sestav cena Predictix Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Reporting cena Predictix**.

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Reporting cena Predictix**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **uživatelů a skupin**:

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte, že hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-a-predictix-price-reporting-test-user"></a>Vytvoření zkušebního uživatele Predictix cena Reporting

Dále je třeba vytvořit uživatele s názvem Britta Simon v sestavách Predictix cena. Práce s [Reporting cena Predictix tým podpory](https://www.infor.com/company/customer-center/) přidat uživatele. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Teď je potřeba otestovat vaši konfiguraci Azure AD jednotné přihlašování pomocí přístupového panelu.

Při výběru dlaždice Reporting Predictix cena na přístupovém panelu, můžete by měl být automaticky přihlášeni Predictix cena Reporting instanci, u kterého nastavíte jednotné přihlašování. Další informace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)