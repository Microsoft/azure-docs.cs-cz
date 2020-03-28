---
title: 'Kurz: Integrace služby Azure Active Directory s kudosem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Kudos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 1fb1a1bc7bfd8b3cc9d7758bf8e80d8759f9357e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227483"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Kurz: Integrace služby Azure Active Directory s kudosem

V tomto kurzu se dozvíte, jak integrovat kudos s Azure Active Directory (Azure AD).
Integrace kudos s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke kudos.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke kudosu (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Kudos, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením kudos

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kudos podporuje **SP** inicioval SSO

## <a name="adding-kudos-from-the-gallery"></a>Přidání slávy z galerie

Chcete-li nakonfigurovat integraci Kudos do Azure AD, musíte přidat Kudos z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat kudos z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Kudos**, z panelu výsledků vyberte **Kudos** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Sláva v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí kudosu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Kudos.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí kudosu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení Kudos](#configure-kudos-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Kudos](#create-kudos-test-user)** – chcete-li mít protějšek Britta Simon v Kudos, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí kudosu, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikací **Kudos** **možnost Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení domény kudos a adresy URL](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<company>.kudosnow.com`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Obraťte [se na tým podpory klienta Kudos](http://success.kudosnow.com/home) získat hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit kudos** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-kudos-single-sign-on"></a>Konfigurace jednotného přihlašování kudos

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Kudos jako správce.

1. V horní nabídce klikněte na **ikonu Nastavení**.

    ![Nastavení](./media/kudos-tutorial/ic787806.png "Nastavení")

1. Klikněte na **integrace > přiřazuje k sociálnímu prostředí** a proveďte následující kroky:

    ![Jednotné přihlašování](./media/kudos-tutorial/ic787807.png "Jednotné přihlašování")

    a. V textovém poli **Přihlašovací adresa URL** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal.

    b. Otevřete svůj certifikát kódovaný base-64 v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte jej do textového pole **certifikátu X.509**

    c. Do textového pole **Odhlášení do adresy URL** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    d. Do textového pole **Vaše adresa URL kudos** zadejte název společnosti.

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
  
    b. V poli **Uživatelské jméno** typ pole`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu kudos.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Kudos**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Kudos**.

    ![Odkaz Kudos v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-kudos-test-user"></a>Vytvořit uživatele testu Kudos

Aby bylo možné povolit uživatelům Azure AD k přihlášení ke kudos, musí být zřízena do Kudos. V případě Kudos zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti **Kudos** jako správce.

1. V horní nabídce klikněte na **ikonu Nastavení**.

   ![Nastavení](./media/kudos-tutorial/ic787806.png "Nastavení")

1. Klikněte na **Správce uživatelů**.

1. Klikněte na kartu **Uživatelé** a potom **klikněte**na Přidat uživatele .

   ![Správce uživatelů](./media/kudos-tutorial/ic787809.png "Správce uživatelů")

1. V části **Přidat uživatele** proveďte následující kroky:

    ![Přidání uživatele](./media/kudos-tutorial/ic787810.png "Přidání uživatele")

    a. Zadejte **jméno**, **příjmení**, **e-mail** a další podrobnosti o platném účtu služby Azure Active Directory, který chcete zřídit do souvisejících textových polí.

    b. Klepněte na **tlačítko Vytvořit uživatele**.

> [!NOTE]
> Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Kudos nebo rozhraní API poskytované Kudos zřídit uživatelské účty Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Kudos na přístupovém panelu, můžete by měl být automaticky přihlášeni k Kudos, u kterého nastavíte přistupující ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
