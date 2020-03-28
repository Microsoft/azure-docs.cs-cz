---
title: 'Kurz: Integrace služby Azure Active Directory s uživatelem Humanity | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a Humanity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd7cfe93aa39982836944230f860c603c7117ceb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233435"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Kurz: Integrace služby Azure Active Directory s uživatelem Humanity

V tomto kurzu se dozvíte, jak integrovat humanity s Azure Active Directory (Azure AD).
Integrace humanity s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k humanity.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k lidskosti (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s humanity, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením humanity

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Lidstvo podporuje **SP** inicioval SSO

## <a name="adding-humanity-from-the-gallery"></a>Přidání lidstva z galerie

Chcete-li nakonfigurovat integraci humanity do Azure AD, musíte přidat Humanity z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat humanity z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Humanity**, z panelu výsledků vyberte **Humanity** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Lidstvo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí aplikace Humanity na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v humanity.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí aplikace Humanity, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování lidstva](#configure-humanity-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit humanity test uživatele](#create-humanity-test-user)** – mít protějšek Britta Simon v humanity, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování azure a služby pomocí aplikace Humanity, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikace **Humanity** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Humanity Domain a URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://company.humanity.com/includes/saml/`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://company.humanity.com/app/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se [na tým podpory klienta Humanity,](https://www.humanity.com/support/) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit lidstvo** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-humanity-single-sign-on"></a>Konfigurace jednotného přihlášení lidstva

1. V jiném okně webového prohlížeče se přihlaste na web společnosti **Humanity** jako správce.

2. V horní nabídce klikněte na **Admin**.

    ![Správce](./media/shiftplanning-tutorial/iC786619.png "Správce")
3. V části **Integrace**klikněte na **Jednotné přihlašování**.

    ![Jednotné přihlašování](./media/shiftplanning-tutorial/iC786620.png "Jednotné přihlašování")

4. V části **Jednotné přihlašování** proveďte následující kroky:

    ![Jednotné přihlašování](./media/shiftplanning-tutorial/iC786905.png "Jednotné přihlašování")

    a. Vyberte **možnost SAML Povolená**.

    b. Vyberte **povolit přihlášení k heslu**.

    c. V textovém poli **url vystavitela SAML** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    d. V textovém poli Adresa **URL vzdáleného odhlášení** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    e. Otevřete svůj certifikát kódovaný base-64 v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte jej do textového pole **Certifikát X.509.**

    f. Klepněte na **tlačítko Uložit nastavení**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k lidstvo.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Humanity**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **položku Humanity**.

    ![Odkaz Humanity v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-humanity-test-user"></a>Vytvořit uživatele testu lidskosti

Aby bylo možné povolit uživatelům Azure AD k přihlášení k lidskosti, musí být zřízena do humanity. V případě lidstva zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se na web společnosti **Humanity** jako správce.

2. Klikněte na **Správce**.

    ![Správce](./media/shiftplanning-tutorial/iC786619.png "Správce")

3. Klepněte na **položku Zaměstnanci**.

    ![Zaměstnanci](./media/shiftplanning-tutorial/ic786623.png "Zaměstnanci")

4. V části **Akce**klikněte na **Přidat zaměstnance**.

    ![Přidat zaměstnance](./media/shiftplanning-tutorial/iC786624.png "Přidat zaměstnance")

5. V části **Přidat zaměstnance** proveďte následující kroky:

    ![Uložit zaměstnance](./media/shiftplanning-tutorial/iC786625.png "Uložit zaměstnance")

    a. Zadejte **jméno**, **příjmení**a **e-mail** platného účtu Azure AD, který chcete zřídit do souvisejících textových polí.

    b. Klepněte na tlačítko **Uložit zaměstnance**.

> [!NOTE]
> Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Humanity nebo rozhraní API poskytované humanity k zřízení uživatelských účtů Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Humanity na přístupovém panelu, můžete by měl být automaticky přihlášeni k lidskosti, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)