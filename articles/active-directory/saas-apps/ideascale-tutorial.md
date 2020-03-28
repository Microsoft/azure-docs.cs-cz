---
title: 'Kurz: Integrace služby Azure Active Directory s IdeaScale | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81594e6a21372f2b4dacedbda638cc87bad966db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227568"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Kurz: Integrace Azure Active Directory s IdeaScale

V tomto kurzu se dozvíte, jak integrovat IdeaScale s Azure Active Directory (Azure AD).
Integrace IdeaScale s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k IdeaScale.
* Můžete povolit uživatelům, aby se automaticky přihlásili k IdeaScale (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s IdeaScale, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením ideascale

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* IdeaScale podporuje **sp** iniciované sso

## <a name="adding-ideascale-from-the-gallery"></a>Přidání IdeaScale z galerie

Chcete-li nakonfigurovat integraci IdeaScale do Azure AD, musíte přidat IdeaScale z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat IdeaScale z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **IdeaScale**, z panelu výsledků vyberte **IdeaScale** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![IdeaScale v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí IdeaScale na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v IdeaScale.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí IdeaScale, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace ideascale jednotného přihlášení](#configure-ideascale-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte test IdeaScale uživatele](#create-ideascale-test-user)** – mít protějšek Britta Simon v IdeaScale, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí IdeaScale, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **IdeaScale** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení domény ideascale a adres URL](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.ideascale.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:
    
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta IdeaScale](https://support.ideascale.com/) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V části **Nastavit IdeaScale** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-ideascale-single-sign-on"></a>Konfigurace jednotného přihlašování ideascale

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti IdeaScale jako správce.

2. Přejděte do **nastavení komunity**.

    ![Nastavení komunity](./media/ideascale-tutorial/ic790847.png "Nastavení komunity")

3. Přejděte do **nastavení \> jednotného přihlašování zabezpečení**.

    ![Nastavení jednotného přihlášení](./media/ideascale-tutorial/ic790848.png "Nastavení jednotného přihlášení")

4. Jako **typ jednotného přihlášení**vyberte možnost **SAML 2.0**.

    ![Typ jednotného přihlášení](./media/ideascale-tutorial/ic790849.png "Typ jednotného přihlášení")

5. V dialogovém okně **Nastavení jednotného přihlášení** proveďte následující kroky:

    ![Nastavení jednotného přihlášení](./media/ideascale-tutorial/ic790850.png "Nastavení jednotného přihlášení")

    a. V textovém poli **ID entity ID id SAML** vložte hodnotu **identifikátoru Azure Ad,** kterou jste zkopírovali z webu Azure Portal.

    b. Otevřete stažený soubor metadat z portálu Azure do poznámkového bloku, zkopírujte jeho obsah a vložte do textového pole **metadat idp SAML.**

    c. Do textového pole **Url odhlášení úspěchu** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    d. Klikněte na **Save Changes** (Uložit změny).

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k IdeaScale.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **IdeaScale**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **IdeaScale**.

    ![Odkaz IdeaScale v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-ideascale-test-user"></a>Vytvořit testovacího uživatele IdeaScale

Chcete-li povolit uživatelům Azure AD k přihlášení do IdeaScale, musí být zřízena do IdeaScale. V případě IdeaScale zřizování je ruční úlohy.

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k webu společnosti **IdeaScale** jako správce.

2. Přejděte do **nastavení komunity**.

    ![Nastavení komunity](./media/ideascale-tutorial/ic790847.png "Nastavení komunity")

3. Přejděte na **základní nastavení \> správy členů**.

4. Klepněte na tlačítko **Přidat člena**.

    ![Vedení členských států](./media/ideascale-tutorial/ic790852.png "Vedení členských států")

5. V části Přidat nový člen proveďte následující kroky:

    ![Přidat nového člena](./media/ideascale-tutorial/ic790853.png "Přidat nového člena")

    a. Do textového pole **E-mailové adresy** zadejte e-mailovou adresu platného účtu Azure AD, který chcete zřídit.

    b. Klikněte na **Save Changes** (Uložit změny).

    > [!NOTE]
    > Držitel účtu Azure Active Directory získá e-mail s odkazem pro potvrzení účtu před tím, než se stane aktivním.

> [!NOTE]
> Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů IdeaScale nebo rozhraní API poskytované IdeaScale zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici IdeaScale na přístupovém panelu, můžete by měl být automaticky přihlášeni k IdeaScale, u kterého nastavíte přiřazování kšerek. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

