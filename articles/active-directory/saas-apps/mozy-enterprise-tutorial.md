---
title: 'Kurz: Azure Active Directory integrace s Mozy Enterprise | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mozy Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 97294bdd088f50a46786c3568f4230706f750c4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520927"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Kurz: Azure Active Directory integrace s Mozy Enterprise

V tomto kurzu se dozvíte, jak integrovat Mozy Enterprise s Azure Active Directory (Azure AD).
Integrace Mozy Enterprise s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Mozy Enterprise.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Mozy Enterprise (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Mozy Enterprise budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Mozy Enterprise s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Mozy Enterprise podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Přidání Mozy Enterprise z Galerie

Pokud chcete nakonfigurovat integraci Mozy Enterprise do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Mozy Enterprise z galerie.

**Pokud chcete přidat Mozy Enterprise z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Mozy Enterprise**, vyberte **Mozy Enterprise** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Mozy Enterprise v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Mozy Enterprise na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Mozy Enterprise.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Mozy Enterprise, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Mozy Enterprise](#configure-mozy-enterprise-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte Mozy Enterprise Test User](#create-mozy-enterprise-test-user)** – abyste měli protějšek Britta Simon v Mozy Enterprise, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Mozy Enterprise, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **podnikových aplikací Mozy** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Mozy firemní domény a adresy URL jednotného přihlašování](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, kontaktujte [tým podpory Mozy Enterprise Client](https://support.mozy.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení organizace Mozy Enterprise** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurace Mozy Enterprise Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu Mozy Enterprise společnosti jako správce.

2. V části **Konfigurace** klikněte na **zásady ověřování**.
   
    ![Snímek obrazovky zobrazuje zásady ověřování vybrané z konfigurace.](./media/mozy-enterprise-tutorial/ic777314.png "Zásady ověřování")

3. V části **zásady ověřování** proveďte následující kroky:
   
    ![Snímek obrazovky se zobrazí v části zásady ověřování, kde můžete zadat hodnoty, které jsou popsány.](./media/mozy-enterprise-tutorial/ic777315.png "Zásady ověřování")
   
    a. Jako **zprostředkovatel** vyberte **adresářová služba** .
   
    b. Vyberte **použít nabízení protokolu LDAP**.
   
    c. Klikněte na kartu **ověřování SAML** .
   
    d. Vložte **adresu URL pro přihlášení**, kterou jste zkopírovali z Azure Portal do TEXTOVÉHO pole **URL pro ověřování** .
   
    e. Vložte **identifikátor Azure AD**, který jste zkopírovali z Azure Portal do textového pole **koncového bodu SAML** .
   
    f. V programu Poznámkový blok otevřete stažený stažený certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte celý certifikát do textového pole **certifikátu SAML** .
   
    například Vyberte možnost **Povolit jednotné přihlašování pro správce, aby se přihlásili pomocí svých síťových přihlašovacích údajů**.
   
    h. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Mozy Enterprise.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Mozy Enterprise**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Mozy Enterprise**.

    ![Odkaz Mozy Enterprise v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-mozy-enterprise-test-user"></a>Vytvořit Mozy Enterprise Test User

Aby se uživatelé Azure AD mohli přihlašovat do Mozy Enterprise, musí se zřídit v Mozy Enterprise. V případě Mozy Enterprise je zřizování ručním úkolem.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Mozy Enterprise nebo rozhraní API poskytovaná Mozy Enterprise.

**Při zřizování uživatelských účtů proveďte následující kroky:**

1. Přihlaste se ke svému klientovi **Mozy Enterprise** .

2. Klikněte na **Uživatelé** a potom klikněte na **Přidat nového uživatele**.
   
    ![Uživatelé](./media/mozy-enterprise-tutorial/ic777317.png "Uživatelé")
   
    >[!NOTE]
    >Možnost **Přidat nového uživatele** se zobrazí jenom v případě, že je v části **zásady ověřování** vybraný **Mozy** jako zprostředkovatel. Pokud je nakonfigurováno ověřování SAML, přidají se uživatelé automaticky při prvním přihlášení prostřednictvím jednotného přihlašování.
    
3. V dialogovém okně Nový uživatel proveďte následující kroky:
   
    ![Přidat uživatele](./media/mozy-enterprise-tutorial/ic777318.png "Přidat uživatele")
   
    a. V seznamu **Vyberte skupinu** vyberte skupinu.
   
    b. V seznamu **typ uživatele** vyberte typ.
   
    c. Do textového pole **uživatelské jméno** zadejte jméno uživatele Azure AD.
   
    d. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele Azure AD.
   
    e. Vyberte **Odeslat e-mailovou instrukci uživatele**.
   
    f. Klikněte na **Přidat uživatele**.

     >[!NOTE]
     > Po vytvoření uživatele se pošle e-mailem uživateli Azure AD, který obsahuje odkaz na potvrzení účtu dřív, než se aktivuje.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Mozy Enterprise na přístupovém panelu, měli byste se automaticky přihlásit k Mozy podniku, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)