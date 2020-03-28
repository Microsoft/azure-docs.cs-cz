---
title: 'Kurz: Integrace služby Azure Active Directory s přenosem obrázků | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a přenosem obrázků.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd0637a632b277eae019ac4aebfbc7cdb87e8e2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158969"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Kurz: Integrace služby Azure Active Directory s přenosem obrázků

V tomto kurzu se dozvíte, jak integrovat image relay s Azure Active Directory (Azure AD).
Integrace přenosu bitových obrázků s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k image relay.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě Image Relay (Single Sign-On) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s image relay, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Relé obrazu podporuje **sp** inicioval sso

## <a name="adding-image-relay-from-the-gallery"></a>Přidání relé obrazu z galerie

Chcete-li nakonfigurovat integraci image relay do Azure AD, musíte přidat image relay z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat relé obrazu z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Relé obrazu**, z panelu výsledků vyberte **Relé obrazu** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![Relé obrazu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí relé bitové kopie na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v aplikaci Image Relay.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí relé image, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení přenosu obrazu](#configure-image-relay-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit testovacího uživatele relé image](#create-image-relay-test-user)** – mít protějšek Britta Simon v image relay, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování azure ad pomocí image relay, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikace **Image Relay** možnost I **Single sign-on**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednom přihlášení do domény přenosu obrázků a adres URL](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.imagerelay.com/`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta image relay.](http://support.imagerelay.com/) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit relé obrazu** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-image-relay-single-sign-on"></a>Konfigurace jednotného přihlašování k přenosu obrazu

1. V jiném okně prohlížeče se přihlaste k webu společnosti Image Relay jako správce.

2. Na panelu nástrojů v horní části klikněte na **úlohu Uživatelé & oprávnění.**

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Klepněte na **tlačítko Vytvořit nové oprávnění**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. V zatížení **Nastavení jednotného přihlášení** zaškrtněte políčko **Tato skupina se může přihlásit jenom prostřednictvím jednotného přihlášení** a pak klikněte na **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Přejděte na **Nastavení účtu**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Přejděte na úlohu **Nastavení jednotného přihlášení.**

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. V dialogovém okně **Nastavení SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. V textovém poli **Přihlašovací adresa URL** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal.

    b. Do textového pole **Adresa URL odhlášení** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    c. Jako **formát Id názvu**vyberte **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. Jako **možnosti vazby pro požadavky od poskytovatele služeb (image relay)** vyberte **možnost POST Binding**.

    e. V části **Certifikát x.509**klepněte na tlačítko **Aktualizovat certifikát**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte obsah a vložte jej do textového pole **certifikátu x.509.**

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. V části **Zřizování uživatelů just-in-time** vyberte **možnost Povolit zřizování uživatelů za chvíli**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Vyberte skupinu oprávnění (například **Jednotné přihlašování Basic),** která se může přihlásit pouze prostřednictvím jednotného přihlášení.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k image relay.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Relé image**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **možnost Relé obrazu**.

    ![Odkaz Relé obrazu v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-image-relay-test-user"></a>Vytvořit uživatele testu relé obrazu

Cílem této části je vytvořit uživatele s názvem Britta Simon v relé obrazu.

**Chcete-li vytvořit uživatele s názvem Britta Simon v relé obrazu, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Image Relay jako správce.

2. Přejděte na **Uživatelé & oprávnění** a vyberte **Vytvořit uživatele přihlašování .**

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Zadejte **e-mail**, **jméno**, **příjmení**a **společnost** uživatele, kterého chcete zřídit, a vyberte skupinu oprávnění (například Jednotné přihlašování basic), což je skupina, která se může přihlásit pouze prostřednictvím jednotného přihlašování.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Klikněte na **Vytvořit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici relé obrazu na přístupovém panelu, můžete by měl být automaticky přihlášeni k relé obrazu, pro které nastavíte přiřazovací ho. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)