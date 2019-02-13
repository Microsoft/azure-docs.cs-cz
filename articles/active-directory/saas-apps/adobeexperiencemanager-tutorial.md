---
title: 'Kurz: Integrace s Azure Active Directory s Adobe Experience Manager | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b24d1040c75e93bf26639e33aa46001b4e9c008
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170648"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Kurz: Integrace s Azure Active Directory s Adobe Experience Manager

V tomto kurzu se dozvíte, jak integrovat Adobe Experience Manager se službou Azure Active Directory (Azure AD).
Integrace s Azure AD Adobe Experience Manager poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k aplikaci Adobe Experience Manager.
* Uživatelům se automaticky přihlášeni k Adobe Experience Manager (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Adobe Experience Manager, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Adobe Experience Manager jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Adobe Experience Manager podporuje **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

* Adobe Experience Manager podporuje **JIT** zřizování uživatelů

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Přidání Adobe Experience Manager z Galerie

Konfigurace integrace nástroje Adobe Experience Manager do služby Azure AD, budete muset přidat Adobe Experience Manager na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Adobe Experience Manager z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Adobe Experience Manager**vyberte **Adobe Experience Manager** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Adobe Experience Manager v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s [název aplikace] podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v [název aplikace].

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s [název aplikace], které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Adobe Experience Manager Single Sign-On](#configure-adobe-experience-manager-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Adobe Experience Manager](#create-adobe-experience-manager-test-user)**  – Pokud chcete mít protějšek Britta Simon v Adobe Experience Manager, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s [název aplikace], proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Adobe Experience Manager** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Adobe Experience Manager domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte jedinečnou hodnotu, která jsou definovány na serveru nástroje AEM.

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není skutečný. Aktualizujte hodnotu adresy URL odpovědi s adresou URL skutečné odpovědi. Chcete-li tuto hodnotu získat, obraťte se [tým podpory Adobe Experience Manager klienta](https://helpx.adobe.com/support/experience-manager.html) tuto výhodu získáte. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Adobe Experience Manager domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL serveru Adobe Experience Manager.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavit až Adobe Experience Manager** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Konfigurace přihlašování jedním Adobe Experience Manager

1. V jiném okně prohlížeče, otevřete **Adobe Experience Manager** portál pro správu.

2. Vyberte **nastavení** > **zabezpečení** > **uživatelé**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Vyberte **správce** nebo jiné příslušné uživatele.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Vyberte **nastavení účtu** > **spravovat TrustStore**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. V části **přidat certifikát ze souboru CER**, klikněte na tlačítko **vyberte soubor certifikátu**. Vyhledejte a vyberte soubor certifikátu, který jste už stáhli z webu Azure portal.

    ![Konfigurace jednotného přihlašování tlačítko Uložit](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Certifikát je přidán do TrustStore. Poznámka: alias certifikátu.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Na **uživatelé** stránce **ověřovací službu**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Vyberte **nastavení účtu** > **vytvoření/Správa KeyStore**. Vytvořte úložiště klíčů zadáním hesla.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Přejděte zpět na obrazovku pro správu. Potom vyberte **nastavení** > **operace** > **Webová konzola**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Otevře se stránka konfigurace.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Najít **obslužná rutina Adobe Granite SAML 2.0 ověřování**. Vyberte **přidat** ikonu.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Pomocí následujících kroků na této stránce.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. V **cesta** zadejte **/**.

    b. V **adresa URL zprostředkovatele identity** zadejte **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    c. V **Alias certifikátu zprostředkovatele identity** zadejte **Alias certifikátu** hodnotu, která jste přidali v TrustStore.

    d. V **zabezpečení zadané ID Entity** zadejte jedinečné **Azure Ad identifikátor** hodnotu, která jste nakonfigurovali na webu Azure Portal.

    e. V **adresa URL služby Assertion příjemce** zadejte **adresy URL odpovědi** hodnotu, která jste nakonfigurovali na webu Azure Portal.

    f. V **heslo klíče Store** zadejte **heslo** , kterou jste nastavili v úložišti klíčů.

    g. V **atribut ID uživatele** zadejte **ID názvu** nebo jiné ID uživatele, který je na váš případ nevztahuje.

    h. Vyberte **uživatele automaticky vytvořit CRX**.

    i. V **odhlašovací adresa URL** zadejte jedinečné **odhlašovací adresa URL** hodnoty, které jste získali z portálu Azure portal.

    j. Vyberte **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k aplikaci Adobe Experience Manager.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Adobe Experience Manager**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Adobe Experience Manager**.

    ![Adobe Experience Manager odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-adobe-experience-manager-test-user"></a>Vytvořit testovacího uživatele Adobe Experience Manager

V této části vytvoříte uživateli Britta Simon v aplikaci Adobe Experience Manager. Pokud jste vybrali **uživatele automaticky vytvořit CRX** možnost, uživatelé se vytvoří automaticky po úspěšném ověření.

Pokud chcete ručně vytvořit uživatele, pracovat [tým podpory Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) přidat uživatele na platformě Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Adobe Experience Manager na přístupovém panelu, můžete by měl být automaticky přihlášeni k Adobe Experience Manager, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
