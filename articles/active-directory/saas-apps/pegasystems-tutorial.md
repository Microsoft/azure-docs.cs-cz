---
title: 'Kurz: Integrace služby Azure Active Directory se systémy Pega | Dokumenty společnosti Microsoft'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Pega Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026804"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Kurz: Integrace služby Azure Active Directory se systémy Pega

V tomto kurzu se dozvíte, jak integrovat systémy Pega s Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Azure AD můžete použít k řízení, kdo má přístup k Pega systems.
* Uživatelům můžete povolit automatické přihlášení k pega systems (jednotné přihlašování) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Další informace o integraci aplikací SaaS s Azure AD najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD se systémy Pega, musíte mít:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete se zaregistrovat na [měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné Pega Systems, které má povoleno jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí.

* Pega Systems podporuje sp-inicioval a IdP-inicioval sso.

## <a name="add-pega-systems-from-the-gallery"></a>Přidat Pega Systems z galerie

Pokud chcete nastavit integraci Pega Systems do Azure AD, musíte přidat Pega Systems z galerie do seznamu spravovaných aplikací SaaS.

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Službu Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace:**

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Pega Systems**. Ve výsledcích hledání vyberte **Pega Systems** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí pega systems pomocí testovacího uživatele s názvem Britta Simon.
Chcete-li povolit jednotné přihlašování, musíte vytvořit vztah mezi uživatelem Služby Azure AD a odpovídajícím uživatelem v Pega Systems.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí pega systems, musíte provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby byla tato funkce povolena pro vaše uživatele.
2. **[Nakonfigurujte jednotné přihlašování pega systems](#configure-pega-systems-single-sign-on)** na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlášení Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD,](#assign-the-azure-ad-test-user)** aby uživatel povolil jednotné přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele Pega Systems,](#create-a-pega-systems-test-user)** který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Pega Systems, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Pega Systems** vyberte **Jednotné přihlašování**:

    ![Vybrat jednotné přihlašování](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování:

    ![Výběr metody jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte ikonu **Upravit** a otevřete dialogové okno Základní **konfigurace SAML:**

    ![Ikona úprav](common/edit-urls.png)

4. Chcete-li aplikaci konfigurovat v režimu iniciovaném protokolem IdP, proveďte v dialogovém **okně Základní konfigurace SAML** následující kroky.

    ![Dialogové okno Základní konfigurace SAML](common/idp-intiated.png)

    1. Do pole **Identifikátor** zadejte adresu URL v tomto vzoru:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Do pole **Adresa URL pro odpověď** zadejte adresu URL v tomto vzoru:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Pokud chcete aplikaci nakonfigurovat v režimu iniciovaném pomocí aktualizace SP, vyberte **Nastavit další adresy URL** a proveďte následující kroky.

    ![Informace o jednotném přihlášení do domény pega systems a adresy URL](common/both-advanced-urls.png)

    1. Do pole **Přihlásit se na adresu URL** zadejte hodnotu adresy URL přihlášení.

    1. Do pole **Stav přenosu** zadejte adresu URL v tomto vzoru:`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Zde uvedené hodnoty jsou zástupné symboly. Musíte použít skutečný identifikátor, adresu URL odpovědi, přihlaste se k adrese URL a adresu URL stavu přenosu. Hodnoty adresy URL identifikátorů a odpovědí můžete získat z aplikace Pega, jak je vysvětleno dále v tomto kurzu. Chcete-li získat hodnotu stavu relé, obraťte se na [tým podpory pega systems](https://www.pega.com/contact-us). Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

6. Aplikace Pega Systems potřebuje, aby kontrolní výrazy SAML byly v určitém formátu. Chcete-li je získat ve správném formátu, je třeba přidat vlastní mapování atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky zobrazuje výchozí atributy. Výběrem ikony **Upravit** otevřete dialogové okno **Atributy uživatele:**

    ![Uživatelské atributy](common/edit-attribute.png)

7. Kromě atributů uvedených na předchozím snímku obrazovky vyžaduje aplikace Pega Systems několik dalších atributů, které mají být předány zpět v odpovědi SAML. V části **Uživatelské deklarace identity** v dialogovém okně **Atributy uživatele** přidejte tyto atributy tokenu SAML následujícím postupem:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Tyto hodnoty jsou specifické pro vaši organizaci. Zadejte příslušné hodnoty.

    1. Výběrem **možnosti Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů:**

    ![Vybrat přidat novou deklaraci.](common/new-save-attribute.png)

    ![Dialogové okno Spravovat deklarace identity uživatelů](common/new-attribute-details.png)

    1. Do pole **Název** zadejte název atributu zobrazený pro tento řádek.

    1. Pole **Obor názvů** ponechejte prázdné.

    1. Ve **zdroji**vyberte **atribut**.

    1. V seznamu **atributů Zdroj** vyberte hodnotu atributu zobrazenou pro daný řádek.

    1. Vyberte **OK**.

    1. Vyberte **Uložit**.

8. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** odkaz **Ke stažení** vedle xml metadat **federace**a certifikát uložte do počítače:

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. V části **Nastavit systémy Pega** zkopírujte příslušné adresy URL na základě vašich požadavků.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    1. **Adresa URL přihlášení**.

    1. **Identifikátor služby Azure .**

    1. **Adresa URL pro odhlášení**.

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurace jednotného přihlašování systémů Pega Systems

1. Chcete-li nakonfigurovat jednotné přihlašování na straně **Pega Systems,** přihlaste se k portálu Pega pomocí účtu správce v jiném okně prohlížeče.

2. Vyberte **Vytvořit** > **ověřovací službu****SysAdmin** > :

    ![Vybrat ověřovací službu](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Na obrazovce Vytvořit **ověřovací službu** proveďte následující kroky.

    ![Obrazovka Vytvořit ověřovací službu](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. V seznamu **Typ** vyberte **možnost SAML 2.0**.

    1. Do pole **Název** zadejte libovolný název (například **Azure AD SSO).**

    1. Do pole **Krátký popis** zadejte popis.  

    1. Vyberte **Vytvořit a otevřete**.
    
4. V části **informace o zprostředkovateli identity (IdP)** vyberte **Importovat metadata IdP** a přejděte k souboru metadat, který jste stáhli z webu Azure Portal. Chcete-li načíst metadata, klepněte na **tlačítko Odeslat:**

    ![Informační sekce zprostředkovatele identity (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Import naplní data IdP, jak je znázorněno zde:

    ![Importovaná data IdP](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. V části Nastavení **poskytovatele služeb (SP)** proveďte následující kroky.

    ![Nastavení poskytovatele služeb](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Zkopírujte hodnotu **Identifikace entity** a vložte ji do pole **Identifikátor** v části Základní **konfigurace SAML** na webu Azure Portal.

    1. Zkopírujte hodnotu **umístění služby Assertion Consumer Service (ACS)** a vložte ji do pole **Adresa URL odpovědi** v části Základní konfigurace **SAML** na webu Azure Portal.

    1. Vyberte **zakázat podepisování požadavků**.

7. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal vyberte v levém podokně **Službu Azure Active Directory,** vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel:**

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **Uživatel** proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. Do pole **Uživatelské jméno** zadejte **brittasimon@\<\<> vaší firemní domény. prodloužení>**. (Například BrittaSimon@contoso.com.)

    c. Vyberte **Zobrazit heslo**a poznamenejte si hodnotu, která je v poli **Heslo.**

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části umožníte Brittě Simonové používat jednotné přihlašování Azure tím, že jí udělíte přístup k pega systems.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Pega Systems**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **pega systems**.

    ![Seznam žádostí](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé **Brittu Simonovou** a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. Klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-pega-systems-test-user"></a>Vytvoření testovacího uživatele Pega Systems

Dále musíte vytvořit uživatele s názvem Britta Simon v Pega Systems. Spolupracujte s [týmem podpory Pega Systems](https://www.pega.com/contact-us) a vytvářejte uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když vyberete dlaždici Pega Systems na přístupovém panelu, měli byste být automaticky přihlášeni k instanci Pega Systems, pro kterou nastavíte služby při připojení k internetu. Další informace najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)