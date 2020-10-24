---
title: 'Kurz: Azure Active Directory integrace s organizačním diagramem nyní | Microsoft Docs'
description: Naučte se, jak můžete nakonfigurovat jednotné přihlašování mezi Azure Active Directory a organizačním diagramem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fc6b90be8e951c0e30c99d0bb16f85e49cf991f2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522321"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Kurz: Azure Active Directory integraci s organizačním diagramem

V tomto kurzu se naučíte integrovat organizační diagram nyní pomocí Azure Active Directory (Azure AD).
Integrace organizačního diagramu nyní s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který teď má přístup k organizačnímu diagramu.
* Uživatelům můžete povolit automatické přihlášení k organizačnímu diagramu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD pomocí organizačního diagramu teď potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné nyní s povoleným jednotným přihlašováním v organizačním diagramu

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Organizační diagram teď podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování

## <a name="adding-orgchart-now-from-the-gallery"></a>Přidávají se z Galerie nyní organizačního diagramu.

Pokud chcete nakonfigurovat integraci organizačního diagramu nyní do služby Azure AD, musíte z Galerie přidat organizační diagram do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat organizační diagram hned z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **organizační diagram nyní**, vyberte z panelu výsledků možnost **organizační diagram** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Organizační diagram nyní v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí organizačního diagramu na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v organizačním diagramu.

Pokud teď chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí organizačního diagramu, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování v organizačním diagramu](#configure-orgchart-now-single-sign-on)** : Pokud chcete nakonfigurovat nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si nyní testovacího uživatele](#create-orgchart-now-test-user)** , abyste měli k dispozici Britta Simon v organizačním diagramu, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud teď chcete nakonfigurovat jednotné přihlašování pomocí služby Azure AD, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **organizační diagram nyní** Application Integration vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující krok:

    ![Informace o jednotném přihlašování v doménovém diagramu a adresách URL](common/idp-identifier.png)

    Do textového pole **identifikátor** zadejte adresu URL:  `https://sso2.orgchartnow.com`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![image](common/both-preintegrated-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` je **identifikátor Azure AD** zkopírovaný z oddílu **nastavit organizační diagram nyní** , popsaný dále v kurzu.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavit organizační diagram nyní** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-orgchart-now-single-sign-on"></a>Konfigurovat nyní jeden Sign-On organizačního diagramu

Chcete-li konfigurovat jednotné přihlašování na **organizačním diagramu** , je třeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [organizačního diagramu, který nyní podporuje tým](mailto:ocnsupport@officeworksoftware.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména****brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k organizačnímu diagramu hned.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **organizační diagram nyní**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **organizační diagram nyní**.

    ![Odkaz organizační diagram nyní v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-orgchart-now-test-user"></a>Vytvořit testovacího uživatele v současnosti

Aby se uživatelé Azure AD mohli teď přihlašovat k organizačnímu diagramu, musí se teď zřídit v organizačním diagramu. 

1. Organizační diagram teď podporuje zřizování za běhu, které je ve výchozím nastavení povolené. Nový uživatel se vytvoří během pokusu o přístup k organizačnímu diagramu, pokud ještě neexistuje. Funkce zřizování uživatelů za běhu vytvoří pouze uživatele jen **pro čtení** , když požadavek SSO přijde z rozpoznaného IDP a e-mailová adresa v kontrolním výrazu SAML se nenalezne v seznamu uživatelů. Pro tuto funkci automatického zřizování musíte nyní vytvořit skupinu přístupu s názvem **Obecné** v organizačním diagramu. Chcete-li vytvořit skupinu přístupu, postupujte podle následujících kroků:

    a. Po kliknutí na **ozubené kolečko** v pravém horním rohu uživatelského rozhraní vyberte možnost **Spravovat skupiny** .

    ![Skupiny organizačních diagramů teď](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Vyberte ikonu **Přidat** a pojmenujte skupinu **Obecné** a pak klikněte na **OK**. 

    ![Přidat organizační diagram nyní](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Vyberte složky, které mají mít přístup k uživatelům, kteří mají přístup pro obecné nebo jen pro čtení:

    ![Složky organizačního diagramu nyní](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zamkněte** složky tak, aby je mohli upravovat jenom uživatelé s oprávněními správce. Pak stiskněte **OK**.

    ![Nyní zamknout organizační diagram](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Chcete-li vytvořit uživatele **správce** a uživatele **pro čtení a zápis** , je nutné ručně vytvořit uživatele, aby bylo možné získat přístup ke své úrovni oprávnění prostřednictvím jednotného přihlašování. Chcete-li zřídit uživatelský účet, proveďte následující kroky:

    a. Přihlaste se k organizačnímu diagramu hned jako správce zabezpečení.

    b.  V pravém horním rohu klikněte na **Nastavení** a potom přejděte ke **správě uživatelů**.

    ![Nastavení organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klikněte na **Přidat** a proveďte následující kroky:

    ![Správa organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Do textového pole **ID uživatele** zadejte ID uživatele, například **brittasimon \@ contoso.com**.

    * Do textového pole **e-mailová adresa** zadejte e-maily uživatele, jako je **brittasimon \@ contoso.com**.

    * Klikněte na **Přidat**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici organizační diagram nyní na přístupovém panelu, měli byste být automaticky přihlášeni k organizačnímu diagramu nyní, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)