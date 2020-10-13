---
title: 'Kurz: Azure Active Directory integrace se sestavami N2F-výdajů | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a N2F – sestav výdajů.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 9eee16c1ba207eb35633d7ec1a7167300d40d662
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941004"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Kurz: Azure Active Directory integrací se sestavami N2F-výdajů

V tomto kurzu se naučíte, jak integrovat sestavy N2F-výdajů pomocí Azure Active Directory (Azure AD).
Integrace sestav N2F-výdajů do Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k N2Fm sestavám.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili do N2F sestav (jednotného přihlašování) s účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí sestav N2F-výdajů potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* N2F – odběr sestav s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* N2F – sestavy výdajů podporují **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování.

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Přidávání sestav N2F-výdajů z Galerie

Pokud chcete nakonfigurovat integraci sestav N2F-výdajů do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat N2F sestavy z galerie.

**Chcete-li přidat sestavy N2F-výdajů z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **N2F-výdajové sestavy**, vyberte **N2F – sestavy výdajů** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![N2F – sestavy výdajů v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí sestav N2F-výdaje na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v sestavách N2F-výdaje.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí sestav N2F-výdajů, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace N2F-výdajových sestav jednotné přihlašování](#configure-n2f---expense-reports-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření sestav N2F-výdaje – testovací uživatel](#create-n2f---expense-reports-test-user)** – aby bylo možné Britta Simon v sestavách N2F-výdajů, které jsou propojeny s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí sestav N2F-výdajů, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **sestav N2F-výdaje** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , uživatel v **základní části Konfigurace SAML** neprovede žádné kroky, protože aplikace už je předem integrovaná s Azure.

    ![Snímek obrazovky ukazuje přihlašovací stránku založenou na SAML se základní konfigurací SAML.](common/preintegrated.png)

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky zobrazující stránku integrace, kde můžete přidat Azure A D jednotného přihlašování.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://www.n2f.com/app/`

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

7. V části **Nastavení myPolicies** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurace N2F – sestavy výdajů na jeden Sign-On

1. V jiném okně webového prohlížeče se přihlaste k vašim N2Fm zprávám společnosti jako správce.

2. Klikněte na **Nastavení** a potom z rozevíracího seznamu vyberte **nastavení zálohy** .

    ![Snímek obrazovky zobrazuje vybraná Rozšířená nastavení.](./media/n2f-expensereports-tutorial/configure1.png)

3. Vyberte kartu **Nastavení účtu** .

    ![Snímek obrazovky zobrazuje vybrané nastavení účtu.](./media/n2f-expensereports-tutorial/configure2.png)

4. Vyberte **ověřování** a pak vyberte **+ Přidat kartu metoda ověřování** .

    ![Snímek obrazovky ukazuje ověřování nastavení účtu, kde můžete přidat metodu ověřování.](./media/n2f-expensereports-tutorial/configure3.png)

5. Jako metodu ověřování vyberte **SAML systém Microsoft Office 365** .

    ![Snímek obrazovky zobrazuje metodu ověřování s vybraným SAML systém Microsoft Office 365.](./media/n2f-expensereports-tutorial/configure4.png)

6. V části **metoda ověřování** proveďte následující kroky:

    ![Snímek obrazovky ukazuje metodu ověřování, kde můžete zadat hodnoty, které jsou popsány.](./media/n2f-expensereports-tutorial/configure5.png)

    a. Do textového pole **ID entity** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL metadat** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    c. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k sestavám N2F-výdajů.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **N2F – sestavy výdajů**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **N2F-výdaje sestavy**.

    ![Odkaz sestavy N2F-výdaje v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-n2f---expense-reports-test-user"></a>Vytvoření N2F – sestavy s testovacími náklady pro uživatele

Aby se uživatelé Azure AD mohli přihlásit k N2Fým zprávám, musí se zřídit v sestavách N2F-výdajů. V případě sestav N2F-výdajů je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k vašim N2Fm zprávám společnosti jako správce.

2. Klikněte na **Nastavení** a potom z rozevíracího seznamu vyberte **nastavení zálohy** .

    ![Snímek obrazovky zobrazuje vybraná Rozšířená nastavení.](./media/n2f-expensereports-tutorial/configure1.png)

3. V levém navigačním panelu vyberte kartu **Uživatelé** .

    ![Snímek obrazovky znázorňující vybrané uživatele.](./media/n2f-expensereports-tutorial/user1.png)

4. Vyberte **+ Nová karta uživatel** .

    ![Snímek obrazovky se zobrazí jako možnost Nový uživatel.](./media/n2f-expensereports-tutorial/user2.png)

5. V části **uživatel** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí část, kde můžete zadat hodnoty, které jsou popsány.](./media/n2f-expensereports-tutorial/user3.png)

    a. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu uživatele, jako je **brittasimon \@ contoso.com**.

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta**.

    c. Do textového pole **název** zadejte jméno uživatele, jako je **BrittaSimon**.

    d. Vyberte **role, přímý nadřízený (N + 1)** a **divizi** podle požadavků vaší organizace.

    e. Klikněte na **ověřit a poslat pozvánku**.

    > [!NOTE]
    > Pokud vás při přidávání uživatele čelí nějaké problémy, kontaktujte prosím [tým podpory N2F-výdaje](mailto:support@n2f.com) .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici sestavy N2F-výdaje na přístupovém panelu, měli byste se automaticky přihlásili k sestavám N2F-výdajů, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

