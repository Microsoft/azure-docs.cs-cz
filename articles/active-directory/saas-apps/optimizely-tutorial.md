---
title: 'Kurz: integrace s Azure Active Directory s optimalizací | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a optimalizuje.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: c10681a053631a338bd0b9a7d19c5625151a9a5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515793"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Kurz: integrace s Azure Active Directory s optimalizací

V tomto kurzu se naučíte integrovat optimalizaci s Azure Active Directory (Azure AD).
Integrace s Azure AD nabízí následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají k optimalizaci přístup.
* Uživatelům můžete povolit, aby se automaticky přihlásili k optimalizaci (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s optimalizací potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Optimalizuje odběr s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Optimalizace podporuje jednotné přihlašování spouštěné přes **SP**

## <a name="adding-optimizely-from-the-gallery"></a>Optimalizace se optimalizuje z galerie.

Ke konfiguraci integrace optimalizuje do Azure AD je potřeba přidat optimalizaci z Galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat optimalizaci z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **optimalizované**, vyberte **optimalizuje** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Optimalizace v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s optimalizací na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba vytvořit odkazový vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci optimalizace.

Ke konfiguraci a testování jednotného přihlašování Azure AD s optimalizací je potřeba, abyste dokončili tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte optimalizované jednotné přihlašování](#configure-optimizely-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte optimalizuje testovacího uživatele](#create-optimizely-test-user)** , abyste měli protějšek Britta Simon v optimalizuje, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete jednotné přihlašování Azure AD nakonfigurovat s optimalizací, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **optimalizace** integrace aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Optimalizuje informace jednotného přihlašování v doménových a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://app.optimizely.net/<instance name>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tuto hodnotu aktualizujete skutečnou přihlašovací adresou URL a identifikátorem, který je vysvětlen později v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Vaše optimalizovaná aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky, který zobrazuje dialogové okno atributy uživatele se ikonou "Upravit" v pravém horním rohu.](common/edit-attribute.png)

6. Kromě toho aplikace optimalizované pro optimalizaci očekává, že se v odpovědi SAML zpátky vrátí několik atributů. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Name | Zdrojový atribut |
    | ---------------| --------------- |
    | e-mail | uživatel. pošta |
    
    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky, který zobrazuje dialogové okno deklarace identity uživatele se zvýrazněnými akcemi přidat nové deklarace identity a uložit](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut** vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit s optimalizací** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-optimizely-single-sign-on"></a>Konfigurovat optimalizující jednu Sign-On

1. Pokud chcete jednotné přihlašování nakonfigurovat na straně **optimalizace** , obraťte se na svého správce optimalizovaného účtu a poskytněte stažený **certifikát (Base64)** a příslušné zkopírované adresy URL.

2. V reakci na váš e-mail vám optimalizuje i adresu URL pro přihlášení (SSO iniciované nástrojem SP) a identifikátor (ID entity poskytovatele služeb).

    a. Zkopírujte **adresu URL jednotného přihlašování iniciované nástrojem SP** a vložte ji do textového pole **Signing URL** v **základní části Konfigurace SAML** na Azure Portal.

    b. Zkopírujte **ID entity poskytovatele služby** poskytnuté optimalizací a vložte je do textového pole **identifikátoru** v **základní části Konfigurace SAML** na Azure Portal.

3. V jiném okně prohlížeče se přihlaste k optimalizované aplikaci.

4. Klikněte na název účtu v pravém horním rohu a pak na **Nastavení účtu**.

    ![Snímek obrazovky, který zobrazuje název účtu vybraný v pravém horním rohu s názvem "nastavení účtu" vybraným v nabídce](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. Na kartě účet zaškrtněte políčko **Povolit jednotné přihlašování** v části jednotné přihlašování v části **Přehled** .
  
    ![Služba Azure AD Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména****brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k optimalizaci.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **optimalizovat**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **optimalizovat**.

    ![Odkaz optimalizovaného v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-optimizely-test-user"></a>Vytvořit optimalizuje testovacího uživatele

V této části vytvoříte v optimalizuje uživatele s názvem Britta Simon.

1. Na domovské stránce vyberte kartu **spolupracovníci** .

2. Chcete-li do projektu přidat nového spolupracovníka, klikněte na tlačítko **Nový spolupracovníka**.
   
    ![Snímek obrazovky znázorňující domovskou stránku s optimalizovaným spolupracovníky a vybraným tlačítkem nový spolupracovníka.](./media/optimizely-tutorial/create_aaduser_10.png)

3. Zadejte e-mailovou adresu a přiřaďte jim roli. Klikněte na **pozvat**.

    ![Vytvoření testovacího uživatele Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Obdrží pozvání k odeslání e-mailu. Pomocí e-mailové adresy se musí přihlásit k optimalizaci.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici optimalizované k optimalizaci, měli byste být automaticky přihlášeni k optimalizaci, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)