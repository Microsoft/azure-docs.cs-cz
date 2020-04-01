---
title: 'Kurz: Integrace služby Azure Active Directory s oddělením podpory Jitbit | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a oddělením technické podpory Jitbit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 29addcd62afd193af83196b2d942e9778ff3f031
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099413"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Kurz: Integrace služby Azure Active Directory s oddělením podpory Jitbit

V tomto kurzu se dozvíte, jak integrovat Jitbit Helpdesk s Azure Active Directory (Azure AD).
Integrace jitbit helpdesku s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Jitbit Helpdesk.
* Můžete povolit uživatelům, aby se automaticky přihlásili k Jitbit Helpdesk (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí služby Jitbit Helpdesk, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením jitbit helpdesk

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jitbit Helpdesk podporuje **sp** inicioval SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Přidání jitbithelpdesku z galerie

Pokud chcete nakonfigurovat integraci Jitbit Helpdesk do Azure AD, je potřeba přidat Jitbit Helpdesk z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat jitbit helpdesk z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Jitbit Helpdesk**, z panelu výsledků vyberte **Jitbit Helpdesk** a potom klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![Jitbit Helpdesk v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jitbithelpdesku na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v oddělení technické podpory Jitbit.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Jitbit Helpdesk, je třeba provést následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování jitbithelpdesku](#configure-jitbit-helpdesk-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu jitbithelpdesku](#create-jitbit-helpdesk-test-user)** – chcete-li mít protějšek Britta Simon v Jitbit Helpdesk, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí služby Jitbit Helpdesk, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Jitbit Helpdesk** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Jitbit Helpdesk Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Tato hodnota není skutečná. Aktualizujte tuto hodnotu skutečnou přihlašovací adresou URL. Chcete-li získat tuto hodnotu, obraťte se na [tým podpory klienta Jitbit Helpdesk.](https://www.jitbit.com/support/)

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL takto:`https://www.jitbit.com/web-helpdesk/`

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Jitbit helpdesk** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurace jednotného přihlašování jitbitového helpdesku

1. V jiném okně webového prohlížeče se přihlaste na firemní web Jitbit Helpdesk jako správce.

1. Na panelu nástrojů v horní části klikněte na **Správa**.

    ![Správa](./media/jitbit-helpdesk-tutorial/ic777681.png "Správa")

1. Klepněte na tlačítko **Obecné nastavení**.

    ![Uživatelé, společnosti a oprávnění](./media/jitbit-helpdesk-tutorial/ic777680.png "Uživatelé, společnosti a oprávnění")

1. V části Konfigurace **nastavení ověřování** proveďte následující kroky:

    ![Nastavení ověřování](./media/jitbit-helpdesk-tutorial/ic777683.png "Nastavení ověřování")

    a. Vyberte **Povolit jednotné přihlašování SAML 2.0**, chcete-li se přihlásit pomocí jednotného přihlašování pomocí **oneloginu**.

    b. Do textového pole **Adresa URL koncového bodu** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal.

    c. Otevřete svůj certifikát kódovaný **base-64** v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte jej do textového pole **Certifikát X.509**

    d. Klepněte na **tlačítko Uložit změny**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Jitbit Helpdesk.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Jitbit Helpdesk**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Jitbit Helpdesk**.

    ![Odkaz Jitbit Helpdesk v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-jitbit-helpdesk-test-user"></a>Vytvořit uživatele testu jitbithelpdesk

Aby bylo možné uživatelům Azure AD umožnit přihlášení k Jitbit Helpdesk, musí být zřízena do Jitbit Helpdesk. V případě Jitbit Helpdesk zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **Jitbit Helpdesk.**

1. V nabídce nahoře klikněte na **Správa**.

    ![Správa](./media/jitbit-helpdesk-tutorial/ic777681.png "Správa")

1. Klikněte na **Uživatelé, společnosti a oprávnění**.

    ![Uživatelé, společnosti a oprávnění](./media/jitbit-helpdesk-tutorial/ic777682.png "Uživatelé, společnosti a oprávnění")

1. Klepněte na tlačítko **Přidat uživatele**.

    ![Přidat uživatele](./media/jitbit-helpdesk-tutorial/ic777685.png "Přidání uživatele")

1. V části Vytvořit zadejte data účtu Azure AD, který chcete zřídit, následujícím způsobem:

    ![Vytvořit](./media/jitbit-helpdesk-tutorial/ic777686.png "Vytvořit")

   a. Do textového pole **Uživatelské jméno** zadejte uživatelské jméno uživatele, jako je **BrittaSimon**.

   b. Do textového pole **E-mail** zadejte e-mail uživatele jako . **BrittaSimon@contoso.com**

   c. Do textového pole **Křestní jméno** zadejte křestní jméno uživatele, jako **je Britta**.

   d. Do textového pole **Příjmení** zadejte příjmení uživatele, jako je **Simon**.

   e. Klikněte na **Vytvořit**.

> [!NOTE]
> Ke zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Jitbit Helpdesk nebo rozhraní API poskytovaná oddělením podpory Jitbit.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Jitbit Helpdesk na přístupovém panelu, měli byste být automaticky přihlášeni k jitbit helpdesk, pro které nastavíte sanace. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
