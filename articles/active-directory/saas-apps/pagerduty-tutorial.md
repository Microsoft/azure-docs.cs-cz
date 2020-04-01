---
title: 'Kurz: Integrace služby Azure Active Directory se službou PagerDuty | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b7a522aaf35303bbd87e7aafe65b1302f1b98bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095336"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Kurz: Integrace Služby Azure Active Directory se službou PagerDuty

V tomto kurzu se dozvíte, jak integrovat PagerDuty s Azure Active Directory (Azure AD).
Integrace PagerDuty s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k PagerDuty.
* Můžete povolit uživatelům, aby se automaticky přihlásili k PagerDuty (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí služby PagerDuty, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením PagerDuty

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* PagerDuty podporuje **sp** inicioval asociaci služeb při .

## <a name="adding-pagerduty-from-the-gallery"></a>Přidání PagerDuty z galerie

Chcete-li nakonfigurovat integraci PagerDuty do Azure AD, musíte přidat PagerDuty z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat PagerDuty z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **PagerDuty**, vyberte **PagerDuty** z panelu výsledků a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![PagerDuty v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby PagerDuty na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v PagerDuty.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby PagerDuty, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace pagerduty jednotného přihlášení](#configure-pagerduty-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte pagerduty test uživatele](#create-pagerduty-test-user)** – mít protějšek Britta Simon v PagerDuty, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby PagerDuty, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **PagerDuty** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![PagerDuty Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<tenant-name>.pagerduty.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta PagerDuty](https://www.pagerduty.com/support/) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit PagerDuty** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-pagerduty-single-sign-on"></a>Konfigurace jednotného přihlašování PagerDuty

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Pagerduty jako správce.

2. V horní nabídce klikněte na **Nastavení účtu**.

    ![Nastavení účtu](./media/pagerduty-tutorial/ic778535.png "Nastavení účtu")

3. Klepněte **na položku Jednotné přihlášení**.

    ![Jednotné přihlašování](./media/pagerduty-tutorial/ic778536.png "Jednotné přihlašování")

4. Na stránce **Povolit jednotné přihlašování (SSO)** proveďte následující kroky:

    ![Povolení jednotného přihlašování](./media/pagerduty-tutorial/ic778537.png "Povolení jednotného přihlašování")

    a. Otevřete svůj certifikát kódovaný ze základní ho 64 stažený z portálu Azure v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Certifikát X.509**
  
    b. Do textového pole **Přihlašovací adresa URL** vložte přihlašovací adresu **URL,** kterou jste zkopírovali z webu Azure Portal.
  
    c. Do textového pole **Adresa URL odhlášení** vložte **adresu URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    d. Vyberte **Povolit přihlášení k uživatelskému jménu/heslu**.

    e. Zaškrtněte políčko **Vyžadovat porovnání kontextu ověřování EXACT.**

    f. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole**brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu pagerduty.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **PagerDuty**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **PagerDuty**.

    ![Odkaz PagerDuty v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-pagerduty-test-user"></a>Vytvořit uživatele testu PagerDuty

Chcete-li povolit uživatelům Azure AD k přihlášení k PagerDuty, musí být zřízeny do PagerDuty.  
V případě PagerDuty zřizování je ruční úlohy.

>[!NOTE]
>Ke zřizování uživatelských účtů Azure Active Directory můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Pagerduty nebo rozhraní API poskytovaná službou Pagerduty.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k tenantovi **Pagerduty.**

2. V nabídce v horní části klikněte na **Uživatelé**.

3. Klepněte na tlačítko **Přidat uživatele**.
   
    ![Přidat uživatele](./media/pagerduty-tutorial/ic778539.png "Přidat uživatele")

4.  V dialogovém okně **Pozvat svůj tým** proveďte následující kroky:
   
    ![Pozvěte svůj tým](./media/pagerduty-tutorial/ic778540.png "Pozvěte svůj tým")

    a. Zadejte **jméno a příjmení** uživatele, jako je **Britta Simon**. 
   
    b. Zadejte **e-mailovou** adresu uživatele, jako **je brittasimon\@contoso.com**.
   
    c. Klikněte na **Přidat**a potom klikněte na **Odeslat pozvánky**.
   
    >[!NOTE]
    >Všichni připočtení uživatelé obdrží pozvánku k vytvoření účtu PagerDuty.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici PagerDuty na přístupovém panelu, můžete by měl být automaticky přihlášeni k PagerDuty, u kterého nastavíte přistupující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

