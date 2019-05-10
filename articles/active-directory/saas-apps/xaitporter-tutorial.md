---
title: 'Kurz: Integrace Azure Active Directory s XaitPorter | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: ada93903113cedd68e23ec66a36dceef99ec1233
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192089"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Kurz: Integrace Azure Active Directory s XaitPorter

V tomto kurzu se dozvíte, jak integrovat XaitPorter s Azure Active Directory (Azure AD).
XaitPorter integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k XaitPorter.
* Můžete povolit uživatelům být automaticky přihlášeni k XaitPorter (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s XaitPorter, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* XaitPorter jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje XaitPorter **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-xaitporter-from-the-gallery"></a>Přidání XaitPorter z Galerie

Konfigurace integrace XaitPorter do služby Azure AD, budete muset přidat XaitPorter z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat XaitPorter z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **XaitPorter**vyberte **XaitPorter** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![XaitPorter v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí XaitPorter podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v XaitPorter.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s XaitPorter, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace XaitPorter Single Sign-On](#configure-xaitporter-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele XaitPorter](#create-xaitporter-test-user)**  – Pokud chcete mít protějšek Britta Simon XaitPorter, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s XaitPorter, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **XaitPorter** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![XaitPorter domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.xaitporter.com/saml/login`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.xaitporter.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory XaitPorter klienta](https://www.xait.com/support/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

6. Zadejte **IP adresu** nebo **adresa Url federačních metadat aplikace** k [SmartRecruiters tým podpory](https://www.smartrecruiters.com/about-us/contact-us/)tak, aby XaitPorter můžete zajistit, že IP adresa je dostupný z vašeho Konfigurace seznamu povolených IP adres na své straně XaitPorter instance. 

### <a name="configure-xaitporter-single-sign-on"></a>Konfigurace XaitPorter jednotné přihlašování

1. K automatizaci konfigurace v rámci XaitPorter, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení XaitPorter** nasměruje na XaitPorter aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do XaitPorter. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 až 6.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete nastavit XaitPorter ručně, otevřete nové okno webového prohlížeče a přihlaste na webu společnosti XaitPorter jako správce a proveďte následující kroky:

4. Klikněte na **správce**.

    ![Konfigurace jednotného přihlašování](./media/xaitporter-tutorial/user1.png)

5. Vyberte **spravovat Single Sign-On** z **nastavení systému** rozevíracího seznamu.

    ![Konfigurace jednotného přihlašování](./media/xaitporter-tutorial/user2.png)

6. V **SPRAVOVAT jednotné přihlašování** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/xaitporter-tutorial/user3.png)

    a. Vyberte **povolit jednotné přihlašování, ověřování**.

    b. V **nastavení zprostředkovatele Identity** vložit do textového pole **adresa Url federačních metadat aplikace** zkopírovanou z webu Azure portal a klikněte na tlačítko **načíst**.

    c. Vyberte **povolit automatické vytvoření uživatelů**.

    d. Klikněte na **OK**.

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

V této části je povolit Britta Simon k udělení přístupu k XaitPorter použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **XaitPorter**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **XaitPorter**.

    ![Odkaz XaitPorter v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-xaitporter-test-user"></a>Vytvoření XaitPorter testovacího uživatele

V této části vytvoříte uživatele v XaitPorter jako Britta Simon. Práce s [tým podpory XaitPorter klienta](https://www.xait.com/support/) přidat uživatele na platformě XaitPorter. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici XaitPorter na přístupovém panelu, můžete by měl být automaticky přihlášeni k XaitPorter, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

