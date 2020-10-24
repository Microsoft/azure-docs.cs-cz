---
title: 'Kurz: Azure Active Directory integrace s Zscaler Internet Access Administrator | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler Internet Access Administrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 0e7e332a807c2a92f0bac2c5c8d8fb421f6903df
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520366"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Kurz: Azure Active Directory integrace s Zscaler Internet Access Administrator

V tomto kurzu se naučíte integrovat Zscaler Internet Access Administrator s Azure Active Directory (Azure AD).
Integrace správce internetového přístupu Zscaler s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k Zscaler správce přístupu k Internetu.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Zscaler správce přístupu k Internetu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD pomocí Správce Zscaler Internet Access potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné správce internetového přístupu Zscaler

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Správce internetového přístupu Zscaler podporuje jednotné přihlašování **IDP** .

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Přidání správce internetového přístupu Zscaler z Galerie

Pokud chcete nakonfigurovat integraci správce internetového přístupu Zscaler do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Zscaler správce internetového přístupu z galerie.

**Pokud chcete přidat správce internetového přístupu Zscaler z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler Internet Access Administrator**, vyberte **Zscaler správce přístupu k Internetu** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Zscaler správce přístupu k Internetu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Zscaler správce internetového přístupu na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler správce internetového přístupu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Správce Zscaler Internet Access, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Zscaler Internet Access Administrator](#configure-zscaler-internet-access-administrator-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele správce přístupu Zscaler k Internetu](#create-zscaler-internet-access-administrator-test-user)** – má protějšek Britta Simon v Zscaler správce internetového přístupu, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Správce Zscaler Internet Access, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **správce internetového přístupu Zscaler** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na tlačítko **Upravit** a otevřete tak základní dialogové okno **Konfigurace SAML** .

    ![Zscaler informace o jednotném přihlašování domén a adres URL správce internetového přístupu](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL dle vašeho požadavku:

    | Identifikátor |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL dle vašeho požadavku:

    | Adresa URL odpovědi |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Aplikace Zscaler pro správce internetového přístupu očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele & deklarace** na stránce integrace aplikací. Na **stránce nastavit jeden Sign-On se stránkou SAML**kliknutím na tlačítko **Upravit** otevřete **atributy uživatele &** dialogovém okně deklarace.

    ![Odkaz na atribut](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. V části **deklarace identity uživatelů** v dialogu **atributy uživatele** NAKONFIGURUJTE atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name  | Zdrojový atribut  |
    | ---------| ------------ |
    | Role     | User. assignedroles |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](./common/new-save-attribute.png)
    
    ![image](./common/new-attribute-details.png)

    b. V seznamu **zdrojového atributu** selelct hodnotu atributu.

    c. Klikněte na **OK**.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Chcete-li zjistit, jak nakonfigurovat roli v Azure AD, klikněte prosím [sem](../develop/active-directory-enterprise-app-role-management.md) .

7. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavení správce internetového přístupu Zscaler** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Konfigurace Zscaler správce internetového přístupu s jedním Sign-On

1. V jiném okně webového prohlížeče se přihlaste k uživatelskému rozhraní správce internetového přístupu Zscaler.

2. Přejděte do části **správa > Správa správců** a proveďte následující kroky a klikněte na Uložit:

    ![Správa](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Správa")

    a. Zaškrtněte **možnost povolit ověřování SAML**.

    b. Klikněte na **nahrát**a nahrajte podpisový certifikát Azure SAML, který jste stáhli z Azure Portal ve **veřejném certifikátu SSL**.

    c. Volitelně můžete pro další zabezpečení přidat údaje **vystavitele** k ověření vystavitele odpovědi SAML.

3. V uživatelském rozhraní správce proveďte následující kroky:

    ![Správa](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Najeďte myší na nabídku **Aktivace** v blízkosti levého dolního rohu.

    b. Klikněte na tlačítko **aktivovat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Zscaler správci přístupu k Internetu.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Zscaler Internet Access Administrator**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Zscaler Internet Access Administrator**.

    ![Odkaz Zscaler Internet Access Administrator v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Vytvořit testovacího uživatele pro správce Zscaler internetového přístupu

Cílem této části je vytvořit uživatele s názvem Britta Simon v Zscaler správce internetového přístupu. Zscaler Internet Access nepodporuje zřizování za běhu pro jednotné přihlašování (SSO) správce. Je nutné ručně vytvořit účet správce.
Postup vytvoření účtu správce najdete v dokumentaci k Zscaler:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Zscaler Internet Access Administrator na přístupovém panelu byste měli být automaticky přihlášeni k uživatelskému rozhraní správce Zscaler pro přístup k Internetu, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)