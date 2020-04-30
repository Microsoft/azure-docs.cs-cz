---
title: 'Kurz: Azure Active Directory integrace s absorpčním LMS | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a absorpčním LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 936de76d1117c56f5a9dec48b51f33b9afa15351
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "67107503"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Kurz: Azure Active Directory integrace s absorpčním LMS

V tomto kurzu se dozvíte, jak integrovat pomocí Azure Active Directory (Azure AD) s pohlcováním.
Integrování absorbovaného LMS pomocí Azure AD vám poskytne následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k tomuto LMS.
* Můžete uživatelům povolit, aby se automaticky přihlásili k absorbovat LMS (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s použitím nástroje pro zavedení s použitím absorpčního LMS potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Povolit odběr jednotného přihlašování pro LMS

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LMS s absorpcí podporuje **IDP** , které INICIOVALO jednotné přihlášení

## <a name="adding-absorb-lms-from-the-gallery"></a>Přidání pohlcování LMS z Galerie

Chcete-li nakonfigurovat integraci systému pro pohlcování do služby Azure AD, je třeba přidat do seznamu spravovaných aplikací pro SaaS absorpční LMS z galerie.

**Chcete-li přidat absorpční LMS z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte text **absorbovaný LMS**, vyberte **absorpční LMS** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Absorpční LMS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí systému pro pohlcování na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v systému v rámci absorpčního LMS.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí systému s absorpcí, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování](#configure-absorb-lms-single-sign-on)** pro program pro nastavení jednotného přihlašování – ke konfiguraci jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele LMS s pohlcováním](#create-absorb-lms-test-user)** – Pokud chcete mít protějšek Britta Simon v systému LMS s absorpcí, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí programu absorbovaného LMS, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce Application Integration **LMS** aplikace vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** kliknutím na tlačítko **Upravit** otevřete základní dialogové okno **Konfigurace SAML** .

    ![Absorpční informace o jednotném přihlašování k doméně LMS a adres URL](common/idp-intiated.png)

    Pokud používáte **pohlcování 5 – uživatelské rozhraní** používá tuto konfiguraci:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://company.myabsorb.com/account/saml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://company.myabsorb.com/account/saml`

    Pokud používáte **absorpci 5 – nové prostředí** pro práci s dalšími postupy, použijte následující konfiguraci:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienty LMS](https://support.absorblms.com/hc/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**.

    ![image](common/edit-attribute.png)

6. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **set LMS pro pohlcování** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-absorb-lms-single-sign-on"></a>Konfigurace jednotného přihlašování v systému pro pohlcování LMS

1. V novém okně webového prohlížeče se přihlaste k vašemu firemnímu LMS webu jako správce.

2. V pravém horním rohu vyberte tlačítko **účet** .

    ![Tlačítko účet](./media/absorblms-tutorial/1.png)

3. V podokně účet vyberte **nastavení portálu**.

    ![Odkaz nastavení portálu](./media/absorblms-tutorial/2.png)

4. Vyberte kartu **Správa nastavení jednotného přihlašování** .

    ![Karta uživatelé](./media/absorblms-tutorial/managesso.png)

5. Na stránce **Spravovat nastavení jednotného přihlašování** udělejte toto:

    ![Stránka Konfigurace jednotného přihlašování](./media/absorblms-tutorial/settings.png)

    a. Do textového pole **název** zadejte název, jako je například jednotné přihlašování Azure AD Marketplace.

    b. Jako **metodu**vyberte **SAML** .

    c. V programu Poznámkový blok otevřete certifikát, který jste stáhli z Azure Portal. Odeberte **---počáteční---certifikátu** a **------značky koncového certifikátu** . Potom do pole **klíč** vložte zbývající obsah.

    d. V poli **režim** vyberte **poskytovatele identity iniciované**.

    e. V poli **vlastnost ID** vyberte atribut, který jste nakonfigurovali jako identifikátor uživatele v Azure AD. Pokud je například ve službě Azure AD vybraný *NameIdentifier* , vyberte **uživatelské jméno**.

    f. Jako **typ podpisu**vyberte **SHA256** .

    g. Do pole **Adresa URL pro přihlášení** vložte **adresu URL přístupu uživatele** ze stránky **vlastností** aplikace Azure Portal.

    h. Do pole **Adresa URL pro odhlášení**vložte hodnotu **URL** pro odhlášení, kterou jste zkopírovali z okna **Konfigurace přihlášení** Azure Portal.

    i. Přepínač **automaticky přesměrovat** na **zapnuto**.

6. Vyberte **Uložit.**

    ![Jediný přepínač povolení přihlášení SSO](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména**`brittasimon\@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k pohlcování LMS.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **absorpční LMS**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **absorpční LMS**.

    ![Odkaz na pohlcený LMS v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-absorb-lms-test-user"></a>Vytvořit uživatele testu na absorpční LMS

Aby se uživatelé Azure AD přihlásili ke absorpčnímu LMS, musí být nastaveni v LMS v absorbované. V případě programu absorpčního LMS je zřizování ručním úkolem.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se ke svému webu společnosti s příznakem pro LMS jako správce.

2. V podokně **Uživatelé** vyberte **Uživatelé**.

    ![Odkaz uživatelé](./media/absorblms-tutorial/absorblms_userssub.png)

3. Vyberte kartu **uživatel** .

    ![Rozevírací seznam přidat nový](./media/absorblms-tutorial/absorblms_createuser.png)

4. Na stránce **Přidat uživatele** postupujte takto:

    ![Stránka Přidat uživatele](./media/absorblms-tutorial/user.png)

    a. Do pole **jméno zadejte jméno,** například **Britta**.

    b. Do pole **příjmení** zadejte příjmení, například **Simon**.

    c. Do pole **uživatelské jméno** zadejte celé jméno, například **Britta Simon**.

    d. Do pole **heslo** zadejte heslo uživatele.

    e. Do pole **Potvrdit heslo** zadejte heslo znovu.

    f. Nastavte přepínač **je aktivní** na **aktivní**.

5. Vyberte **Uložit.**

    ![Jediný přepínač povolení přihlášení SSO](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Ve výchozím nastavení není zřizování uživatelů v jednotném přihlašování povolené. Pokud chce zákazník tuto funkci povolit, musí ji nastavit tak, jak je uvedeno v [této](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentaci. Upozorňujeme také, že pro uživatele je k dispozici pouze **pohlcování 5 – nové prostředí s možností učení** s adresou URL služby ACS –`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici pohlcená LMS na přístupovém panelu byste se měli automaticky přihlášeni k LMS, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
