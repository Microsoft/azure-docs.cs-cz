---
title: 'Kurz: Integrace služby Azure Active Directory s targetprocess | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 60cd043c34ba2aee365c59feb38cc9dd02d45746
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089045"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Kurz: Integrace služby Azure Active Directory s targetprocessem

V tomto kurzu se dozvíte, jak integrovat TargetProcess s Azure Active Directory (Azure AD).
Integrace TargetProcess s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TargetProcess.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k TargetProcess (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s TargetProcess, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením TargetProcess

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TargetProcess podporuje **sp** iniciované spři přijíma.
* TargetProcess podporuje zřizování uživatelů **just in time**

## <a name="adding-targetprocess-from-the-gallery"></a>Přidání TargetProcess z galerie

Chcete-li nakonfigurovat integraci TargetProcess do Azure AD, je třeba přidat TargetProcess z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat TargetProcess z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TargetProcess**, z panelu výsledků vyberte **TargetProcess** a klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![TargetProcess v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TargetProcess na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v TargetProcess.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TargetProcess, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace single sign-on targetprocess](#configure-targetprocess-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit TargetProcess testovací ho uživatele](#create-targetprocess-test-user)** – mít protějšek Britta Simon v TargetProcess, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí technologie TargetProcess, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **TargetProcess** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení domény targetprocess a adresy URL](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.tpondemand.com/`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<subdomain>.tpondemand.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte [se na tým podpory klienta TargetProcess](mailto:support@targetprocess.com) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit TargetProcess** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-targetprocess-single-sign-on"></a>Konfigurace jednotného přihlašování targetprocess

1. Chcete-li automatizovat konfiguraci v rámci **targetprocess**, je třeba nainstalovat **my apps secure sign-in rozšíření prohlížeče** kliknutím na nainstalovat **rozšíření**.

    ![image](./media/target-process-tutorial/install_extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení TargetProcess** vás nasměruje do aplikace TargetProcess. Odtud zadejte pověření správce pro přihlášení do TargetProcess. Rozšíření prohlížeče automaticky nakonfiguruje aplikaci pro vás a automatizovat kroky 3-7.

    **Chcete-li aplikaci nakonfigurovat ručně, proveďte následující kroky:**

3. Přihlaste se k aplikaci TargetProcess jako správce.

4. V horní nabídce klepněte na tlačítko **Nastavení**.

    ![Nastavení](./media/target-process-tutorial/tutorial_target_process_05.png)

5. Klikněte na kartu **Nastavení.**

    ![Nastavení](./media/target-process-tutorial/tutorial_target_process_06.png)

6. Klikněte na kartu **Jednotné přihlášení.**

    ![klikněte na Jednotné přihlášení](./media/target-process-tutorial/tutorial_target_process_07.png)

7. V dialogovém okně Nastavení jednotného přihlášení proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/target-process-tutorial/tutorial_target_process_08.png)

    a. Klepněte **na položku Povolit jednotné přihlašování**.

    b. V textovém poli **přihlašovací adresy URL** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal.

    c. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte obsah a vložte ho do textového pole **Certifikát.**

    d. klepněte na **tlačítko Povolit zřizování JIT**.

    e. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k TargetProcess.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **TargetProcess**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **targetprocess**.

    ![Odkaz TargetProcess v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-targetprocess-test-user"></a>Vytvořit testovacího uživatele TargetProcess

V této části je vytvořen uživatel s názvem Britta Simon v TargetProcess. TargetProcess podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v TargetProcess, nový je vytvořen po ověření.

> [!Note]
> Pokud potřebujete vytvořit uživatele ručně, obraťte se [na tým podpory TargetProcess](mailto:support@targetprocess.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici TargetProcess na přístupovém panelu, by měl být automaticky přihlášeni k TargetProcess, pro které nastavíte přispojené k zámesu. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)