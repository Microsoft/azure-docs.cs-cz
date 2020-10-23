---
title: 'Kurz: Azure Active Directory integrace s getabstract | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a getabstract.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 05138011822b15363b12f2c860f4f459972201a7
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92449740"
---
# <a name="tutorial-azure-active-directory-integration-with-getabstract"></a>Kurz: Azure Active Directory integrace s getabstract

V tomto kurzu se dozvíte, jak integrovat getabstract s Azure Active Directory (Azure AD).
Integrací getabstract with Azure AD získáte následující výhody:

* Můžete řídit v Azure AD, který má přístup k getabstract.
* Uživatelům můžete povolit, aby se automaticky přihlásili k getabstract (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí getabstract budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Povolit odběr getabstract jednotného přihlašování

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Getabstract podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování.

* Getabstract podporuje zřizování uživatelů **jenom v čase**


## <a name="adding-getabstract-from-the-gallery"></a>Přidání getabstract z Galerie

Pokud chcete nakonfigurovat integraci getabstract do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat getabstract z galerie.

**Chcete-li přidat getabstract z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **getabstract**, vyberte **getabstract** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Getabstract v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí getabstract založeného na testovacím uživateli s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci getabstract.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí getabstract, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte getabstract Single Sign-on](#configure-getabstract-single-sign-on)** pro konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření getabstract Test User](#create-getabstract-test-user)** – Pokud chcete mít protějšek Britta Simon v getabstract, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí getabstract, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Getabstract** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na **Upravit** ikona a otevřete DIALOGOVÉ okno **základní konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky zobrazující oddíl "základní S A M L" konfigurace s zvýrazněnou možností "identifikátor" a "adresa URL odpovědi" a vybrané tlačítko Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL:

    Pro fázi/pre_production: `https://int.getabstract.com`

    V produkčním prostředí: `https://www.getabstract.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL:
    
    Pro fázi/pre_production: `https://int.getabstract.com/ACS.do`
    
    V produkčním prostředí: `https://www.getabstract.com/ACS.do`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Getabstract doména a adresy URL jednotného přihlašování](common/metadata-upload-additional-signon.png)
    
    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    Pro fázi/pre_production: `https://int.getabstract.com/portal/<org_username>`
    
    V produkčním prostředí: `https://www.getabstract.com/portal/<org_username>`

    > [!NOTE] 
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory getabstract Client support](https://www.getabstract.com/en/contact) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavit getabstract** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-getabstract-single-sign-on"></a>Konfigurovat getabstract Single Sign-On

Chcete-li konfigurovat jednotné přihlašování na straně **getabstract** , je třeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory getabstract](https://www.getabstract.com/en/contact). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k getabstract.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **getabstract**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **getabstract**.

    ![Odkaz getabstract v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-getabstract-test-user"></a>Vytvořit getabstract Test User

V této části se v getabstract vytvoří uživatel s názvem Britta Simon. Getabstract podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povoleno. V této části není žádná položka akce. Pokud uživatel v getabstract ještě neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory getabstract](https://www.getabstract.com/en/contact) .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici getabstract na přístupovém panelu byste měli být automaticky přihlášeni k getabstract, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)