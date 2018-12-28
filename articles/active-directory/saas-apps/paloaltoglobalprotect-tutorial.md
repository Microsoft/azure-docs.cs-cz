---
title: 'Kurz: Integrace Azure Active Directory s Palo Alto Networks - GlobalProtect | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/11/2018
ms.author: jeedes
ms.openlocfilehash: 06d3d834e1115e43757cee37d44d27cd517acd44
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793425"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>Kurz: Integrace Azure Active Directory s Palo Alto Networks - GlobalProtect

V tomto kurzu se dozvíte, jak integrovat Palo Alto Networks - GlobalProtect se službou Azure Active Directory (Azure AD).
Integrace Palo Alto Networks - GlobalProtect s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Palo Alto Networks - GlobalProtect.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Palo Alto Networks - GlobalProtect (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Palo Alto Networks - GlobalProtect, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto Networks - GlobalProtect jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje GlobalProtect Palo Alto Networks - **SP** jednotné přihlašování zahájené pomocí
* Podporuje GlobalProtect Palo Alto Networks - **JIT** zřizování uživatelů

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Přidání Palo Alto Networks - GlobalProtect z Galerie

Konfigurace integrace Palo Alto Networks - GlobalProtect do služby Azure AD, budete muset přidat Palo Alto Networks - GlobalProtect z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Palo Alto Networks - GlobalProtect z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Palo Alto Networks - GlobalProtect**vyberte **Palo Alto Networks - GlobalProtect** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace .

     ![Palo Alto Networks - GlobalProtect v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks – GlobalProtect podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování k práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Palo Alto Networks - GlobalProtect potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Palo Alto Networks - GlobalProtect, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Palo Alto Networks - GlobalProtect Single Sign-On](#configure-palo-alto-networks---globalprotect-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvoření Palo Alto Networks - GlobalProtect testovacího uživatele](#create-palo-alto-networks---globalprotect-test-user)**  – Pokud chcete mít protějšek Britta Simon Palo Alto Networks - GlobalProtect, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Palo Alto Networks - GlobalProtect, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Palo Alto Networks - GlobalProtect** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Palo Alto Networks - GlobalProtect domény a adresy URL jednotné přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<Customer Firewall URL>`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [Palo Alto Networks - tým podpory GlobalProtect klienta](https://support.paloaltonetworks.com/support) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Palo Alto Networks - GlobalProtect aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název | Zdrojový atribut|
    | ------|--------- |
    | uživatelské jméno  | user.userprincipalname  |
    | | |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **soubor XML s metadaty**z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

8. Na **nastavení Palo Alto Networks - GlobalProtect** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-palo-alto-networks---globalprotect-single-sign-on"></a>Konfigurace Palo Alto Networks - GlobalProtect jednotné přihlašování

1. Otevřete uživatelské rozhraní Správce brány Firewall sítě Palo Alto jako správce v jiném okně prohlížeče.

2. Klikněte na **zařízení**.

    ![Konfigurace Palo Alto jednotného přihlašování](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Vyberte **zprostředkovatele Identity SAML** na levém navigačním panelu a klikněte na tlačítko "Import" k importu souboru metadat.

    ![Konfigurace Palo Alto jednotného přihlašování](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Proveďte následující akce v okně Import

    ![Konfigurace Palo Alto jednotného přihlašování](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. V **název profilu** textového pole zadejte například název Azure AD GlobalProtect.

    b. V **metadat zprostředkovatele Identity**, klikněte na tlačítko **Procházet** a vyberte soubor metadata.xml, který jste si stáhli z webu Azure portal

    c. Klikněte na tlačítko **OK**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Palo Alto Networks - GlobalProtect.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Palo Alto Networks - GlobalProtect**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Palo Alto Networks - GlobalProtect**.

    ![Sítě Palo Alto - GlobalProtect odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Vytvoření Palo Alto Networks - GlobalProtect testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Palo Alto Networks - GlobalProtect. Palo Alto Networks - GlobalProtect podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Palo Alto Networks - GlobalProtect, vytvoří se nový po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Palo Alto Networks - GlobalProtect dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni ke Palo Alto Networks - GlobalProtect, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)