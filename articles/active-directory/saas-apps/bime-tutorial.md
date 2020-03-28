---
title: 'Kurz: Integrace služby Azure Active Directory s Bime | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Bime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7774fd55a64df198a9b025a67ce377deebefe8fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232074"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Kurz: Integrace služby Azure Active Directory s Bime

V tomto kurzu se dozvíte, jak integrovat Bime s Azure Active Directory (Azure AD).
Integrace Bime s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Bime.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Bime (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Bime, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Bime

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Bime podporuje **sp** inicioval SSO

## <a name="adding-bime-from-the-gallery"></a>Přidání Bime z galerie

Pokud chcete nakonfigurovat integraci Bime do Azure AD, musíte přidat Bime z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Bime z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Bime**, z panelu výsledků vyberte **Bime** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Bime v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Bime na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Bime.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Bime, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace bime jednotného přihlášení](#configure-bime-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Bime](#create-bime-test-user)** – chcete-li mít protějšek Britta Simon v Bime, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí Bime, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace **bime** aplikací vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Bime Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<tenant-name>.Bimeapp.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<tenant-name>.Bimeapp.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta Bime](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-) a získejte tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. V části **Podpisový certifikát SAML** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Podpisový certifikát SAML.**

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **Podpisový certifikát SAML** zkopírujte **thumbprint** a uložte jej do počítače.

    ![Kopírovat kryptografický otisk, hodnota](common/copy-thumbprint.png)

7. V části **Nastavit Bime** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-bime-single-sign-on"></a>Konfigurace jednotného přihlašování Bime

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Bime jako správce.

2. Na panelu nástrojů klikněte na **Admin**a potom na **Účet**.
  
    ![Správce](./media/bime-tutorial/ic775558.png "Správce")

3. Na stránce konfigurace účtu proveďte následující kroky:
  
    ![Konfigurace jednotného přihlašování](./media/bime-tutorial/ic775559.png "Konfigurace jednotného přihlašování")

    a. Vyberte **možnost Povolit ověřování SAML**.

    b. V textovém poli **Adresa URL vzdáleného přihlášení** vložte hodnotu přihlašovací adresy **URL**, kterou jste zkopírovali z webu Azure Portal.

    c. Do textového pole **Otisk prstu certifikátu** vložte hodnotu **THUMBPRINT,** kterou jste zkopírovali z webu Azure Portal.

    d. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Bime.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Bime**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Bime**.

    ![Odkaz Bime v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-bime-test-user"></a>Vytvořit uživatele bime testu

Aby bylo možné povolit uživatelům Azure AD k přihlášení k Bime, musí být zřízena do Bime. V případě Bime zřizování je ruční úlohy.

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **Bime.**

2. Na panelu nástrojů klepněte na **položku Správce**a potom na **položku Uživatelé**.

    ![Správce](./media/bime-tutorial/ic775561.png "Správce")

3. V **seznamu Uživatelé**klikněte na **Přidat nového uživatele** ("+").

    ![Uživatelé](./media/bime-tutorial/ic775562.png "Uživatelé")

4. Na stránce Dialogové **okno Podrobnosti o uživateli** proveďte následující kroky:

    ![Podrobnosti o uživateli](./media/bime-tutorial/ic775563.png "Podrobnosti o uživateli")

    a. Do textového pole **Jméno** zadejte křestní jméno uživatele, jako **je Britta**.

    b. Do textového pole **Příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. Do textového pole **E-mail** zadejte e-mail uživatele, jako **\@je brittasimon contoso.com**.

    d. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Bime nebo rozhraní API poskytované Bime zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Bime na přístupovém panelu, měli byste být automaticky přihlášeni k Bime, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

