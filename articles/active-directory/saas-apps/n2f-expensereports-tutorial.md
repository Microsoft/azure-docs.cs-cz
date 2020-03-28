---
title: 'Kurz: Integrace služby Azure Active Directory se sestavami výdajů N2F – výdaje | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a N2F – vyúčtování výdajů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161282"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Kurz: Integrace služby Azure Active Directory se sestavami výdajů N2F – výdaje

V tomto kurzu se dozvíte, jak integrovat Sestavy výdajů N2F – výdaje s Azure Active Directory (Azure AD).
Integrace N2F – sestavy výdajů s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k N2F – vyúčtování výdajů.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k N2F – vyúčtování výdajů (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD se sestavami výdajů N2F – výdaje, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* N2F - Vyúčtování výdajů jedno přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* N2F - Vyúčtování výdajů podporuje **SP** a **IDP** iniciované spo.

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Přidání N2F - Vyúčtování výdajů z galerie

Chcete-li nakonfigurovat integraci sestav Výdajů N2F – výdaje do Azure AD, je třeba přidat N2F – vyúčtování výdajů z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat sestavy N2F - Expense reporty z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **N2F - Expense reports**, vyberte **N2F - Expense reports** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![N2F - Vyúčtování výdajů v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí sestav n2F – výdajů na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v N2F – vyúčtování výdajů.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí zpráv o výdajích N2F – výdaje, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace N2F - Sestavy výdajů jednotné přihlášení](#configure-n2f---expense-reports-single-sign-on)** - ke konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit N2F - vyúčtování test uživatele](#create-n2f---expense-reports-test-user)** – mít protějšek Britta Simon v N2F – sestavy výdajů, který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí sestav výdajů N2F – výdaje, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **N2F – výdaje** vyberte možnost Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML,** pokud chcete nakonfigurovat aplikaci v režimu iniciovaném **IDP,** uživatel nemusí provádět žádné kroky, protože aplikace je již předem integrovaná s Azure.

    ![N2F - Vykazuje vyúčtování domén a adres URL jednotné přihlašovací informace](common/preintegrated.png)

5. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![N2F - Vykazuje vyúčtování domén a adres URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL:`https://www.n2f.com/app/`

6. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na tlačítko Kopírovat, chcete-li zkopírovat **adresu URL metadat federace aplikací** a uložit ji do počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

7. V části **Nastavit myPolicies** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurace N2F - Sestavy výdajů jednotné ho přihlášení

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti N2F – vyúčtování výdajů jako správce.

2. Klikněte na **Nastavení** a v rozevíracím souboru vyberte **Přestoupit nastavení.**

    ![N2F - Konfigurace vyúčtování výdajů](./media/n2f-expensereports-tutorial/configure1.png)

3. Vyberte kartu **Nastavení účtu.**

    ![N2F - Konfigurace vyúčtování výdajů](./media/n2f-expensereports-tutorial/configure2.png)

4. Vyberte **Ověřování** a pak vyberte + Přidat kartu **metody ověřování.**

    ![N2F - Konfigurace vyúčtování výdajů](./media/n2f-expensereports-tutorial/configure3.png)

5. Jako metodu ověřování vyberte **možnost SAML Microsoft Office 365.**

    ![N2F - Konfigurace vyúčtování výdajů](./media/n2f-expensereports-tutorial/configure4.png)

6. V části **Metoda ověřování** proveďte následující kroky:

    ![N2F - Konfigurace vyúčtování výdajů](./media/n2f-expensereports-tutorial/configure5.png)

    a. V textovém poli **ID entity** vložte hodnotu **Identifikátor Azure AD,** kterou jste zkopírovali z webu Azure Portal.

    b. Do textového pole **Url metadat** vložte hodnotu URL metadat **federace aplikací,** kterou jste zkopírovali z portálu Azure.

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k N2F – vyúčtování výdajů.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Sestavy **výdajů N2F – Výdaje**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **N2F - Vyúčtování výdajů**.

    ![Odkaz N2F - Vyúčtování výdajů v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-n2f---expense-reports-test-user"></a>Vytvořit N2F - Vyúčtování výdajů testovací ho uživatele

Chcete-li povolit uživatelům Azure AD přihlásit se k N2F – vyúčtování výdajů, musí být zřízena do sestavy výdajů N2F – výdaje. V případě N2F - vyúčtování výdajů zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti N2F - Expense reports jako správce.

2. Klikněte na **Nastavení** a v rozevíracím souboru vyberte **Přestoupit nastavení.**

    ![N2F - Přidání uživatele výdajů](./media/n2f-expensereports-tutorial/configure1.png)

3. Na levém navigačním panelu vyberte kartu **Uživatelé.**

    ![N2F - Konfigurace vyúčtování výdajů](./media/n2f-expensereports-tutorial/user1.png)

4. Vyberte **+ Karta Nový uživatel.**

    ![N2F - Konfigurace vyúčtování výdajů](./media/n2f-expensereports-tutorial/user2.png)

5. V části **Uživatel** proveďte následující kroky:

    ![N2F - Konfigurace vyúčtování výdajů](./media/n2f-expensereports-tutorial/user3.png)

    a. Do textového pole **E-mailová adresa** zadejte e-mailovou adresu uživatele, jako **je\@brittasimon contoso.com**.

    b. Do textového pole **Jméno** zadejte křestní jméno uživatele, jako **je Britta**.

    c. Do textového pole **Název** zadejte jméno uživatele, jako je **BrittaSimon**.

    d. Zvolte **Role, Přímý manažer (N+1)** a **Divize** podle požadavků vaší organizace.

    e. Klikněte na **Ověřit a odeslat pozvánku**.

    > [!NOTE]
    > Pokud se při přidávání uživatele potýkáte s nějakými problémy, obraťte se na [tým podpory N2F - Expense reports](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici N2F – vyúčtování výdajů na přístupovém panelu, měli byste být automaticky přihlášeni k N2F – vyúčtování výdajů, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

