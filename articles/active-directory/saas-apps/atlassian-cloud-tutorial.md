---
title: 'Kurz: Integrace Azure Active Directory s cloudem od společnosti Atlassian | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cloud od společnosti Atlassian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.openlocfilehash: 2089f600d89d1b072829976e2b3257bbf086bafa
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098042"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Kurz: Integrace Azure Active Directory s cloudem od společnosti Atlassian

V tomto kurzu se dozvíte, jak integrovat Cloud od společnosti Atlassian s Azure Active Directory (Azure AD).
Integrace od společnosti Atlassian cloudu s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup pro Cloud od společnosti Atlassian.
* Uživatelům se automaticky přihlášeni k Cloud od společnosti Atlassian (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s cloudem od společnosti Atlassian, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Cloud od společnosti Atlassian jednotného přihlašování povolená předplatného
* Chcete-li povolit zabezpečení kontrolního výrazu SAML (Markup Language) jednotného přihlašování pro Cloud od společnosti Atlassian produkty, nastavení přístupu od společnosti Atlassian. Další informace o [přístupu od společnosti Atlassian]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Cloud od společnosti Atlassian **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Přidání Cloud od společnosti Atlassian z Galerie

Pokud chcete nakonfigurovat integraci od společnosti Atlassian Cloud do služby Azure AD, budete muset přidat Cloud od společnosti Atlassian z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Cloud od společnosti Atlassian z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cloud od společnosti Atlassian**vyberte **Cloud od společnosti Atlassian** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Cloud od společnosti Atlassian v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s cloudem od společnosti Atlassian podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v cloudu od společnosti Atlassian.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s cloudem od společnosti Atlassian, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace od společnosti Atlassian cloudu Single Sign-On](#configure-atlassian-cloud-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Cloud od společnosti Atlassian](#create-atlassian-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon od společnosti Atlassian cloudu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s cloudem od společnosti Atlassian, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Cloud od společnosti Atlassian** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![[Název aplikace] Domény a adresy URL jednotného přihlašování – informace](common/idp-relay.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://auth.atlassian.com/saml/<unique ID>`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Klikněte na tlačítko **nastavit další adresy URL**.

    d. V **stav přenosu** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Předchozí hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Zobrazí se tyto skutečné hodnoty z konfigurace SAML služby Cloud od společnosti Atlassian obrazovky, který je vysvětlen později v tomto kurzu.

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![[Název aplikace] Domény a adresy URL jednotného přihlašování – informace](common/both-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Předchozí přihlašovací adresa URL není skutečný. Skutečná adresa URL přihlašování zaktualizujte příslušnou hodnotu. Kontakt [tým podpory od společnosti Atlassian cloudu klienta](https://support.atlassian.com/) tuto výhodu získáte.

6. Od společnosti Atlassian cloudové aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Očekává, že aplikace Cloud od společnosti Atlassian **nameidentifier** namapovat s **user.mail**, takže budete muset upravit mapování atributů po kliknutí na **upravit** ikonu a změňte mapování atributů.

    ![image](common/edit-attribute.png)

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Na **nastavit Cloud od společnosti Atlassian** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-atlassian-cloud-single-sign-on"></a>Nakonfigurujte Cloud od společnosti Atlassian jednotného přihlašování

1. Chcete-li získat jednotné přihlašování nakonfigurované pro vaši aplikaci, přihlaste se k portálu od společnosti Atlassian pomocí přihlašovacích údajů správce.

2. Budete muset ověřit doménu před přechodem ke konfiguraci jednotného přihlašování. Další informace najdete v tématu [ověření domény od společnosti Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentu.

3. V levém podokně vyberte **SAML jednotného přihlašování**. Pokud jste tak již neučinili, přihlásit k odběru pro správce identit od společnosti Atlassian.

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

4. V **konfigurace přidat SAML** okno, postupujte takto:

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. V **zprostředkovatele Identity Entity ID** vložte ID entity SAML, který jste zkopírovali z portálu Azure portal.

    b. V **zprostředkovatele Identity adresu URL jednotného přihlašování** pole, vložte adresu URL služby jednotného přihlašování SAML, který jste zkopírovali z portálu Azure portal.

    c. Otevřete stažený certifikát z portálu Azure portal v souboru .txt, zkopírujte hodnotu (bez *začít certifikát* a *End Certificate* řádků) a vložte jej do **veřejné X509 certifikát** pole.

    d. Klikněte na tlačítko **uložit konfiguraci**.

5. Aby bylo zajištěno, že jste nastavili správné adresy URL, aktualizujte nastavení Azure AD následujícím způsobem:

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. V okně SAML Kopírovat **SP Identity ID** a potom na webu Azure portal, v části Cloud od společnosti Atlassian **domény a adresy URL**, vložte ji **identifikátor** pole.

    b. V okně SAML Kopírovat **SP adresa URL služby příjemce kontrolního výrazu** a potom na webu Azure portal, v části Cloud od společnosti Atlassian **domény a adresy URL**, vložte ji **adresy URL odpovědi** pole. Přihlašovací adresa URL je adresa URL tenanta Cloud od společnosti Atlassian.

    > [!NOTE]
    > Pokud jste stávající zákazník, po aktualizaci **SP Identity ID** a **SP adresa URL služby příjemce kontrolního výrazu** hodnoty na webu Azure Portal, vyberte **Ano, aktualizujte konfiguraci**. Pokud jste nového zákazníka, můžete tento krok přeskočit.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu pro Cloud od společnosti Atlassian.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Cloud od společnosti Atlassian**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Cloud od společnosti Atlassian**.

    ![V seznamu aplikací na odkaz Cloud od společnosti Atlassian](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-atlassian-cloud-test-user"></a>Vytvořit testovacího uživatele Cloud od společnosti Atlassian

Pokud chcete povolit Azure AD uživatelům umožní přihlásit se na Cloud od společnosti Atlassian, zřízení uživatelských účtů v cloudu od společnosti Atlassian ručně následujícím způsobem:

1. V **správu** vyberte **uživatelé**.

    ![The Atlassian Cloud Users link](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Chcete-li vytvořit uživatele v cloudu od společnosti Atlassian, vyberte **pozvání uživatele**.

    ![Vytvoření uživatele služby Cloud od společnosti Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. V **e-mailová adresa** zadejte e-mailovou adresu uživatele a pak přiřadit přístup k aplikaci.

    ![Vytvoření uživatele služby Cloud od společnosti Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Chcete-li odeslat e-mailová pozvánka pro uživatele, vyberte **pozvat uživatele**. Uživateli se odešle e-mailovou pozvánku a po přijetí e-mailové pozvánce, je uživatel aktivní v systému.

> [!NOTE]
> Můžete také hromadně – vytvořit uživatele tak, že vyberete **Hromadně vytvořit** tlačítko **uživatelé** části.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Cloud od společnosti Atlassian na přístupovém panelu, vám by měl být automaticky přihlášeni cloud od společnosti Atlassian, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   