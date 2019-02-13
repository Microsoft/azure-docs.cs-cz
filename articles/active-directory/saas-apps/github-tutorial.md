---
title: 'Kurz: Integrace Azure Active Directory s Githubem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Githubu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a04b25904f41da3e600e3d1e6fcea8cb760d7502
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163015"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Kurz: Integrace Azure Active Directory s Githubem

V tomto kurzu se dozvíte, jak k integraci Githubu s Azure Active Directory (Azure AD).
Integrace Githubu s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke Githubu.
* Můžete povolit uživatelům, aby se automaticky přihlášeni ke Githubu (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Githubem, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* GitHub jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* GitHub podporuje **SP** jednotné přihlašování zahájené pomocí

* GitHub podporuje [ **automatizovaná** zřizování uživatelů](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>Přidání Githubu z Galerie

Pokud chcete nakonfigurovat integraci Githubu do služby Azure AD, budete muset přidat GitHub na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání Githubu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Githubu**vyberte **Githubu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![GitHub v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí Githubu podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě GitHub.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Githubem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Githubu Single Sign-On](#configure-github-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Githubu](#create-github-test-user)**  – Pokud chcete mít protějšek Britta Simon v Githubu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Githubem, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Githubu** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![GitHub domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://github.com/orgs/<entity-id>/sso`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Tady doporučujeme používat jedinečnou hodnotu řetězce v identifikátoru. Přejděte do části správce Githubu k načtení těchto hodnot.

5. GitHub aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Očekává, že aplikace Githubu **nameidentifier** namapovat s **user.mail**, takže budete muset kliknout na Upravit mapování atributů **upravit** ikonu a změnit atribut mapování.

    ![image](common/edit-attribute.png)

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavte GitHub** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-github-single-sign-on"></a>Konfigurace přihlašování jedním Githubu

1. V okně jiné webové prohlížeče přihlaste jako správce webu organizace GitHub.

2. Přejděte do **nastavení** a klikněte na tlačítko **zabezpečení**

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Zkontrolujte **ověřování povolit SAML** pole odhalení pole Konfigurace jednotného přihlašování. Potom použijte jednu hodnotu adresy URL přihlašování aktualizovat adresy jednotného přihlašování – konfigurace služby Azure AD.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Vyplňte následující pole:

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. V **přihlašovací adresa URL** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    b. V **vystavitele** vložit do textového pole **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. Otevřete stažený certifikát z webu Azure portal v programu Poznámkový blok, vložte obsah do **veřejný certifikát** textového pole.

    d. Klikněte na **upravit** ikona pro úpravy **podpisová metoda** a **metodu Digest** z **RSA SHA1** a **SHA1**k **RSA-SHA256** a **SHA256** jak je znázorněno níže.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Klikněte na **Konfigurace testu SAML** potvrdit, že žádné chyby ověření nebo chyb během jednotného přihlašování.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Klikněte na **Uložit**.

> [!NOTE]
> Jednotné přihlašování ve službě GitHub ověřuje pro konkrétní organizaci v Githubu a nenahrazuje ověřování Githubu, samotného. Proto pokud vypršela platnost relace webu github.com uživatele, můžete být vyzváni k ověření pomocí Githubu ID a hesla během procesu přihlašování.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke Githubu.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Githubu**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Githubu**.

    ![Odkaz na Githubu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-github-test-user"></a>Vytvořit testovacího uživatele Githubu

Cílem této části je vytvořte uživatele Britta Simon v Githubu. GitHub podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](github-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se na web GitHub společnosti jako správce.

2. Klikněte na tlačítko **lidé**.

    ![Lidé](./media/github-tutorial/tutorial_github_config_github_08.png "osoby")

3. Klikněte na tlačítko **pozvání členů**.

    ![Pozvat uživatele](./media/github-tutorial/tutorial_github_config_github_09.png "pozvat uživatele")

4. Na **pozvání členů** dialogového okna stránky, proveďte následující kroky:

    a. V **e-mailu** textového pole zadejte e-mailovou adresu účtu Britta Simon.

    ![Pozvat](./media/github-tutorial/tutorial_github_config_github_10.png "pozvat")

    b. Klikněte na tlačítko **poslat pozvánku**.

    ![Pozvat](./media/github-tutorial/tutorial_github_config_github_11.png "pozvat")

    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici GitHub na přístupovém panelu, vám by měl být automaticky přihlášeni ke Githubu, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
