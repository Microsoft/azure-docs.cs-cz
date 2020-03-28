---
title: 'Kurz: Integrace služby Azure Active Directory s plátnem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a canvas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ce5cff0de2939c25400d1d63138b23bc6c9822
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232033"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Kurz: Integrace služby Azure Active Directory s plátnem

V tomto kurzu se dozvíte, jak integrovat základní stránky s Azure Active Directory (Azure AD).
Integrace základní stránky s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k canvas.
* Můžete povolit uživatelům, aby se automaticky přihlásili k canvas (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s plátnem, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením na základní plátno

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Základní stránka podporuje **sp** inicioval sso

## <a name="adding-canvas-from-the-gallery"></a>Přidání plátna z galerie

Pokud chcete nakonfigurovat integraci základní stránky do Azure AD, musíte přidat základní stránku z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat základní stránku z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Základní stránka**, z panelu výsledků vyberte **Základní stránka** a pak kliknutím na Tlačítko **Přidat** přidejte aplikaci.

     ![Základní stránka v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí základní hodu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem na základní stránce.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí základní stránky, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování na plátně](#configure-canvas-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit plátno testovací ho uživatele](#create-canvas-test-user)** – mít protějšek Britta Simon v plátně, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí základní stránky, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Canvas** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o službě Canvas Domain a URL single sign-on](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<tenant-name>.instructure.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta základní stránky.](https://community.canvaslms.com/community/help) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. V části **Podpisový certifikát SAML** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Podpisový certifikát SAML.**

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **Podpisový certifikát SAML** zkopírujte **thumbprint** a uložte jej do počítače.

    ![Kopírovat kryptografický otisk, hodnota](common/copy-thumbprint.png)

7. V části **Nastavit základní stránku** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-canvas-single-sign-on"></a>Konfigurace jednotného přihlášení na plátně

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Canvas jako správce.

2. Přejděte na **Kurzy \> spravované účty \> Microsoft**.

    ![Plátno](./media/canvas-lms-tutorial/ic775990.png "Plátno")

3. V navigačním podokně vlevo vyberte **Možnost Ověřování**a klepněte na tlačítko Přidat **novou konfiguraci SAML**.

    ![Ověřování](./media/canvas-lms-tutorial/ic775991.png "Ověřování")

4. Na stránce Aktuální integrace proveďte následující kroky:

    ![Současná integrace](./media/canvas-lms-tutorial/ic775992.png "Současná integrace")

    a. V textovém poli **ID Entity ID id id id** id vložte hodnotu **identifikátoru Azure Ad,** které jste zkopírovali z webu Azure Portal.

    b. Do textového pole **Přihlašovací adresa URL** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal .

    c. Do textového pole **Odhlásit adresu URL** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    d. V textovém poli **Změnit odkaz na heslo** vložte hodnotu Změnit adresu URL **hesla,** kterou jste zkopírovali z webu Azure Portal.

    e. V textovém poli **Otisk prstu certifikátu** vložte hodnotu **kryptografický otisk** certifikátu, který jste zkopírovali z webu Azure Portal.

    f. V seznamu **Atribut přihlášení** vyberte **NameID**.

    g. Ze seznamu **Formát identifikátoru** vyberte **položku emailAddress**.

    h. Klepněte na **tlačítko Uložit nastavení ověřování**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k základní stránce.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Základní **stránka**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **základní stránka**.

    ![Odkaz Plátno v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-canvas-test-user"></a>Vytvořit uživatele testu plátna

Chcete-li povolit uživatelům Azure AD k přihlášení k základní stránce, musí být zřízena do základní stránky. V případě základní stránky zřizování uživatele je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k tenantovi **základní stránky.**

2. Přejděte na **Kurzy \> spravované účty \> Microsoft**.

   ![Plátno](./media/canvas-lms-tutorial/ic775990.png "Plátno")

3. Klepněte na **položku Uživatelé**.

   ![Uživatelé](./media/canvas-lms-tutorial/ic775995.png "Uživatelé")

4. Klepněte na tlačítko **Přidat nového uživatele**.

   ![Uživatelé](./media/canvas-lms-tutorial/ic775996.png "Uživatelé")

5. Na stránce Dialogové okno Přidat nového uživatele proveďte následující kroky:

   ![Přidání uživatele](./media/canvas-lms-tutorial/ic775997.png "Přidání uživatele")

   a. Do textového pole **Celé jméno** zadejte jméno uživatele, jako je **BrittaSimon**.

   b. Do textového pole **E-mail** zadejte e-mail uživatele, jako **\@je brittasimon contoso.com**.

   c. Do textového pole **Přihlášení** zadejte e-mailovou adresu Azure AD uživatele, jako **je\@brittasimon contoso.com**.

   d. Vyberte **možnost Odeslat uživateli e-mail em o vytvoření tohoto účtu**.

   e. Klikněte na **Add User** (Přidat uživatele).

> [!NOTE]
> Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů canvas nebo rozhraní API poskytované základní hodu k zřízení uživatelských účtů Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Základní stránka na přístupovém panelu, měli byste být automaticky přihlášeni k základní stránce, pro kterou nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

