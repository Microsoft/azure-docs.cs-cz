---
title: 'Kurz: Azure Active Directory integrace s Comm100 Live chat | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Comm100 Live chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91a7bf535a4afe0d46a13bbdc5643da7d5ce3334
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157349"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Kurz: Azure Active Directory integrace s Comm100 Live chat

V tomto kurzu se naučíte integrovat Comm100 Live chat s Azure Active Directory (Azure AD).
Integrace Comm100 Live chatu s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k Comm100 Live chat.
* Můžete uživatelům povolit, aby se automaticky přihlásili k Comm100 živého chatu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Comm100 Live chat potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Comm100 Live chat s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Comm100 Live chat podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Přidání živého chatu Comm100 z Galerie

Pokud chcete nakonfigurovat integraci Comm100 Live chatu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Comm100 Live Chat z galerie.

**Chcete-li přidat Comm100 Live Chat z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Comm100 Live chat**, vyberte **Comm100 Live chat** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Comm100 Live chat v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Comm100 Live chatu na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Comm100 Live chat.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Comm100 Live chatu, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Comm100 Live chat](#configure-comm100-live-chat-single-sign-on)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte Comm100 Live chat Test User](#create-comm100-live-chat-test-user)** – abyste měli protějšek Britta Simon v Comm100 živém chatu, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Comm100 Live chatu, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Comm100 Live chat** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování Comm100 Live chatu a adres URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Hodnota přihlašovací adresy URL není reálné číslo. Hodnotu adresy URL pro přihlášení aktualizujete pomocí skutečné přihlašovací adresy URL, která se vysvětluje později v tomto kurzu.

5. Aplikace Comm100 Live chat očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na tlačítko **Upravit** a otevřete dialog **uživatelské atributy** .

    ![image](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky: 

    | Name (Název) |  Zdrojový atribut|
    | ---------------| --------------- |
    |   e-mail    | uživatel. pošta |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    g. Klikněte na **Uložit**.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení živého chatu Comm100** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-comm100-live-chat-single-sign-on"></a>Konfigurace jednotného přihlašování Comm100 Live chatu

1. V jiném okně webového prohlížeče se přihlaste k Comm100 Live Chat jako správce zabezpečení.

1. V pravém horním rohu stránky klikněte na **můj účet**.

   ![Comm100 Live chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. V levé části nabídky klikněte na **zabezpečení** a pak klikněte na **jednotné přihlašování agenta**.

   ![Zabezpečení Comm100 Live chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Na stránce **jednotné přihlašování agenta** proveďte následující kroky:

   ![Zabezpečení Comm100 Live chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Zkopírujte první zvýrazněný odkaz a vložte ho do pole **Adresa URL pro přihlášení** v části **doména a adresy URL Comm100 Live chatu** na Azure Portal.

   b. Do textového pole **URL jednotného přihlašování SAML** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

   c. Do textového pole **Adresa URL vzdáleného odhlášení** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

   d. Kliknutím na **zvolit soubor** odešlete certifikát s kódováním base-64, který jste stáhli z Azure Portal, do **certifikátu**.

   e. Klikněte na **Uložit změny** .

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Comm100 živému chatu.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Comm100 Live chat**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Comm100 Live chat**.

    ![Odkaz Comm100 Live chat v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-comm100-live-chat-test-user"></a>Vytvořit testovacího uživatele Comm100 Live chat

Aby se uživatelé Azure AD mohli přihlásit k Comm100 živému chatu, musí se zřídit do Comm100 živého chatu. V Comm100 Live chat je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Comm100 Live Chat jako správce zabezpečení.

2. V pravém horním rohu stránky klikněte na **můj účet**.

    ![Comm100 Live chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. V levé části nabídky klikněte na **agenti** a potom klikněte na **nový agent**.

    ![Agent Comm100 Live chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na stránce **nový agent** proveďte následující kroky:

    ![Nový agent Comm100 Live chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Do textového pole **e-mail** zadejte e-maily uživatele, jako je **Brittasimon\@contoso.com**.

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta**.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    d. Do textového pole **Zobrazovaný název** zadejte zobrazované jméno uživatele, jako je **Britta Simon** .

    e. Do textového pole **heslo** zadejte heslo.

    f. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Comm100 Live chat na přístupovém panelu, měli byste být automaticky přihlášeni k živému chatu Comm100, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

