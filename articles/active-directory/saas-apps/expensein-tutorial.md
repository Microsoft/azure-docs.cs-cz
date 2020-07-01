---
title: 'Kurz: Azure Active Directory integrace s ExpenseIn | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ExpenseIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7e3659aa3044a4453574a2cd171303d00abc19
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85606677"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Kurz: integrace ExpenseIn s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat ExpenseIn s Azure Active Directory (Azure AD). Když integrujete ExpenseIn s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ExpenseIn.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k ExpenseIn svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* ExpenseIn odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. ExpenseIn podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-expensein-from-the-gallery"></a>Přidání ExpenseIn z Galerie

Pokud chcete nakonfigurovat integraci ExpenseIn do služby Azure AD, musíte přidat ExpenseIn z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ExpenseIn** .
1. Na panelu výsledků vyberte **ExpenseIn** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ExpenseIn pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ExpenseIn.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ExpenseIn, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte ExpenseIn](#configure-expensein)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
4. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
5. **[Vytvořte uživatele ExpenseIn test](#create-expensein-test-user)** , který bude mít protějšek B. Simon v ExpenseIn, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ExpenseIn** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující krok:

    Do textového pole **Adresa URL odpovědi** zadejte některou z těchto adres URL:

    ```https
    https://app.expensein.com/samlcallback
    https://mobileapi.expensein.com/identity/samlcallback
    ```

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:`https://app.expensein.com/saml`

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a kliknutím na tlačítko **Stáhnout** stáhněte **certifikát (Base64)** a uložte ho do počítače.

   ![Odkaz na stažení certifikátu](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. V části **Nastavení ExpenseIn** zkopírujte na základě vašeho požadavku příslušné adresy URL.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Konfigurace ExpenseIn

1. Pokud chcete automatizovat konfiguraci v rámci ExpenseIn, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **ExpenseIn nastavení** a nasměrujte vás na aplikaci ExpenseIn. Odtud zadejte přihlašovací údaje správce, které se přihlásí k ExpenseIn. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit ExpenseIn ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu ExpenseIn společnosti jako správce a proveďte následující kroky:

4. V horní části stránky klikněte na **správce** , přejděte do **jednotného přihlašování** a klikněte na **Přidat poskytovatele**.

     ![Konfigurace ExpenseIn](./media/expenseIn-tutorial/config01.png)

5. V místní nabídce **nového zprostředkovatele identity** proveďte následující kroky:

    ![Konfigurace ExpenseIn](./media/expenseIn-tutorial/config02.png)

    a. Do textového pole **název poskytovatele** zadejte název, jako např.: Azure.

    b. Vyberte **Ano** , pokud **povolíte přihlášení Intitated poskytovatele**.

    c. Do textového pole **cílová adresa URL** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **vystavitele** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    e. V programu Poznámkový blok otevřete certifikát (Base64), zkopírujte jeho obsah a vložte ho do textového pole **certifikát** .

    f. Klikněte na **Vytvořit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ExpenseIn.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **ExpenseIn**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-expensein-test-user"></a>Vytvořit testovacího uživatele ExpenseIn

Aby se uživatelé Azure AD mohli přihlašovat k ExpenseIn, musí se zřídit v ExpenseIn. V ExpenseIn je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k ExpenseIn jako správce.

2. V horní části stránky klikněte na **správce** a pak přejděte na **Uživatelé** a klikněte na **Nový uživatel**.

     ![Konfigurace ExpenseIn](./media/expenseIn-tutorial/config03.png)

3. V automaticky otevíraném okně **Podrobnosti** proveďte následující kroky:

    ![Konfigurace ExpenseIn](./media/expenseIn-tutorial/config04.png)

    a. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako **B**.

    b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. Do textového pole **e-mailu** zadejte e-maily uživatele jako `B.Simon@contoso.com` .

    d. Klikněte na **Vytvořit**.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici ExpenseIn, měli byste se automaticky přihlásit k ExpenseIn, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
