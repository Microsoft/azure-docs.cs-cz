---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML pro Jira podle rozlišení GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 943131bc746b5d2a1fd95a26a6a6c9f3bb6b9e57
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509960"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH

V tomto kurzu se dozvíte, jak nastavit jednotné přihlašování SAML pro Jira podle rozlišení GmbH se službou Azure Active Directory (Azure AD).
Integrace jednotného přihlašování SAML pro Jira podle rozlišení GmbH s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který můžete přihlásit do nástroje Jira pomocí modulu plug-in jednotného přihlašování SAML podle rozlišení GmbH.
* Můžete povolit uživatelům, aby se automaticky přihlášeni do nástroje Jira pomocí jejich účtů služby Azure AD pomocí SAML SSO pro Jira pomocí řešení GmbH (Single Sign-On).
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD a SAML SSO pro Jira s rozlišení GmbH, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [zde](https://azure.microsoft.com/pricing/free-trial/)
* Jednotné přihlašování SAML pro Jira podle rozlišení GmbH jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Jednotné přihlašování SAML pro Jira tak řešení podporuje GmbH **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Přidání podniková aplikace pro jednotné přihlašování

Pokud chcete nastavit jednotné přihlašování ve službě Azure AD, budete muset přidat nové podnikovou aplikaci. V galerii, je předem nakonfigurovanou aplikaci předvolby v takovém případě **jednotné přihlašování SAML pro Jira podle rozlišení GmbH**.

**Pokud chcete přidat jednotné přihlašování SAML pro Jira z Galerie řešení GmbH, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace**a potom klikněte na tlačítko **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **jednotné přihlašování SAML pro Jira podle rozlišení GmbH**vyberte **jednotné přihlašování SAML pro Jira podle rozlišení GmbH** z panelu výsledek a pak klikněte na tlačítko **přidat** tlačítko pro přidání aplikace. Můžete také změnit název podnikové aplikace.

     ![Jednotné přihlašování SAML pro Jira podle rozlišení GmbH v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Nakonfigurovat a otestovat jednotné přihlašování pomocí SAML SSO modulu plug-in a Azure AD

V této části otestujete a nakonfigurovat jednotné přihlašování ve službě Jira pro uživatele služby Azure AD. To se provede pro testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v jednotné přihlašování SAML pro Jira podle rozlišení GmbH musí být vytvořeno.

Nakonfigurovat a otestovat jednotné přihlašování, budete muset provést následující kroky:

1. **[Podniková aplikace Azure AD pro jednotné přihlašování konfigurovat](#configure-the-azure-ad-enterprise-application-for-single-sign-on)**  -nakonfigurovat podniková aplikace Azure AD pro jednotné přihlašování
2. **[Konfigurace modulu plug-in SAML SSO instance Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)**  – konfigurace nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – vytvoření zkušebního uživatele ve službě Azure AD.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – povolení testovacího uživatele použití jedné přihlašování na straně Azure.
1. **[Vytvořit testovacího uživatele ve službě Jira](#create-the-test-user-also-in-jira)**  – vytvoření zkušebního uživatele protějšek ve službě Jira pro uživatele Azure AD.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – ověřte, jestli funguje v konfiguraci.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurovat podnikové aplikace Azure AD pro jednotné přihlašování

V této části můžete nastavit jednotné přihlašování na portálu Azure portal.

Ke konfiguraci jednotné přihlašování s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/), v právě vytvořenou **jednotné přihlašování SAML pro Jira podle rozlišení GmbH** podnikové aplikace, vyberte **jednotného přihlašování** na levém panelu.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Pro **vybrat jedinou metodu přihlašování**, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Později, klikněte na tlačítko **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. V **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace **zprostředkovatele identity** iniciované režimu, a poté proveďte následující kroky:

    ![Jednotné přihlašování SAML pro Jira podle rozlišení GmbH domény a adresy URL jednotné přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Jednotné přihlašování SAML pro Jira podle rozlišení GmbH domény a adresy URL jednotné přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Pro identifikátor, adresa URL odpovědi a přihlašovací adresa URL, nahraďte  **\<adresa url serveru base >** s základní adresu URL instance Jira. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal. Pokud máte potíže, kontaktujte nás na adrese [tým podpory jednotného přihlašování SAML pro Jira podle rozlišení GmbH klienta](https://www.resolution.de/go/support).

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** oddílu, stáhněte si **kód XML metadat federace** a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Konfigurace modulu plug-in SAML SSO instance Jira 

1. V okně jiné webové prohlížeče Přihlaste se k vaší instanci Jira jako správce.

2. Najeďte myší ikonu ozubeného kola na pravé straně a klikněte na tlačítko **Správa aplikací**.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon1.png)

3. Pokud budete přesměrováni na stránku přístup správce, zadejte **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon2.png)

4. Jira obvykle vás přesměruje na webu marketplace od společnosti Atlassian. Pokud ne, klikněte na **najít nové aplikace** na levém panelu. Vyhledejte **SAML jednotné přihlašování (SSO) pro JIRA** a klikněte na tlačítko **nainstalovat** nainstalujte zásuvný modul SAML.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store.png)

5. Spustí se instalace modulu plug-in. Po dokončení klikněte na tlačítko **Zavřít** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store-2.png)

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store-3.png)

6. Potom klikněte na **spravovat**.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store-4.png)
    
8. Později, klikněte na tlačítko **konfigurovat** konfigurace právě nainstalovaných modulů plug-in.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store-5.png)

9. V **konfigurace modulu plug-in SingleSignOn SAML** průvodce, klikněte na tlačítko **přidat nového zprostředkovatele identity** ke konfiguraci Azure AD jako nového zprostředkovatele Identity.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon4.png) 

10. Na **zvolte zprostředkovatele Identity SAML** stránce, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5a.png)
 
    a. Nastavte **Azure AD** jako typ zprostředkovatele identity.
    
    b. Přidat **název** zprostředkovatele Identity (např. Azure AD).
    
    c. (Volitelně) přidejte **popis** zprostředkovatele Identity (např. Azure AD).
    
    d. Klikněte na **Další**.
    
11. Na **konfigurace zprostředkovatele Identity** klikněte na **Další**.
 
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5b.png)

12. Na **Import metadat zprostředkovatele identity SAML** stránce, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5c.png)

    a. Klikněte na tlačítko **vyberte soubor XML metadat** tlačítko a vyberte si **kód XML metadat federace** souborů, které jste stáhli dříve.

    b. Klikněte na tlačítko **Import** tlačítko.
     
    c. Počkejte krátce import úspěšný.  
     
    d. Klikněte na tlačítko **Další** tlačítko.
    
13. Na **atribut ID uživatele a transformace** stránky, klikněte na tlačítko **Další** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5d.png)
    
14. Na **vytvoření uživatele a aktualizace** klikněte na **u & ložit Další** uložte nastavení.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6a.png)
    
15. Na **Test nastavení** klikněte na **přeskočit testovací & ručně nakonfigurovat** testovací uživatel prozatím přeskočit. To se provede v další části a vyžaduje některá nastavení na portálu Azure portal.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6b.png)
    
16. Klikněte na tlačítko **OK** přeskočit upozornění.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon. S uživatelem můžete otestovat jednotné přihlašování.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Zvolte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V **vlastnosti uživatele**, proveďte následující kroky:

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **Britta Simon**.
  
    b. V **uživatelské jméno** zadejte <b> BrittaSimon@contoso.com </b>.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části přidáte do podniková aplikace, které jí použití jednotného přihlašování umožní Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**. 

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyhledejte podniková aplikace, kterou jste vytvořili na začátku tohoto kurzu. Pokud postupujete kroků tohoto kurzu, se nazývá **jednotné přihlašování SAML pro Jira podle rozlišení GmbH**. Pokud jste ho zadaný jiný název, vyhledejte tento název.

    ![Jednotné přihlašování SAML pro Jira podle propojení GmbH řešení v seznamu aplikací](common/all-applications.png)

3. Na levém panelu klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML, pak v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a potom klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky .

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-the-test-user-also-in-jira"></a>Také vytvořit testovacího uživatele ve službě Jira

Umožňuje uživatelům Azure AD k přihlášení na jednotné přihlašování SAML pro Jira rozlišení GmbH, musí být zřízená do jednotné přihlašování SAML pro Jira podle rozlišení GmbH. Pro případ v tomto kurzu budete muset provést ruční zřizování. Existují však také jiné zřizování modely k dispozici pro modul plug-in jednotného přihlašování SAML podle rozlišení, třeba **JIT** zřizování. Přečtěte si jejich dokumentaci na [jednotného přihlašování SAML podle rozlišení GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Pokud máte dotaz týkající se ho, kontaktujte podporu na [podporu rozlišení](https://www.resolution.de/go/support).

**Ručně zřídit účet uživatele, proveďte následující kroky:**

1. Přihlaste se k instanci systému Jira jako správce.

2. Podržte ukazatel myši nad ozubeného kola a vyberte **Správa uživatelů**.

   ![Přidat zaměstnance](./media/samlssojira-tutorial/user1.png)

3. Pokud budete přesměrováni na stránku pro přístup správce, zadejte **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user2.png) 

4. V části **Správa uživatelů** kartě oddíl, klikněte na tlačítko **vytvořit uživatele**.

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user3-new.png) 

5. Na **"Vytvořit nový uživatel"** dialogového okna stránky, proveďte následující kroky. Je nutné vytvořit uživatele přesně ve službě Azure AD, jako jsou:

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user4-new.png) 

    a. V **e-mailová adresa** textového pole zadejte e-mailová adresa uživatele: <b> BrittaSimon@contoso.com </b>.

    b. V **jméno a příjmení** textové pole, typ celé jméno uživatele: **Britta Simon**.

    c. V **uživatelské jméno** textového pole zadejte e-mailová adresa uživatele: <b> BrittaSimon@contoso.com </b>. 

    d. V **heslo** textového pole zadejte heslo uživatele.

    e. Klikněte na tlačítko **vytvořit uživatele** na dokončení vytvoření uživatele.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na jednotné přihlašování SAML pro Jira podle rozlišení GmbH dlaždici na přístupovém panelu, by měl být automaticky přihlásíte na jednotné přihlašování SAML pro Jira podle rozlišení GmbH, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Můžete také otestovat jednotné přihlašování, při přechodu k [https://\<adresa url serveru base >/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Náhradní  **\<adresa url serveru base >** s základní adresu URL instance Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Povolit přesměrování jednotné přihlašování pro Jira

Jak je uvedeno v části před nejsou aktuálně dva způsoby, jak aktivovat jednotné přihlašování. S použitím **webu Azure portal** nebo pomocí **speciální odkaz k vaší instanci Jira**. Modul plug-in jednotného přihlašování SAML podle rozlišení GmbH také umožňuje aktivovat jednotné přihlašování pomocí jednoduše **přístup k libovolnou adresu URL odkazující na instanci systému Jira**.

V podstatě všem uživatelům přístup k systému Jira budou přesměrováni na jednotné přihlašování až po dokončení aktivace možnost v modulu plug-in.

K aktivaci přesměrování jednotného přihlašování, provést následující akce v **vaší instance Jira**:

1. Přístup ke stránce Konfigurace modulu plug-in SAML SSO ve službě Jira.
1. Klikněte na **přesměrování** na levém panelu.
![](./media/samlssojira-tutorial/ssore1.png)

1. Značky **povolení jednotného přihlašování k přesměrování**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Stisknutím klávesy **uložit nastavení** tlačítko v pravém horním rohu.

Po aktivaci možnost, můžete oslovit příkazový řádek if uživatelského jména a hesla **povolit nosso** tak, že přejdete do je zaškrtnuta možnost [https://\<adresa url serveru base > /login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Jako vždy nahradit  **\<adresa url serveru base >** s základní adresu URL.


## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

