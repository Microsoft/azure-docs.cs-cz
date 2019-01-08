---
title: 'Kurz: Integrace Azure Active Directory pomocí konzoly pro správu Mimecast | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mimecast konzoly pro správu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 7d1280525560c5333a5764ac9f962f79bd2284ad
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54061619"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Kurz: Integrace Azure Active Directory pomocí konzoly pro správu Mimecast

V tomto kurzu se dozvíte, jak integrovat Mimecast konzoly pro správu Azure Active Directory (Azure AD).
Integrace konzoly pro správu Mimecast s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke konzole pro správu Mimecast.
* Uživatelům se automaticky přihlášeni k Mimecast konzoly pro správu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí konzoly pro správu Mimecast, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Konzola správce Mimecast jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Konzola správce Mimecast podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Přidání Mimecast konzoly pro správu z Galerie

Konfigurace integrace Mimecast konzoly pro správu do služby Azure AD, budete muset přidat Mimecast konzoly pro správu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Mimecast konzoly pro správu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **konzoly pro správu Mimecast**vyberte **konzoly pro správu Mimecast** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Mimecast konzoly pro správu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování pomocí konzoly pro správu Mimecast podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Mimecast konzoly pro správu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Mimecast konzoly pro správu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Mimecast správce konzoly Single Sign-On](#configure-mimecast-admin-console-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele konzoly pro správu Mimecast](#create-mimecast-admin-console-test-user)**  – Pokud chcete mít protějšek Britta Simon Mimecast konzoly pro správu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí konzoly pro správu Mimecast, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **konzoly pro správu Mimecast** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Mimecast správce konzoly domény a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > Přihlášení na adrese URL se liší podle oblastí.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení konzoly pro správu Mimecast** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-mimecast-admin-console-single-sign-on"></a>Konfigurace správce Mimecast konzoly jednotného přihlašování

1. V okně jiný webový prohlížeč Přihlaste se ke konzole správce Mimecast jako správce.

2. Přejděte na **služby \> aplikace**.

    ![Služby](./media/mimecast-admin-console-tutorial/ic794998.png "služby")

3. Klikněte na tlačítko **ověřování profilů**.

    ![Ověřování profilů](./media/mimecast-admin-console-tutorial/ic794999.png "ověřování profilů")
    
4. Klikněte na tlačítko **nový profil ověřování**.

    ![Nové profily ověřování](./media/mimecast-admin-console-tutorial/ic795000.png "nové profily ověřování")

5. V **ověřování profilu** části, proveďte následující kroky:

    ![Profil ověření](./media/mimecast-admin-console-tutorial/ic795015.png "profilu ověření systému")
    
    a. V **popis** textového pole zadejte název pro vaši konfiguraci.
    
    b. Vyberte **vynutit ověřování SAML pro konzolu Správce Mimecast**.
    
    c. Jako **poskytovatele**vyberte **Azure Active Directory**.
    
    d. Vložit **Azure Ad identifikátor**, který jste zkopírovali z portálu Azure portal do **URL vystavitele** textového pole.
    
    e. Vložit **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal do **přihlašovací adresa URL** textového pole.

    f. Vložit **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal do **odhlašovací adresa URL** textového pole.
    
    >[!NOTE]
    >Hodnota adresy URL pro přihlášení a hodnota odhlašovací adresa URL jsou pro konzolu Správce Mimecast stejné.
    
    g. Otevřít certifikát base-64 stáhnout z webu Azure portal v programu Poznámkový blok, odeberte první řádek ("*--*") a poslední řádek ("*--*"), zkopírujte ho do zbývající obsah vaší schránky a vložte ho do **certifikát zprostředkovatele Identity (Metadata)** textového pole.
    
    h. Vyberte **povolit jednotné přihlašování na**.
    
    i. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke konzole správce Mimecast.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **konzoly pro správu Mimecast**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **konzoly pro správu Mimecast**.

    ![Na konzole pro správu Mimecast odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-mimecast-admin-console-test-user"></a>Vytvořit testovacího uživatele konzoly pro správu Mimecast

Chcete-li povolit uživatele Azure AD pro přihlášení ke konzole pro správu Mimecast, musí být poskytnuty do konzoly pro správu Mimecast. V případě Mimecast konzoly pro správu zřizování se ruční úlohy.

* Budete muset zaregistrovat k doméně, než budete moct vytvořit uživatele.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **konzoly pro správu Mimecast** jako správce.

2. Přejděte na **adresáře \> interní**.
   
    ![Adresáře](./media/mimecast-admin-console-tutorial/ic795003.png "adresáře")

3. Klikněte na tlačítko **zaregistrovat novou doménu**.
   
    ![Zaregistrujte novou doménu](./media/mimecast-admin-console-tutorial/ic795004.png "zaregistrovat nové domény")

4. Po vytvoření nové domény, klikněte na tlačítko **novou adresu**.
   
    ![Nová adresa](./media/mimecast-admin-console-tutorial/ic795005.png "novou adresu")

5. V dialogovém okně Nový adresy proveďte následující kroky:
   
    ![Uložit](./media/mimecast-admin-console-tutorial/ic795006.png "uložit")
   
    a. Typ **e-mailovou adresu**, **globální název**, **heslo**, a **potvrzení hesla** účtu atributů platné Azure AD, který jste chcete zřízení související textových polí.

    b. Klikněte na **Uložit**.

>[!NOTE]
>Další nástroje pro tvorbu účtu uživatele konzoly pro správu Mimecast nebo rozhraní API poskytovaných Mimecast konzoly pro správu můžete použít ke zřízení uživatelských účtů služby Azure AD. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Mimecast konzoly pro správu na přístupovém panelu, vám by měl být automaticky přihlášeni do konzoly pro správu Mimecast, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

