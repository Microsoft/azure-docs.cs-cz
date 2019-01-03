---
title: 'Kurz: Integrace Azure Active Directory s Slack | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: fd868e02987c61e6bdd992c45a95cb775d1778ca
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969538"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Kurz: Integrace Azure Active Directory s Slack

V tomto kurzu se dozvíte, jak integrovat Slack s Azure Active Directory (Azure AD).
Integrace Slack s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Slack.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Slack (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Slack, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Slack jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Slack podporuje **SP** jednotné přihlašování zahájené pomocí
* Slack podporuje **JIT** zřizování uživatelů
* Slack podporuje [ **automatizovaná** zřizování uživatelů](slack-provisioning-tutorial.md)

## <a name="adding-slack-from-the-gallery"></a>Přidání Slack z Galerie

Konfigurace integrace Slack do služby Azure AD, budete muset přidat Slack z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Slack z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Slack**vyberte **Slack** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![V seznamu výsledků Slack](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Slack podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Slack.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Slack, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Slack Single Sign-On](#configure-slack-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Slack testovacího uživatele](#create-slack-test-user)**  – Pokud chcete mít protějšek Britta Simon Slack, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Slack, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Slack** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Slack domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.slack.com`.

    b. V **identifikátor (Entity ID)** textové pole, zaktualizujte příslušnou hodnotu přihlašovací adresa URL. Toto je vaše doména pracovního prostoru. Například: `https://contoso.slack.com`.

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory klienta Slack](https://slack.com/help/contact) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Slack aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

    > [!NOTE] 
    > Pokud máte uživatele, kteří se přiřadí **e-mailová adresa** není na licenci Office 365 **User.Email** deklarace identity se nezobrazí v tokenu SAML. V těchto případech doporučujeme používat **user.userprincipalname** jako **User.Email** atribut hodnota k namapování jako **jedinečný identifikátor** místo.

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název | Zdrojový atribut |
    | --- | --- |
    | křestní_jméno | user.givenname |
    | Příjmení | user.surname |
    | User.Email | User.Mail |
    | User.Username | user.userprincipalname |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Na **nastavení Slack** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-slack-single-sign-on"></a>Konfigurace Slack jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu Slack společnosti jako správce.

2. Přejděte do **Microsoft Azure AD** pak přejděte na **nastavení týmu**.

     ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial_slack_001.png)

3. V **nastavení týmu** klikněte na tlačítko **ověřování** kartu a potom klikněte na tlačítko **změnit nastavení**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial_slack_002.png)

4. Na **nastavení ověřování SAML** dialogového okna, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial_slack_003.png)

    a.  V **SAML 2.0 koncový bod (HTTP)** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    b.  V **Vystavitel zprostředkovatele Identity** textového pole vložte hodnotu **Azure Ad identifikátor**, který jste zkopírovali z portálu Azure portal.

    c.  V poznámkovém bloku otevřete soubor stažený certifikát, zkopírujte obsah ho do schránky a vložte jej do **veřejný certifikát** textového pole.

    d. Výše uvedené tři nastavení nakonfigurujte podle potřeby pro váš tým Slack. Další informace o nastavení, najdete **Příručka pro konfiguraci jednotného přihlašování pro Slack** tady. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klikněte na tlačítko **uložit konfiguraci**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na Slack.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Slack**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Slack**.

    ![V seznamu aplikací odkaz Slack](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-slack-test-user"></a>Vytvoření Slack testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Slack. Slack podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Slack, pokud ještě neexistuje. Slack také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](slack-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory Slack](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect je nástroj pro synchronizaci, které můžete synchronizovat místní Active Directory identity do služby Azure AD a pak tyto synchronizovaných uživatelů můžete také použít aplikace, jak ostatní uživatelé v cloudu.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na Slack dlaždici na přístupovém panelu, které by měl být automaticky přihlášeni k Slack, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Konfigurace zřizování uživatelů](slack-provisioning-tutorial.md)