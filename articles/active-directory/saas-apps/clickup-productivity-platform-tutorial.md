---
title: 'Kurz: Integrace služby Azure Active Directory s platformou ClickUp Productivity Platform | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a platformou ClickUp Productivity Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: f497955b698d7ca390b40686e94d553a7cd5c948
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048768"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Kurz: Integrace Služby Azure Active Directory s platformou ClickUp Productivity Platform

V tomto kurzu se dozvíte, jak integrovat platformu ClickUp Productivity Platform s Azure Active Directory (Azure AD).
Integrace platformy ClickUp Productivity Platform s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k platformě ClickUp Productivity Platform.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k platformě ClickUp Productivity Platform (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí platformy ClickUp Productivity Platform, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením platformy ClickUp Productivity Platform

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ClickUp Produktivita Platform podporuje **SP** inicioval sso

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Přidání platformy ClickUp Productivity Platform z galerie

Chcete-li nakonfigurovat integraci platformy ClickUp Productivity Platform do Azure AD, musíte přidat platformu ClickUp Productivity Platform z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat platformu ClickUp Productivity Platform z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ClickUp Productivity Platform**, z panelu výsledků vyberte **ClickUp Productivity Platform** a pak kliknutím na **tlačítko Přidat** aplikaci přidejte.

     ![Platforma clickup produktivity v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí platformy ClickUp Productivity Platform založené na testovacím uživateli s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v platformě ClickUp Productivity Platform.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí platformy ClickUp Productivity Platform, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování platformy ClickUp Productivity Platform](#configure-clickup-productivity-platform-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu platformy ClickUp Productivity Platform](#create-clickup-productivity-platform-test-user)** – chcete-li mít protějšek Britta Simon v platformě ClickUp Productivity Platform, která je propojená s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí platformy ClickUp Productivity Platform, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **platformy ClickUp Productivity Platform** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![ClickUp Produktivita platformy domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL:`https://app.clickup.com/login/sso`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Hodnota Identifikátor není skutečná. Aktualizujte tuto hodnotu skutečným identifikátorem, který je vysvětlen dále v tomto kurzu.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na tlačítko Kopírovat, chcete-li zkopírovat **adresu URL metadat federace aplikací** a uložit ji do počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Konfigurace jednotného přihlašování platformy ClickUp Productivity Platform

1. V jiném okně webového prohlížeče se přihlaste ke svému tenantovi platformy ClickUp Productivity Platform jako správce.

2. Klikněte na **profil uživatele**a pak vyberte **Nastavení**.

    ![Konfigurace produktivity clickup](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Konfigurace produktivity clickup](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Vyberte **Microsoft**, v části Zprostředkovatel jednotného přihlášení (SSO).

    ![Konfigurace produktivity clickup](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Na stránce **Configure Microsoft Single Sign On** proveďte následující kroky:

    ![Konfigurace produktivity clickup](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Kliknutím na **Kopírovat** zkopírujte hodnotu ID entity a vložte ji do textového pole **Identifikátor (ID entity)** v části **Základní konfigurace SAML** na webu Azure Portal.
    
    b. V textovém poli **URL metadat azure federationu** vložte hodnotu URL metadat federace aplikace, kterou jste zkopírovali z portálu Azure, a klikněte na **Uložit**.

5. Chcete-li dokončit instalaci, klepněte na **tlačítko Ověřit se společností Microsoft a dokončete instalaci** a ověřte pomocí účtu microsoft.

    ![Konfigurace produktivity clickup](./media/clickup-productivity-platform-tutorial/configure4.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu clickup produktivity platformy.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **ClickUp Productivity Platform**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ClickUp Productivity Platform**.

    ![Odkaz Platforma produktivity ClickUp v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-clickup-productivity-platform-test-user"></a>Vytvořit testovacího uživatele platformy ClickUp Productivity Platform

1. V jiném okně webového prohlížeče se přihlaste ke svému tenantovi platformy ClickUp Productivity Platform jako správce.

2. Klikněte na **profil uživatele**a vyberte **lidé**.
   
    ![Konfigurace produktivity clickup](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Konfigurace produktivity clickup](./media/clickup-productivity-platform-tutorial/user1.png)

3. Do textového pole zadejte e-mailovou adresu uživatele a klepněte na **tlačítko Pozvat**.

    ![Konfigurace produktivity clickup](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > Uživatel obdrží oznámení a musí přijmout pozvání k aktivaci účtu.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici ClickUp produktivity platformy na přístupovém panelu, měli byste být automaticky přihlášeni k platformě ClickUp produktivity, pro které nastavíte přiřazovací ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

