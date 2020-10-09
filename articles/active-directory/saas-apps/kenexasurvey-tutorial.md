---
title: 'Kurz: Azure Active Directory integrace s nástrojem IBM Kenexa Survey Enterprise | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IBM Kenexa Survey Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: ed4725a98939e160f42d3087b86e591b09c026c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88540163"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Kurz: Azure Active Directory integrace s nástrojem IBM Kenexa Survey Enterprise

V tomto kurzu se dozvíte, jak integrovat Azure Kenexa Survey Enterprise s Azure Active Directory (Azure AD).
Integrace nástroje IBM Kenexa Survey Enterprise s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k nástroji IBM Kenexa Survey Enterprise.
* Můžete uživatelům povolit, aby se automaticky přihlásili k nástroji IBM Kenexa Survey Enterprise (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s využitím nástroje IBM Kenexa Survey Enterprise budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné IBM Kenexa Survey Enterprise s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* IBM Kenexa Survey Enterprise podporuje jednotné přihlašování **IDP** .

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Přidání průzkumu IBM Kenexa Survey Enterprise z Galerie

Pokud chcete nakonfigurovat integraci nástroje IBM Kenexa Survey Enterprise do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Azure Kenexa Survey Enterprise z galerie.

**Pokud chcete do galerie přidat Kenexa Survey Enterprise, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **IBM Kenexa Survey Enterprise**, vyberte **IBM Kenexa Survey Enterprise** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![IBM Kenexa Survey Enterprise v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s využitím průzkumu IBM Kenexa Survey Enterprise na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji IBM Kenexa Survey Enterprise.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí nástroje Kenexa Survey Enterprise společnosti IBM, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování IBM Kenexa Survey](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** – pro konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte si Azure Kenexa Survey Enterprise Test User](#create-ibm-kenexa-survey-enterprise-test-user)** – abyste měli protějšek Britta Simon v podniku IBM Kenexa Survey Enterprise, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí nástroje Kenexa Survey Enterprise, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace s **podnikovou aplikací IBM Kenexa** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování k IBM Kenexa průzkumu podnikových domén a adres URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://surveys.kenexa.com/<companycode>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory IBM Kenexa Survey Enterprise Client](https://www.ibm.com/support/home/?lnk=fcw) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Podniková aplikace Kenexa Survey v IBM očekává, že v určitém formátu obdrží kontrolní výrazy jazyka SAML (Security Assert Markup Language), které vyžadují, abyste přidali mapování vlastních atributů do konfigurace atributů tokenu SAML. Hodnota deklarace identity uživatelského identifikátoru v odpovědi musí odpovídat ID jednotného přihlašování, které je nakonfigurované v systému Kenexa. Pokud chcete namapovat příslušný identifikátor uživatele ve vaší organizaci jako protokol IDP (SSO Internet Datagram Protocol), pracujte s [týmem Enterprise Support pro průzkum IBM Kenexa](https://www.ibm.com/support/home/?lnk=fcw).

    Ve výchozím nastavení Azure AD nastaví identifikátor uživatele jako hodnotu hlavního názvu uživatele (UPN). Tuto hodnotu můžete změnit na kartě **atributy uživatele** , jak je znázorněno na následujícím snímku obrazovky. Integrace funguje až po správném dokončení mapování.

    ![image](common/edit-attribute.png)

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **nastavení nástroje IBM Kenexa Survey Enterprise** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Konfigurace podnikového jednoho Sign-Onho průzkumu IBM Kenexa

Chcete-li nakonfigurovat jednotné přihlašování na straně **Enterprise společnosti IBM Kenexa Survey** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Kenexa pro průzkum Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k nástroji IBM Kenexa Survey Enterprise.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **IBM Kenexa Survey Enterprise**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **IBM Kenexa Survey Enterprise**.

    ![Odkaz na seznam aplikací v IBM Kenexa Survey Enterprise](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Vytvořit dotazník IBM Kenexa Survey Enterprise Test User

V této části vytvoříte uživatele s názvem Britta Simon v IBM Kenexa Survey Enterprise.

Pokud chcete vytvářet uživatele v podnikovém systému IBM Kenexa Survey a namapovat na ně ID jednotného přihlašování, můžete pracovat s [týmem Enterprise Support pro průzkum IBM Kenexa](https://www.ibm.com/support/home/?lnk=fcw). Tato hodnota ID jednotného přihlašování by taky měla být namapovaná na hodnotu identifikátoru uživatele z Azure AD. Toto výchozí nastavení můžete změnit na kartě **atribut** .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Microsoft Kenexa Survey Enterprise na přístupovém panelu byste se měli automaticky přihlášeni ke službě IBM Kenexa Survey Enterprise, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

