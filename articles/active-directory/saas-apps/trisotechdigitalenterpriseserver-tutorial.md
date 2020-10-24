---
title: 'Kurz: Azure Active Directory integrace s Trisotech Digital Enterprise Server | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trisotech Digital Enterprise Server.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: dfad185718dcc4a1c06ba3a4ed22961615ccd37e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486952"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Kurz: Azure Active Directory integrace s Trisotech Digital Enterprise Server

V tomto kurzu se dozvíte, jak integrovat Trisotech Digital Enterprise Server s Azure Active Directory (Azure AD).
Integrace Trisotech Digital Enterprise Server se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Trisotech Digital Enterprise serveru.
* Můžete uživatelům povolit, aby se automaticky přihlásili k Trisotech Digital Enterprise serveru (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Trisotech Digital Enterprise Server budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Trisotech Digital Enterprise Server s podporou jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Trisotech Digital Enterprise Server podporuje jednotné přihlašování (SSO) iniciované na **SP**

* Trisotech Digital Enterprise Server podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Přidání serveru Trisotech Digital Enterprise z Galerie

Pokud chcete nakonfigurovat integraci Trisotech Digital Enterprise serveru do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Trisotech Digital Enterprise Server z galerie.

**Pokud chcete do galerie přidat Trisotech Digital Enterprise Server, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Trisotech Digital Enterprise Server**, na panelu výsledků vyberte **Trisotech Digital Enterprise Server** a pak klikněte na **Přidat** tlačítko a přidejte aplikaci.

     ![Trisotech Digital Enterprise Server v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Trisotech digitálního podnikového serveru na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Trisotech Digital Enterprise serveru.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Trisotech Digital Enterprise serveru, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Trisotech Digital Enterprise serveru](#configure-trisotech-digital-enterprise-server-single-sign-on)** a nakonfigurujte nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele Trisotech Digital Enterprise serveru](#create-trisotech-digital-enterprise-server-test-user)** – abyste měli protějšek Britta Simon v Trisotech Digital Enterprise serveru, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí serveru Trisotech Digital Enterprise, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **serveru Trisotech Digital Enterprise Server** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Trisotech digitálního podnikového serveru a informace o jednotném přihlašování adresy URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.trisotech.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. K získání těchto hodnot se obraťte na [tým podpory klienta podpory Trisotech Digital Enterprise Server](mailto:support@trisotech.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Konfigurace jednoho Sign-On Trisotech Digital Enterprise Server

1. V jiném okně webového prohlížeče se přihlaste ke svému firemnímu webu Trisotech Digital Enterprise Server Configuration jako správce.

2. Klikněte na **ikonu nabídky** a pak vyberte **Správa**.

    ![Snímek obrazovky ukazuje ikonu správy na serveru Microsoft Digital Enterprise Server.](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Vyberte **poskytovatele uživatele**.

    ![Snímek obrazovky se zobrazí v nabídce vybraný poskytovatel uživatelů.](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. V části **Konfigurace poskytovatele uživatelských služeb** proveďte následující kroky:

    ![Snímek obrazovky ukazuje konfigurace poskytovatele uživatelských služeb, kde můžete zadat hodnoty, které jsou popsány.](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Z rozevíracího seznamu v **metodě ověřování**vyberte **zabezpečený kód kontrolního výrazu (SAML 2)** .

    b. Do textového pole **Adresa URL metadat** vložte hodnotu **URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **ID aplikace** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.trisotech.com` .

    d. Klikněte na **Uložit**.

    e. Zadejte název domény do textového pole **povolené domény (prázdné znamená všichni)** , automaticky přiřadí licence pro uživatele, kteří odpovídají povoleným doménám.

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Trisotech Digital Enterprise serveru.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **Trisotech Digital Enterprise Server**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Trisotech Digital Enterprise Server**.

    ![Odkaz na server Trisotech Digital Enterprise v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Vytvořit testovacího uživatele pro Trisotech Digital Enterprise Server

V této části se v Trisotech Digital Enterprise serveru vytvoří uživatel s názvem Britta Simon. Trisotech Digital Enterprise Server podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Trisotech Digital Enterprise Server neexistuje, vytvoří se po ověření nový.

>[!Note]
>Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Server Trisotech Digital Enterprise na přístupovém panelu byste se měli automaticky přihlášeni k serveru Trisotech Digital Enterprise, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

