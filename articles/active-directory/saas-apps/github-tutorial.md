---
title: 'Kurz: Integrace Azure Active Directory s Githubem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Githubu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: 41e8fb40f07a88cb6fa2108a38db7f973fccd2f9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607794"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Kurz: Integrace Azure Active Directory s Githubem

V tomto kurzu se dozvíte, jak k integraci Githubu s Azure Active Directory (Azure AD).

Integrace Githubu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke Githubu.
- Můžete povolit uživatelům, aby automaticky získat přihlášeného ke Githubu (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Githubem, potřebujete následující položky:

- Předplatné Azure AD
- Na Githubu jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Githubu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-github-from-the-gallery"></a>Přidání Githubu z Galerie
Pokud chcete nakonfigurovat integraci Githubu do služby Azure AD, budete muset přidat GitHub na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání Githubu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![image](./media/github-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/github-tutorial/a_select_app.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/github-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **Githubu**vyberte **Githubu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![image](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Githubu podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Githubu je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě GitHub.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Githubem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Githubu](#create-a-github-test-user)**  – Pokud chcete mít protějšek Britta Simon v Githubu, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Githubu.

**Ke konfiguraci Azure AD jednotné přihlašování s Githubem, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **Githubu** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/github-tutorial/b1_b2_select_sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/github-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![image](./media/github-tutorial/tutorial_github_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://github.com/orgs/<entity-id>/sso`

    b. V **identifikátor (Entity ID)** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Tady doporučujeme používat jedinečnou hodnotu řetězce v identifikátoru. Přejděte do části správce Githubu k načtení těchto hodnot.

5. GitHub aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Klikněte na tlačítko **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](./media/github-tutorial/i3-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    a. Klikněte na tlačítko **upravit** tlačítko Otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/github-tutorial/i2-attribute.png)

    ![image](./media/github-tutorial/i4-attribute.png)

    b. Z **zdrojový atribut** vyberte hodnotu atributu.

    c. Klikněte na **Uložit**.

7. V **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** a uložte ho do počítače.

    ![image](./media/github-tutorial/tutorial_github_certficate.png)

8. Na **nastavte GitHub** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![image](./media/github-tutorial/d1_samlsonfigure.png)

9. V okně jiné webové prohlížeče přihlaste jako správce webu organizace GitHub.

10. Přejděte do **nastavení** a klikněte na tlačítko **zabezpečení**

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_03.png)

11. Zkontrolujte **ověřování povolit SAML** pole odhalení pole Konfigurace jednotného přihlašování. Potom použijte jednu hodnotu adresy URL přihlašování aktualizovat adresy jednotného přihlašování – konfigurace služby Azure AD.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_13.png)

12. Vyplňte následující pole:

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. V **přihlašovací adresa URL** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    b. V **vystavitele** vložit do textového pole **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. Otevřete stažený certifikát z webu Azure portal v programu Poznámkový blok, vložte obsah do **veřejný certifikát** textového pole.

    d. Klikněte na **upravit** ikona pro úpravy **podpisová metoda** a **metodu Digest** z **RSA SHA1** a **SHA1**k **RSA-SHA256** a **SHA256** jak je znázorněno níže.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

13. Klikněte na **Konfigurace testu SAML** potvrdit, že žádné chyby ověření nebo chyb během jednotného přihlašování.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_06.png)

14. Klikněte na **Uložit**.

> [!NOTE]
> Jednotné přihlašování ve službě GitHub ověřuje pro konkrétní organizaci v Githubu a nenahrazuje ověřování Githubu, samotného. Proto pokud vypršela platnost relace webu github.com uživatele, můžete být vyzváni k ověření pomocí Githubu ID a hesla během procesu přihlašování.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/github-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/github-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/github-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="create-a-github-test-user"></a>Vytvoření zkušebního uživatele Githubu

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

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke Githubu.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/github-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **Githubu**.

    ![image](./media/github-tutorial/tutorial_github_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/github-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/github-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici GitHub na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Githubu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


