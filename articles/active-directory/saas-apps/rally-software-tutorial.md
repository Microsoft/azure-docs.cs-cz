---
title: 'Kurz: Integrace Azure Active Directory pomocí technologie Rally softwaru | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a technologie Rally softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: de84d03c3e0e433dbe7bc24c47b1766b32ad7bc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093172"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Kurz: Integrace Azure Active Directory pomocí technologie Rally softwaru

V tomto kurzu se dozvíte, jak integrovat technologie Rally softwaru se službou Azure Active Directory (Azure AD).
Integrace technologie Rally Software s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k technologie Rally softwaru.
* Uživatelům se automaticky přihlášeni k technologie Rally softwaru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí technologie Rally softwaru, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Technologie Rally jednotné přihlašování povoleno předplatné softwaru

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Technologie Rally softwaru podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-rally-software-from-the-gallery"></a>Přidání technologie Rally softwaru z Galerie

Konfigurace integrace technologie Rally softwaru do služby Azure AD, budete muset přidat technologie Rally Software z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat technologie Rally softwaru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **technologie Rally softwaru**vyberte **technologie Rally softwaru** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Technologie Rally Software v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí technologie Rally Software založený na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské technologie Rally softwaru.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí technologie Rally softwaru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace technologie Rally softwaru Single Sign-On](#configure-rally-software-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele technologie Rally softwaru](#create-rally-software-test-user)**  – Pokud chcete mít protějšek Britta Simon technologie Rally Software, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí technologie Rally softwaru, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **technologie Rally softwaru** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Technologie Rally softwaru domény a adresy URL jednotné přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<tenant-name>.rally.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory technologie Rally softwarového klienta](https://help.rallydev.com/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení technologie Rally softwaru** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-rally-software-single-sign-on"></a>Konfigurace technologie Rally softwaru jednotného přihlašování

1. Přihlaste se k vaší **technologie Rally softwaru** tenanta.

2. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a pak vyberte **předplatné**.
   
    ![Předplatné](./media/rally-software-tutorial/ic769531.png "předplatného")

3. Klikněte na tlačítko **akce** tlačítko. Vyberte **upravit předplatné** v horní pravé straně panelu nástrojů.

4. Na **předplatné** dialogového okna stránky, proveďte následující kroky a pak klikněte na **uložit a zavřít**:
   
    ![Ověřování](./media/rally-software-tutorial/ic769542.png "ověřování")
   
    a. Vyberte **technologie Rally nebo jednotného přihlašování ověřování** z rozevíracího seznamu ověřování.

    b. V **adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal. 

    c. V **jednotné odhlašování** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k technologie Rally softwaru.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **technologie Rally softwaru**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **technologie Rally softwaru**.

    ![Technologie Rally softwaru odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-rally-software-test-user"></a>Vytvořit Software technologie Rally testovacího uživatele

Pro uživatele Azure AD bude moct přihlásit musí být poskytnuty k technologie Rally softwarová aplikace pomocí jejich uživatelská jména Azure Active Directory.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vašemu tenantovi technologie Rally softwaru.

2. Přejděte na **nastavení \> uživatelé**a potom klikněte na tlačítko **+ přidat nový**.
   
    ![Uživatelé](./media/rally-software-tutorial/ic781039.png "uživatelů")

3. Zadejte název do textového pole nového uživatele a potom klikněte na tlačítko **přidat s podrobnostmi**.

4. V **vytvořit uživatele** části, proveďte následující kroky:
   
    ![Vytvoření uživatele](./media/rally-software-tutorial/ic781040.png "vytvoření uživatele")

    a. V **uživatelské jméno** textového pole, zadejte jméno uživatele, jako jsou **Brittsimon**.
   
    b. V **e-mailovou adresu** textového pole zadejte e-mailu uživatele, jako je brittasimon@contoso.com.

    c. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    d. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **Simon**.

    e. Klikněte na tlačítko **uložit a zavřít**.

   >[!NOTE]
   >Další nástroje pro tvorbu technologie Rally softwaru uživatelského účtu nebo rozhraní API poskytovaných technologie Rally softwaru můžete použít ke zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici technologie Rally softwaru na přístupovém panelu, můžete by měl být automaticky přihlášeni na technologie Rally Software, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

