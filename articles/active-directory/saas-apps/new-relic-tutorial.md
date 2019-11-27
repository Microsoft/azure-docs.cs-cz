---
title: 'Kurz: Azure Active Directory integrace s novým Relic | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a novými Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: dfa5fbcf5df8ee314aab3e4a8228d81e7e14565b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233520"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Kurz: Azure Active Directory integrace s novým Relic

V tomto kurzu se naučíte integrovat nové Relic s Azure Active Directory (Azure AD).
Integrací nových Relic do Azure AD získáte následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k novým Relic.
* Můžete uživatelům povolit, aby se automaticky přihlásili k novému Relic (jednotné přihlašování) pomocí svých účtů Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s novým Relic potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Nové předplatné s povoleným jednotným přihlašováním Relic

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* New Relic podporuje jednotné přihlašování spouštěné v **SP**

## <a name="adding-new-relic-from-the-gallery"></a>Přidávání nových Relic z Galerie

Pokud chcete nakonfigurovat integraci nových Relic do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat nový Relic z galerie.

**Pokud chcete přidat novou Relic z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Nový Relic**, vyberte **Nový Relic** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Nový Relic v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí New Relic na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v New Relic.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí nového Relic, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte nové jednotné přihlašování Relic](#configure-new-relic-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit nového Relic testovacího uživatele](#create-new-relic-test-user)** – pro Britta Simon v novém Relic, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí nových Relic, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Nová** integrace aplikací Relic vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Nové přihlašovací údaje pro doménu Relic a adresy URL jednotného přihlašování](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` – Nezapomeňte nahradit vlastní ID účtu Relic.

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `rpm.newrelic.com`

5. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit nový Relic** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-new-relic-single-sign-on"></a>Konfigurace nového jednotného přihlašování Relic

1. V jiném okně webového prohlížeče se přihlaste k **novému webu Relic** společnosti jako správce.

2. V nabídce v horní části klikněte na **Nastavení účtu**.
   
    ![Nastavení účtu](./media/new-relic-tutorial/ic797036.png "Nastavení účtu")

3. Klikněte na kartu **zabezpečení a ověřování** a pak klikněte na kartu **jednotné přihlašování** .
   
    ![Jednotné přihlašování](./media/new-relic-tutorial/ic797037.png "Jednotné přihlašování")

4. Na stránce dialogu SAML proveďte následující kroky:
   
    ![ELEMENT](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Klikněte na **zvolit soubor** a nahrajte stažený Azure Active Directory certifikát.

    b. Do textového pole **Adresa URL vzdáleného přihlášení** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.
   
    c. Do textového pole **Adresa URL odpočívadla odhlášení** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    d. Klikněte na **Uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k novému Relic.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Nový Relic**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Nový Relic**.

    ![Nový odkaz Relic v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-new-relic-test-user"></a>Vytvořit nového Relic testovacího uživatele

Aby se uživatelé mohli Azure Active Directory přihlašovat k novým Relic, musí se zřídit do nové Relic. V případě nového Relic je zřizování ručním úkolem.

**Pokud chcete zřídit uživatelský účet pro nový Relic, proveďte následující kroky:**

1. Přihlaste se k **novému webu Relic** společnosti jako správce.

2. V nabídce v horní části klikněte na **Nastavení účtu**.
   
    ![Nastavení účtu](./media/new-relic-tutorial/ic797040.png "Nastavení účtu")

3. V podokně **účet** na levé straně klikněte na **Souhrn**a pak klikněte na **Přidat uživatele**.
   
    ![Nastavení účtu](./media/new-relic-tutorial/ic797041.png "Nastavení účtu")

4. V dialogovém okně **aktivní uživatelé** proveďte následující kroky:
   
    ![Aktivní uživatelé](./media/new-relic-tutorial/ic797042.png "Aktivní uživatelé")
   
    a. Do textového pole **e-mail** zadejte e-mailovou adresu platného Azure Active Directory uživatele, kterého chcete zřídit.

    b. Jako **role** vyberte **uživatele**.

    c. Klikněte na **Přidat tohoto uživatele**.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jakékoli další nové nástroje pro vytváření uživatelských účtů Relic nebo rozhraní API poskytovaná novými Relic.
> 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici nová Relic na přístupovém panelu byste měli být automaticky přihlášeni k novému Relic, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

