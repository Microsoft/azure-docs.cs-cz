---
title: 'Kurz: Integrace Azure Active Directory s Trisotech digitální Enterprise Server | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trisotech digitální Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: c5d1a791de5c295b4644e5633983a5369200498e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65888387"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Kurz: Integrace Azure Active Directory s Trisotech digitální Enterprise Server

V tomto kurzu se dozvíte, jak integrovat Trisotech digitální Enterprise serveru s Azure Active Directory (Azure AD).
Integrace Trisotech digitální Enterprise serveru s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Trisotech digitální Enterprise Server.
* Uživatelům se automaticky přihlášeni k Trisotech digitální Enterprise serveru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Trisotech digitální Enterprise serveru, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Trisotech digitální Enterprise serveru jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Trisotech digitální Enterprise Server **SP** jednotné přihlašování zahájené pomocí

* Podporuje Trisotech digitální Enterprise Server **JIT** zřizování uživatelů

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Přidání Trisotech digitální Enterprise serveru z Galerie

Ke konfiguraci integrace Trisotech digitální Enterprise serveru do služby Azure AD, budete muset přidat Trisotech digitální Enterprise serveru z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Trisotech digitální Enterprise serveru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Trisotech digitální Enterprise Server**vyberte **Trisotech digitální Enterprise Server** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Trisotech digitální Enterprise Server v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí Trisotech digitální Enterprise serveru podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Trisotech digitální Enterprise serveru.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Trisotech digitální Enterprise serveru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Trisotech digitální Enterprise serveru Single Sign-On](#configure-trisotech-digital-enterprise-server-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Trisotech digitální Enterprise Server](#create-trisotech-digital-enterprise-server-test-user)**  – Pokud chcete mít protějšek Britta Simon Trisotech digitální Enterprise serveru, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Trisotech digitální Enterprise serveru, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Trisotech digitální Enterprise Server** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Trisotech digitální Enterprise serveru domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.trisotech.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Trisotech digitální Enterprise serveru Client](mailto:support@trisotech.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Konfigurace digitálního Trisotech Enterprise serveru jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Trisotech digitální podniková konfigurace serveru jako správce.

2. Klikněte na **ikonu nabídky** a pak vyberte **správu**.

    ![Konfigurace jednotného přihlašování](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Vyberte **zprostředkovatel uživatele**.

    ![Konfigurace jednotného přihlašování](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. V **uživatele poskytovatele konfigurace** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Vyberte **zabezpečené kontrolní výraz Markup Language 2 (SAML 2)** z rozevíracího seznamu v **metodu ověřování**.

    b. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, kterou jste zkopírovali formuláře na webu Azure portal.

    c. V **ID aplikace** textového pole zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.trisotech.com`.

    d. Klikněte na **Uložit**.

    e. Zadejte název domény v **povolené domén (prázdná hodnota znamená všem uživatelům)** textového pole automaticky přiřadí licence pro porovnávání domén povolené uživatele

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
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Trisotech digitální Enterprise serveru.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Trisotech digitální Enterprise Server**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Trisotech digitální Enterprise Server**.

    ![Odkaz Trisotech digitální Enterprise Server v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Vytvořit testovacího uživatele Trisotech digitální Enterprise Server

V této části je uživateli Britta Simon vytvořit Trisotech digitální Enterprise serveru. Trisotech digitální Enterprise Server podporuje zřizování uživatelů v čase, je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Trisotech digitální Enterprise serveru, vytvoří se nový po ověření.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Trisotech digitální Enterprise Server](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Trisotech digitální Enterprise Server na přístupovém panelu, můžete by měl být automaticky přihlášeni k Trisotech digitální Enterprise serveru SQL, pro kterou nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

