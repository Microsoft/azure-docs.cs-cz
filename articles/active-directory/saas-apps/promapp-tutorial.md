---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Promappem Nintex | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Nintex Promapp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.openlocfilehash: 3b4102b4e8dcbd5aa04e354cec0ab91bf139572c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553474"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Promappem Nintex

V tomto kurzu se dozvíte, jak integrovat Nintex Promapp s Azure Active Directory (Azure AD). Když integrujete Nintex Promapp s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Nintex Promapp.
* Umožněte uživatelům, aby se automaticky přihlásili k Nintex Promapp s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Nintex Promapp s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Nintex Promapp podporuje **SP a IDP** iniciované jednotné přihlašování.
* Nintex Promapp podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-nintex-promapp-from-the-gallery"></a>Přidání Nintex Promapp z Galerie

Pokud chcete nakonfigurovat integraci Nintex Promapp do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Nintex Promapp z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Nintex Promapp** .
1. Z panelu výsledků vyberte **Nintex Promapp** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Nintex Promapp

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Nintex Promapp s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Nintex Promapp.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Nintex Promapp, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Nintex PROMAPP SSO](#configure-nintex-promapp-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořit Nintex Promapp Test User](#create-nintex-promapp-test-user)** -to znamená, že má protějšek B. Simon v Nintex Promapp, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Promapp Nintex** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    1. Do pole **identifikátor** zadejte adresu URL v tomto vzoru:

        ```https
        https://go.promapp.com/TENANTNAME/
        https://au.promapp.com/TENANTNAME/
        https://us.promapp.com/TENANTNAME/
        https://eu.promapp.com/TENANTNAME/
        https://ca.promapp.com/TENANTNAME/
        ```

       > [!NOTE]
       > Integrace Azure AD s Nintex Promapp se aktuálně konfiguruje jenom pro ověřování iniciované službou. (To znamená, že přístup k adrese URL Nintex Promapp zahájí proces ověřování.) Ale pole **Adresa URL odpovědi** je povinné pole.

    1. Do pole **Adresa URL odpovědi** zadejte adresu URL v tomto vzoru:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do pole **přihlašovací adresa URL** zadejte adresu URL v tomto vzoru: `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Musíte použít skutečný identifikátor, adresu URL odpovědi a přihlašovací adresu URL. Pokud chcete získat hodnoty, obraťte se na [tým podpory Nintex Promapp](https://www.promapp.com/about-us/contact-us/) . Můžete se také podívat na vzory zobrazené v dialogovém okně **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Promapp Nintex** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Nintex Promapp.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Nintex Promapp**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-nintex-promapp-sso"></a>Konfigurace jednotného přihlašování Nintex Promapp

1. Přihlaste se k webu společnosti Nintex Promapp jako správce.

2. V nabídce v horní části okna vyberte **správce**:

    ![Vybrat správce][12]

3. Vyberte **Konfigurovat**:

    ![Vyberte konfigurovat.][13]

4. V dialogovém okně **zabezpečení** proveďte následující kroky.

    ![Dialogové okno zabezpečení][14]

    1. Vložte **adresu URL pro přihlášení** , kterou jste zkopírovali z Azure Portal do pole **Adresa URL pro přihlášení SSO** .

    1. V seznamu režim jednotného přihlašování **(SSO** ) vyberte **volitelné**. Vyberte **Uložit**.

       > [!NOTE]
       > Volitelný režim je pouze pro testování. Až budete s konfigurací spokojeni, vyberte v seznamu Režim přihlašování jednotného **přihlašování** u všech uživatelů možnost **požadováno** k vynucení ověření pomocí služby Azure AD.

    1. V programu Poznámkový blok otevřete certifikát, který jste si stáhli v předchozí části. Zkopírujte obsah certifikátu bez prvního řádku (**-----začít certifikát-----**) nebo poslední řádek (**-----konec certifikátu-----**). Vložte obsah certifikátu do pole **certifikát SSO-x. 509** a pak vyberte **Uložit**.

### <a name="create-nintex-promapp-test-user"></a>Vytvořit testovacího uživatele Nintex Promapp

V této části se na Nintex Promapp vytvoří uživatel s názvem B. Simon. Nintex Promapp podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Nintex Promapp neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Nintex Promapp, měli byste být automaticky přihlášeni k Nintex Promapp, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Nintex Promapp s Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png