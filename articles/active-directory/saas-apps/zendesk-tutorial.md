---
title: 'Kurz: Integrace Azure Active Directory s Zendesku | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 2f58bb8a1a3a0f0593513570355b8d3ba46816b9
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825829"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Kurz: Integrace Azure Active Directory s Zendesku.

V tomto kurzu se dozvíte, jak integrovat Zendesku s Azure Active Directory (Azure AD).
Integrace Zendesku s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k službě Zendesk.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k službě Zendesk (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zendesku, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Zendesk jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje platformy Zendesk **SP** jednotné přihlašování zahájené pomocí

* Podporuje platformy Zendesk [ **automatizovaná** zřizování uživatelů](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Přidání Zendesku z Galerie

Pokud chcete nakonfigurovat integraci Zendesku do služby Azure AD, budete muset přidat Zendesku z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zendesku z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zendesku**vyberte **Zendesku** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Zendesku v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Zendesku podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelem v systému Zendesk.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zendesku, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace platformy Zendesk Single Sign-On](#configure-zendesk-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Zendesku](#create-zendesk-test-user)**  – Pokud chcete mít protějšek Britta Simon v systému Zendesk, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Zendesku, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Zendesku** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Zendesk domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.zendesk.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `<subdomain>.zendesk.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Zendesku klienta](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Zendesk aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Neexistují žádné povinné atributy SAML, ale můžete také spravovat z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, vyberte příslušný atribut hodnotu.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Chcete-li přidat atributy, které nejsou ve službě Azure AD ve výchozím nastavení použijete atributy rozšíření. Klikněte na tlačítko [atributy uživatele, které je možné nastavit v SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) zobrazíte úplný seznam atributů SAML, který **Zendesku** přijímá.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** tématu, zkopírujte **kryptografický otisk** a uložte ho do počítače.

    ![Zkopírujte hodnotu kryptografického otisku](common/copy-certificatethumbprint.png)

8. Na **nastavení Zendesku** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

9. Existují dva způsoby ve kterých by šlo Zendesku - automatické a ruční.
  
10. K automatizaci konfigurace v rámci Zendesku, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Zendesku** nasměruje na aplikace platformy Zendesk. Odtud zadejte přihlašovací údaje správce pro přihlášení do Zendesku. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat části **konfigurace Zendesku Single Sign-On**.

     ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Konfigurace přihlašování jedním Zendesku.

1. Pokud chcete nastavit Zendesku ručně, otevřete nové okno webového prohlížeče a přihlaste na webu společnosti Zendesku jako správce a proveďte následující kroky:

2. Klikněte na tlačítko **správce**.

3. V levém navigačním podokně klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **zabezpečení**.

4. Na **zabezpečení** stránce, proveďte následující kroky:

    ![Zabezpečení](././media/zendesk-tutorial/ic773089.png "zabezpečení")

    ![Jednotné přihlašování](././media/zendesk-tutorial/ic773090.png "jednotného přihlašování")

    a. Klikněte na tlačítko **agenti pro správu and** kartu.

    b. Vyberte **jednotné přihlašování (SSO) a SAML**a pak vyberte **SAML**.

    c. V **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    d. V **vzdálené adresy URL odhlašovací** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    e. V **otisku certifikátu** vložit do textového pole **kryptografický otisk** hodnotu certifikát, který jste zkopírovali z portálu Azure portal.

    f. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k službě Zendesk.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Zendesku**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Zendesku**.

    ![Propojení Zendesku v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-zendesk-test-user"></a>Vytvořit testovacího uživatele Zendesku.

Cílem této části je vytvořte uživatele Britta Simon v systému Zendesk. Zendesk podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](Zendesk-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

> [!NOTE]
> **Koncový uživatel** účty se zřizují automaticky při přihlášení. **Agent** a **správce** účty musí být ručně zřízené v **Zendesku** před přihlášením.

1. Přihlaste se k vaší **Zendesku** tenanta.

2. Vyberte **seznam zákazníků** kartu.

3. Vyberte **uživatele** kartu a klikněte na tlačítko **přidat**.

    ![Přidat uživatele](././media/zendesk-tutorial/ic773632.png "přidat uživatele")
4. Typ **název** a **e-mailu** existujícího účtu služby Azure AD ke zřízení a potom klikněte na tlačítko **Uložit**.

    ![Nový uživatel](././media/zendesk-tutorial/ic773633.png "nového uživatele")

> [!NOTE]
> Můžete použít jakékoli jiné Zendesku uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Zendesku uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zendesku na přístupovém panelu, vám by měl být automaticky přihlášeni k službě Zendesk, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](zendesk-provisioning-tutorial.md) 

