---
title: 'Kurz: Azure Active Directory integrace s Adobe Creative Cloud | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f3fdd1aa4fa655cfffb9e9a2f29d551133613a8
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154013"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Kurz: Azure Active Directory integrace s Adobe Creative Cloud

V tomto kurzu se naučíte, jak integrovat Adobe Creative Cloud s Azure Active Directory (Azure AD).
Integrace Adobe Creative Cloud se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Adobe Creative Cloud.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Adobe Creative Cloud (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Adobe Creative Cloud potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Adobe Creative Cloud odběr povoleného jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adobe Creative Cloud podporuje jednotné přihlašování spouštěné přes **SP**

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Přidání Adobe Creative Cloud z Galerie

Pokud chcete nakonfigurovat integraci Adobe Creative Cloud do služby Azure AD, musíte přidat Adobe Creative Cloud z Galerie do svého seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Adobe Creative Cloud z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Adobe Creative Cloud**, vyberte **Adobe Creative Cloud** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Adobe Creative Cloud v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Adobe Creative Cloud na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v Adobe Creative Cloud.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Adobe Creative Cloud, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte Adobe Creative Cloud jednotné přihlašování](#configure-adobe-creative-cloud-single-sign-on)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte Adobe Creative Cloud testovacího uživatele](#create-adobe-creative-cloud-test-user)** – Britta Simon v Adobe Creative Cloud, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Adobe Creative Cloud, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Adobe Creative Cloud** integrace aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně a adresám URL Adobe Creative Cloud](common/sp-identifier.png)

    a. Do textového pole **Adresa URL pro přihlášení** zadejte hodnotu jako: `https://adobe.com`.

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Hodnota identifikátoru není reálné číslo. Aktualizuje tuto hodnotu skutečným identifikátorem. Pokud chcete získat tuto hodnotu, kontaktujte [tým podpory Adobe Creative Cloud klientů](https://www.adobe.com/au/creativecloud/business/teams/plans.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Adobe Creative Cloud aplikace očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na tlačítko **Upravit** a otevřete dialog **uživatelské atributy** .

    ![image](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogu **atributy uživatele** NAKONFIGURUJTE atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name (Název) | Zdrojový atribut|
    |----- | --------- |
    | firstName | User. křestní jméno |
    | Polím | User. příjmení |
    | E-mail | uživatel. pošta

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Uživatelé musí mít platnou licenci Office 365 ExO pro hodnotu deklarace e-mailu, která se má vyplnit v odpovědi SAML.

7. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

8. V části **nastavit Adobe Creative Cloud** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Konfigurace Adobe Creative Cloud jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste ke [konzole správce Adobe](https://adminconsole.adobe.com) jako správce.

2. V horním navigačním panelu vyberte **Nastavení** a pak zvolte **Identita**. Otevře se seznam domén. Klikněte na **Konfigurovat** odkaz na vaši doménu. Pak proveďte následující kroky v části **vyžadování konfigurace jednotného přihlašování** . Další informace najdete v tématu [nastavení domény](https://helpx.adobe.com/enterprise/using/set-up-domain.html) .

    ![Nastavení](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Nastavení")

    a. Kliknutím na **Procházet** nahrajte stažený certifikát z Azure AD do **IDP certifikátu**.

    b. Do textového pole **vystavitele IDP** zadejte hodnotu **ID entity SAML** , kterou jste zkopírovali z části **Konfigurace přihlašování** v Azure Portal.

    c. Do textového pole **Adresa URL pro přihlášení IDP** zadejte hodnotu **adresy URL služby SAML** , kterou jste zkopírovali z části **Konfigurace přihlašování** v Azure Portal.

    d. Jako **vazbu IDP**vyberte **http-redirect** .

    e. Jako **Nastavení přihlášení uživatele**vyberte **e-mailová adresa** .

    f. Klikněte na tlačítko **Uložit** .

3. Řídicí panel teď nabídne soubor XML pro **Stažení metadat** . Obsahuje adresu URL společnosti Adobe EntityDescriptor a adresu URL AssertionConsumerService. Otevřete prosím soubor a nakonfigurujte ho v aplikaci Azure AD.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Použijte hodnotu EntityDescriptor společnosti Adobe, kterou jste zadali pro **identifikátor** v dialogovém okně **Konfigurovat nastavení aplikace** .

    b. Použijte hodnotu AssertionConsumerService Adobe, kterou jste zadali pro **adresu URL odpovědi** v dialogovém okně **Konfigurovat nastavení aplikace** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Adobe Creative Cloud.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Adobe Creative Cloud**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Adobe Creative Cloud**.

    ![Odkaz Adobe Creative Cloud v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-adobe-creative-cloud-test-user"></a>Vytvořit Adobe Creative Cloud testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat k Adobe Creative Cloud, musí se zřídit do Adobe Creative Cloud. V případě Adobe Creative Cloud je zřizování ručním úkolem.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Při zřizování uživatelských účtů proveďte následující kroky:

1. Přihlaste se k webu [konzoly pro správu Adobe](https://adminconsole.adobe.com) jako správce.

2. Přidejte uživatele do konzoly společnosti Adobe jako federované ID a přiřaďte je k profilu produktu. Podrobné informace o přidávání uživatelů najdete v tématu [Přidání uživatelů v konzole pro správu Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) . 

3. V tomto okamžiku zadejte svou e-mailovou adresu nebo hlavní název uživatele (UPN) do formuláře přihlášení společnosti Adobe, stiskněte klávesu TAB a měli byste být federované zpátky do služby Azure AD:
   * Webový přístup: Webová\.adobe.com > přihlášení
   * V rámci nástroje Desktop App > přihlášení
   * V rámci aplikace > Help > přihlášení

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Adobe Creative Cloud na přístupovém panelu byste měli být automaticky přihlášení do Adobe Creative Cloud, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Nastavení domény (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Konfigurace Azure pro použití s Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
