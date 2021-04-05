---
title: 'Kurz: Azure Active Directory integrace s vytvářením sestav cen Predictix | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vytvářením sestav Predictix cen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 801197a171b5be2cfe46c249398381e1b25857ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515351"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Kurz: Azure Active Directory integrace s vytvářením sestav cen Predictix

V tomto kurzu se dozvíte, jak integrovat Predictix Price Reporting s Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Službu Azure AD můžete použít k řízení, kdo má přístup k Predictix cenovému vykazování.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Predictix cen (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K nakonfigurování integrace služby Azure AD s využitím sestav Predictix Price potřebujete:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete si zaregistrovat [měsíční zkušební](https://azure.microsoft.com/pricing/free-trial/) předplatné.
* Předplatné vykazování ceny Predictix s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Vytváření sestav cen Predictix podporuje jednotné přihlašování iniciované v SP.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Přidávání sestav Predictix Price z Galerie

Pokud chcete nastavit integraci Predictixch sestav s cenami do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Predictix pro vytváření sestav cen z galerie.

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít k **podnikovým aplikacím**  >  **všechny aplikace**:

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Predictix Price Reporting**. Ve výsledcích hledání vyberte **Predictix Price Reporting** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s využitím sestav Predictix Price pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, musíte vytvořit relaci mezi uživatelem služby Azure AD a odpovídajícím uživatelem v sestavách Predictix Price.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí sestav Predictix Price, je potřeba provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , abyste funkci povolili uživatelům.
2. **[Konfigurace jednotného přihlašování Predictix Price Reporting](#configure-predictix-price-reporting-single-sign-on)** na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** povolení jednotného přihlašování Azure AD pro uživatele.
5. **[Vytvořte testovacího uživatele Predictix Price pro vytváření sestav](#create-a-predictix-price-reporting-test-user)** , který je propojený s Předprezentací Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí sestav Predictix Price, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce Application informování o **cenách Predictix** vyberte **jednotné přihlašování**:

    ![Vyberte jednotné přihlašování.](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte ikonu **Upravit** a otevřete DIALOGOVÉ okno **základní konfigurace SAML** :

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **základní konfigurace SAML** proveďte následující kroky.

    ![Základní dialogové okno Konfigurace SAML](common/sp-identifier.png)

    1. Do pole **přihlašovací adresa URL** zadejte adresu URL v tomto vzoru:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. Do pole **identifikátor (ID entity)** zadejte adresu URL v tomto vzoru:

        ```https
        https://<companyname-pricing>.predictix.com
        https://<companyname-pricing>.dev.predictix.com
        ```

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Musíte použít vlastní přihlašovací adresu URL a identifikátor. Pokud chcete získat hodnoty, obraťte se na [tým podpory generování sestav Predictix Price](https://www.infor.com/company/customer-center/) . Můžete se také podívat na vzory zobrazené v dialogovém okně **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte odkaz pro **stažení** vedle **certifikátu (Base64)** podle vašich požadavků a uložte certifikát do počítače:

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení vytváření sestav cen Predictix** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

    1. **Přihlašovací adresa URL**

    1. **Identifikátor Azure AD**.

    1. **Odhlašovací adresa URL**

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Konfigurace jednotného přihlašování pro Predictix ceny sestav

Chcete-li nakonfigurovat jednotné přihlašování na straně Predictix Price, je třeba odeslat certifikát, který jste stáhli, a adresy URL, které jste zkopírovali z Azure Portal do [týmu podpory Predictix Price pro vytváření sestav](https://www.infor.com/company/customer-center/). Tento tým zajišťuje správné nastavení připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory** , vyberte **Uživatelé** a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** :

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **uživatel** proveďte následující kroky.

    ![Uživatel – dialogové okno](common/user-properties.png)

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **BrittaSimon@ \<yourcompanydomain> . \<extension>**. (Například BrittaSimon@contoso.com .)

    1. Vyberte možnost **Zobrazit heslo** a pak zapište hodnotu, která je uvedena v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí služby Azure AD tím, že udělíte přístup k Predictix cenovému vykazování.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Predictix Price Reporting**.

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Predictix Price Reporting**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-predictix-price-reporting-test-user"></a>Vytvořit testovacího uživatele pro vytváření sestav s Predictixmi cenami

V dalším kroku je potřeba vytvořit uživatele s názvem Britta Simon v hlášení ceny Predictix. Pokud chcete přidat uživatele, pracujte s [týmem podpory tvorby sestav Predictix Price](https://www.infor.com/company/customer-center/) . Před použitím jednotného přihlašování je potřeba vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici zasílání zpráv o cenách Predictix, měli byste se automaticky přihlášeni ke službě Predictix Price Reporting instance, pro kterou jste nastavili jednotné přihlašování. Další informace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy integrace aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)