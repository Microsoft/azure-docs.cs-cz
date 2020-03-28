---
title: 'Kurz: Integrace služby Azure Active Directory s bonusy | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9c88f8eb8ac7b72f11ff286d2294df8cb70860
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232061"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Kurz: Integrace služby Azure Active Directory s bonusy

V tomto kurzu se dozvíte, jak bonusly integrovat s Azure Active Directory (Azure AD).
Integrace bonusly s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Bonusly.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě Bonusly (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Bonusly, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Bonusově jednopřihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Bonusově podporuje **idp** iniciované sso

## <a name="adding-bonusly-from-the-gallery"></a>Přidání Bonusly z galerie

Chcete-li nakonfigurovat integraci Bonusly do Azure AD, musíte přidat Bonusly z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Bonusly z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Bonusly**, z panelu výsledků vyberte **Bonusly** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

    ![Bonusně v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí bonusu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Bonusly.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD s Bonusly, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace bonusového jednotného přihlášení](#configure-bonusly-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořit bonusly test uživatele](#create-bonusly-test-user)** – mít protějšek Britta Simon v Bonusly, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí bonusu, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce **Integrace aplikací Bonusly** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Bonusově informace o jedné přihlašovací službě domény a adresy URL](common/idp-reply.png)

    Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou adresou URL pro odpověď. Chcete-li získat hodnotu, obraťte se na [tým podpory klienta Bonusly.](https://bonus.ly/contact) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. V části **Podpisový certifikát SAML** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Podpisový certifikát SAML.**

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **Podpisový certifikát SAML** zkopírujte **thumbprint** a uložte jej do počítače.

    ![Kopírovat kryptografický otisk, hodnota](common/copy-thumbprint.png)

7. V části **Nastavit bonusové** kódy zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-bonusly-single-sign-on"></a>Konfigurace bonusového jednotného přihlašování

1. V jiném okně prohlížeče se přihlaste ke svému **klientovi Bonusly.**

1. Na panelu nástrojů nahoře klikněte na **Nastavení** a potom vyberte **Integrace a aplikace**.

    ![Bonusně sociální sekce](./media/bonus-tutorial/ic773686.png "Bonusly")
1. V části **Jednotné přihlašování**vyberte **možnost SAML**.

1. Na stránce dialogového okna **SAML** proveďte následující kroky:

    ![Bonusně Saml Dialog stránka](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. V textovém poli **cílová adresa URL cíle idp s em-line** vložte hodnotu **přihlašovací adresy URL**, kterou jste zkopírovali z webu Azure Portal.

    b. V textovém poli **Adresa URL přihlášení k ověření přihlašovacího údaje** vložte hodnotu přihlašovací adresy **URL**, kterou jste zkopírovali z webu Azure Portal.

    c. V textovém poli **IdP Issuer** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z webu Azure Portal.
    
    d. Vložte hodnotu **Kryptotisku** zkopírovanou z portálu Azure do textového pole **Otisku prstu certifikátu.**
    
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
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu bonusly.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Bonusly**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **bonusová možnost**.

    ![Bonusový odkaz v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-bonusly-test-user"></a>Vytvořit uživatele testně bonusu

Aby bylo možné uživatelům Azure AD přihlásit se k Bonusly, musí být zřízena do Bonusly. V případě Bonusly zřizování je ruční úlohy.

> [!NOTE]
> Můžete použít jakékoli jiné bonusly nástroje pro vytváření uživatelských účtů nebo rozhraní API poskytované Bonusly zřídit uživatelské účty Azure AD. 

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. V okně webového prohlížeče se přihlaste ke svému klientovi Bonusly.

1. Klikněte na příkaz **Nastavení**.

    ![Nastavení](./media/bonus-tutorial/ic781041.png "Nastavení")

1. Klikněte na kartu **Uživatelé a bonusy.**

    ![Uživatelé a bonusy](./media/bonus-tutorial/ic781042.png "Uživatelé a bonusy")

1. Klepněte na **tlačítko Spravovat uživatele**.

    ![Správa uživatelů](./media/bonus-tutorial/ic781043.png "Správa uživatelů")

1. Klikněte na **Add User** (Přidat uživatele).

    ![Přidání uživatele](./media/bonus-tutorial/ic781044.png "Přidání uživatele")

1. V dialogovém okně **Přidat uživatele** proveďte následující kroky:

    ![Přidání uživatele](./media/bonus-tutorial/ic781045.png "Přidání uživatele")  

    a. Do textového pole **Jméno** zadejte křestní jméno uživatele, jako **je Britta**.

    b. Do textového pole **Příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. Do textového pole **E-mail** zadejte e-mail uživatele, který se líbí `brittasimon\@contoso.com`.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Držitel účtu Azure AD obdrží e-mail, který obsahuje odkaz pro potvrzení účtu před tím, než se stane aktivním.  

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Bonusly na přístupovém panelu, měli byste být automaticky přihlášeni k Bonusly, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
