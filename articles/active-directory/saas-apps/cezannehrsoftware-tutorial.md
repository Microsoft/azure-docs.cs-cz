---
title: 'Kurz: Azure Active Directory integraci se softwarem Cezanne HR | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a software Cezanne HR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 0aa0dab7b512c85fbbdf374c962e6ee8e1c7d616
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456271"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Kurz: Azure Active Directory integraci se softwarem Cezanne HR

V tomto kurzu se naučíte integrovat software Cezanne HR s Azure Active Directory (Azure AD).
Integrace softwaru Cezanne HR s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Cezanne softwaru pro správu lidských zdrojů.
* Uživatelům můžete povolit, aby se automaticky přihlásili k softwaru Cezanne HR (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se softwarem Cezanne HR budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Cezanne HR software pro jednotné přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Software Cezannee pro **personál podporuje jednotné** přihlašování.

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Přidání softwaru Cezanne HR z Galerie

Pokud chcete nakonfigurovat integraci softwaru Cezanne HR do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat software Cezanne HR z galerie.

**Pokud chcete přidat software Cezanne HR z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **CEZANNE HR software**, vyberte **Cezanne HR software** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Cezanne software HR v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí softwaru Cezanne HR založeného na testovacím uživateli s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v softwaru Cezanne HR.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí softwaru Cezannee HR, je potřeba, abyste dokončili tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro CEZANNE HR software](#configure-cezanne-hr-software-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si uživatele Cezannee software Tester](#create-cezanne-hr-software-test-user)** , abyste měli protějšek Britta Simon v softwaru Cezanne HR, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí softwaru Cezannee HR, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **softwarových aplikací Cezanne HR** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování Cezanne HR software a adresy URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečnou adresou URL Sign-On a adresou URL odpovědi. K získání těchto hodnot se obraťte na [tým podpory CEZANNE HR software Client](https://cezannehr.com/services/support/) .

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit software Cezannee HR** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Konfigurace Cezanne HR software Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k vašemu tenantovi softwaru Cezanne HR jako správce.

2. V levém navigačním podokně klikněte na **nastavení systému**. Přejít na **nastavení zabezpečení** Pak přejděte do **konfigurace s jedním Sign-On**.

    ![Snímek obrazovky s vybraným nastavením zabezpečení a konfigurací pro jednu Sign-On zobrazuje klienta softwaru Cezanne H R.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. V okně **dovolit uživatelům přihlášení pomocí následujícího panelu služby jednotného Sign-On (SSO)** zaškrtněte políčko **SAML 2,0** a vyberte možnost **Pokročilá konfigurace** .

    ![Snímek obrazovky zobrazuje podokno pro povolení uživatelů s vybraným rozhraním SAML 2,0 a pokročilou konfigurací.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Klikněte na tlačítko **Přidat nové** .

    ![Snímek obrazovky se zobrazeným tlačítkem Přidat nový.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. V části **Zprostředkovatelé identit SAML 2,0** proveďte následující kroky.

    ![Snímek obrazovky se zobrazí podokno, kde můžete zadat hodnoty popsané v tomto kroku.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Jako **Zobrazovaný název**zadejte název poskytovatele identity.

    b. Do textového pole **identifikátor entity** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    c. Změňte **vazbu SAML** na post.

    d. Do textového pole **koncového bodu služby tokenu zabezpečení** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    e. Do textového pole název atributu ID uživatele zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    f. Kliknutím na **nahrát** ikonu nahrajte stažený certifikát z Azure Portal.

    například Klikněte na tlačítko **OK** .

6. Klikněte na tlačítko **Uložit** .

    ![Snímek obrazovky se zobrazením tlačítka Uložit pro konfiguraci jednotného přihlašování.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k softwaru Cezanne HR.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **software Cezanne HR**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Cezanne software HR**.

    ![Odkaz na software Cezanne HR v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-cezanne-hr-software-test-user"></a>Vytvořit uživatele Cezanne HR software test

Aby se uživatelé Azure AD mohli přihlásit k Cezanne softwaru pro personál, musí se zřídit v softwaru Cezanne HR. V případě softwaru Cezanne HR je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu Cezanne HR software společnosti jako správce.

2. V levém navigačním podokně klikněte na **nastavení systému**. Přejít na **Správa uživatelů** Pak přejděte k **Přidat nového uživatele**.

    ![Snímek obrazovky se zobrazí jako tenant softwaru Cezanne H R s možností spravovat uživatele a přidat nového uživatele.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nový uživatel")

3. V části **Podrobnosti o osobě** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v části Podrobnosti o osobě, kde můžete zadat hodnoty popsané v tomto kroku.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nový uživatel")

    a. Nastavte **interního uživatele** jako vypnutý.

    b. Do textového pole **jméno v prvním** poli zadejte jméno uživatele jako **Britta**.  

    c. Do textového pole **příjmení** zadejte jméno uživatele jako **Simon**.

    d. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com .

4. V části **informace o účtu** proveďte následující kroky:

    ![Snímek obrazovky zobrazuje informace o účtu, kde můžete zadat hodnoty popsané v tomto kroku.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nový uživatel")

    a. Do textového pole **uživatelské jméno** zadejte e-maily jako uživatel Brittasimon@contoso.com .

    b. Do textového pole **heslo** zadejte heslo uživatele.

    c. Jako **roli zabezpečení**vyberte **HR Professional** .

    d. Klikněte na **OK**.

5. Přejděte na kartu **jednotné přihlašování** a v oblasti **identifikátory SAML 2,0** vyberte **Přidat nový** .

    ![Snímek obrazovky zobrazuje jednu kartu Sign-On, kde můžete vybrat přidat nový.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Uživatel")

6. Vyberte poskytovatele identity pro **poskytovatele identity** a v textovém poli **identifikátoru uživatele**zadejte e-mailovou adresu účtu Britta Simon.

    ![Snímek obrazovky zobrazuje identifikátory SAML 2,0, kde můžete vybrat poskytovatele identity a identifikátor uživatele.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Uživatel")

7. Klikněte na tlačítko **Uložit** .

    ![Snímek obrazovky zobrazující tlačítko Uložit pro uživatelská nastavení](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Uživatel")

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici software Cezanne HR na přístupovém panelu, měli byste se automaticky přihlásit k softwaru Cezanne HR, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)