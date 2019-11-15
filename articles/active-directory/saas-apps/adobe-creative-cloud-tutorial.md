---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Adobe Creative Cloud | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 815cffab118f6900c1c9d42a7e44821f8af62532
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081992"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Adobe Creative Cloud

V tomto kurzu se naučíte, jak integrovat Adobe Creative Cloud s Azure Active Directory (Azure AD). Když integrujete Adobe Creative Cloud s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Adobe Creative Cloud.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Adobe Creative Cloud se svými účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Adobe Creative Cloud odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adobe Creative Cloud podporuje jednotné přihlašování spouštěné přes **SP**





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Přidání Adobe Creative Cloud z Galerie

Pokud chcete nakonfigurovat integraci Adobe Creative Cloud do služby Azure AD, musíte přidat Adobe Creative Cloud z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Adobe Creative Cloud** .
1. Na panelu výsledků vyberte **Adobe Creative Cloud** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Adobe Creative Cloud

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Adobe Creative Cloud pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Adobe Creative Cloud.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Adobe Creative Cloud, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Adobe Creative Cloud SSO](#configure-adobe-creative-cloud-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Adobe Creative Cloud testovacího uživatele](#create-adobe-creative-cloud-test-user)** – můžete mít protějšek B. Simon v Adobe Creative Cloud, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **Adobe Creative Cloud** integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu url: `https://adobe.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Hodnota identifikátoru není skutečný. Aktualizuje tuto hodnotu skutečným identifikátorem. Pokud chcete získat tuto hodnotu, kontaktujte [tým podpory Adobe Creative Cloud klientů](https://www.adobe.com/au/creativecloud/business/teams/plans.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Adobe Creative Cloud aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho Adobe Creative Cloud aplikace očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > Uživatelé musí mít platnou licenci Office 365 ExO pro hodnotu deklarace e-mailu, která se má vyplnit v odpovědi SAML.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit Adobe Creative Cloud** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Adobe Creative Cloud.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Adobe Creative Cloud**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-adobe-creative-cloud-sso"></a>Konfigurace Adobe Creative Cloud jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste ke [konzole správce Adobe](https://adminconsole.adobe.com) jako správce.

2. V horním navigačním panelu vyberte **Nastavení** a pak zvolte **Identita**. Otevře se seznam domén. Klikněte na **Konfigurovat** odkaz na vaši doménu. Pak proveďte následující kroky v části **vyžadování konfigurace jednotného přihlašování** . Další informace najdete v tématu [nastavení domény](https://helpx.adobe.com/enterprise/using/set-up-domain.html) .

    ![Nastavení](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Nastavení")

    a. Kliknutím na **Procházet** nahrajte stažený certifikát z Azure AD do **IDP certifikátu**.

    b. Do textového pole **vystavitele IDP** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL pro přihlášení IDP** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Jako **vazbu IDP**vyberte **http-redirect** .

    e. Jako **Nastavení přihlášení uživatele**vyberte **e-mailová adresa** .

    f. Klikněte na tlačítko **Uložit** tlačítko.

3. Řídicí panel teď nabídne soubor XML pro **Stažení metadat** . Obsahuje adresu URL společnosti Adobe EntityDescriptor a adresu URL AssertionConsumerService. Otevřete prosím soubor a nakonfigurujte ho v aplikaci Azure AD.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Použijte hodnotu EntityDescriptor společnosti Adobe, kterou jste zadali pro **identifikátor** v dialogovém okně **Konfigurovat nastavení aplikace** .

    b. Použijte hodnotu AssertionConsumerService Adobe, kterou jste zadali pro **adresu URL odpovědi** v dialogovém okně **Konfigurovat nastavení aplikace** .

### <a name="create-adobe-creative-cloud-test-user"></a>Vytvořit Adobe Creative Cloud testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat Adobe Creative Cloud, musí se zřídit do Adobe Creative Cloud. V případě Adobe Creative Cloud je zřizování ručním úkolem.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Při zřizování uživatelských účtů proveďte následující kroky:

1. Přihlaste se k webu [konzoly pro správu Adobe](https://adminconsole.adobe.com) jako správce.

2. Přidejte uživatele do konzoly společnosti Adobe jako federované ID a přiřaďte je k profilu produktu. Podrobné informace o přidávání uživatelů najdete v tématu [Přidání uživatelů v konzole pro správu Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) . 

3. V tomto okamžiku zadejte svou e-mailovou adresu nebo hlavní název uživatele (UPN) do formuláře přihlašování Adobe, stiskněte klávesu TAB a měli byste být federované zpátky do služby Azure AD:
   * Webový přístup: Webová\.adobe.com > přihlášení
   * V rámci nástroje Desktop App > přihlášení
   * V rámci aplikace > Help > přihlášení

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Adobe Creative Cloud na přístupovém panelu byste měli být automaticky přihlášení do Adobe Creative Cloud, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušet Adobe Creative Cloud s využitím Azure AD](https://aad.portal.azure.com/)

- [Nastavení domény (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Konfigurace Azure pro použití s Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

