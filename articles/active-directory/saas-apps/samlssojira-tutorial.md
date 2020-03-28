---
title: 'Kurz: Integrace služby Azure Active Directory se službou JEDNOTNÉHO PŘIBÍHA pro Jiru společností Resolution GmbH | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašování SAML pro Jiru podle řešení GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957fee48397bc0b23737157dec0e74cf6505fab5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160128"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Kurz: Integrace služby Azure Active Directory se službou JEDNOTNÉHO PŘIBLIŽOvat elu pro Jiru podle rozlišení GmbH

V tomto kurzu se dozvíte, jak nastavit jednotné přibrádá saml pro Jira podle rozlišení GmbH s Azure Active Directory (Azure AD).
Integrace jednotného přihrádání SAML pro Jira pomocí řešení GmbH s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, kteří se můžou přihlásit k Jiře pomocí pluginu jednotného přihlašování SAML podle řešení GmbH.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k Jiře pomocí svých účtů Azure AD pomocí jednotného přihlašování SAML pro Jira podle řešení GmbH (Jednotné přihlášení).
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD a jednotné připínděk SAML pro Jira podle rozlišení GmbH, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO pro Jira podle rozlišení GmbH jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAML SSO pro Jiru usnesením GmbH podporuje **SP** a **IDP** inicioval SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Přidání podnikové aplikace pro jednotné přihlašování

Chcete-li nastavit jednotné přihlašování ve službě Azure AD, musíte přidat novou podnikovou aplikaci. V galerii je přednastavená aplikace, **SAML SSO pro Jiru podle rozlišení GmbH**.

**Chcete-li přidat SAML SSO pro Jira podle rozlišení GmbH z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **položku Podnikové aplikace**a klepněte na tlačítko **Všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAML SSO pro Jiru podle rozlišení GmbH**, zvolte **saml sso pro Jiru podle rozlišení GmbH** z panelu výsledků a pak kliknutím na tlačítko **Přidat** přidejte aplikaci. Můžete také změnit název podnikové aplikace.

     ![SAML SSO pro Jiru usnesením GmbH v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Konfigurace a testování jednotného přihlášení pomocí modulu plug-in jednotného přihlašování SAML a služby Azure AD

V této části otestujete a nakonfigurujete jednotné přihlašování k Jiře pro uživatele Azure AD. To bude provedeno pro testovací uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Služby Azure AD a souvisejícím uživatelem v jednotném přihlašování SAML pro Jira podle rozlišení GmbH.

Chcete-li konfigurovat a testovat jednotné přihlašování, je třeba provést následující kroky:

1. **[Konfigurace podnikové aplikace Azure AD pro jednotné přihlašování](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** – konfigurace podnikové aplikace Azure AD pro jednotné přihlašování
2. **[Konfigurace pluginu jednotného přihlašování SAML instance Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)** – nakonfigurujte nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – vytvořte testovacího uživatele ve službě Azure AD.
1. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – povolení testovacímu uživateli používat na straně Azure jednotné přihlašování.
1. **[Vytvořte testovacího uživatele v Jira](#create-the-test-user-also-in-jira)** – vytvořte protějšek testovací ho uživatele v Jira pro uživatele testu Azure AD.
1. **[Testování jednotného přihlášení](#test-single-sign-on)** – ověřte, zda konfigurace funguje.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurace podnikové aplikace Azure AD pro jednotné přihlašování

V této části nastavíte jednotné přihlašování na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování pomocí jednotného přihlašování SAML pro Jiru pomocí řešení GmbH, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)v právě vytvořeném **jednotném přihlašování SAML pro Jiru podle řešení podnikové** aplikace GmbH vyberte v levém panelu jednotné **přihlašování.**

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **části Select a Single sign-on method**vyberte režim **SAML,** který povolí jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Poté kliknutím na ikonu **Upravit** otevřete dialogové okno **Základní konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Chcete-li nakonfigurovat aplikaci v režimu iniciovaného **protokolu IDP,** proveďte v části **Základní konfigurace SAML** následující kroky:

    ![SAML Jednotné přihlašování pro Jiru podle rozlišení GmbH Doména a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![SAML Jednotné přihlašování pro Jiru podle rozlišení GmbH Doména a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Pro identifikátor, adresu URL odpovědi a přihlašovací adresu URL, náhradní ** \<server-base-url>** se základní adresou URL instance Jira. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal. Pokud máte problém, kontaktujte nás na [SAML SSO pro Jira usnesením GmbH Client support team](https://www.resolution.de/go/support).

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** v části **Podpisový certifikát SAML** stáhněte **xml metadat federace** a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Konfigurace pluginu saml sso vaší instance Jira 

1. V jiném okně webového prohlížeče se přihlaste k instanci Jira jako správce.

2. Najeďte přes kolečko na pravé straně a klikněte na **Spravovat aplikace**.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon1.png)

3. Pokud jste přesměrováni na stránku Přístup správce, zadejte **heslo** a klepněte na tlačítko **Potvrdit.**

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon2.png)

4. Jira vás obvykle přesměruje na trh Atlassian. Pokud ne, klikněte na **Najít nové aplikace** v levém panelu. Vyhledejte **saml jednotné přihlášení na (SSO) pro JIRA** a klepněte na tlačítko **Nainstalovat** nainstalovat plugin SAML.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store.png)

5. Spustí se instalace pluginu. Po dokončení klikněte na tlačítko **Zavřít.**

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store-2.png)

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store-3.png)

6. Potom klepněte na tlačítko **Spravovat**.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store-4.png)
    
8. Poté kliknutím na **konfigurovat** nakonfigurovat právě nainstalovaný plugin.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/store-5.png)

9. V průvodci **konfigurací modulu plug-in SAML SingleSignOn** klikněte na **Přidat nový zprostředkovatel idp** a nakonfigurujte Azure AD jako nového zprostředkovatele identity.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon4.png) 

10. Na stránce **Vyberte si poskytovatele identity SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5a.png)
 
    a. Nastavte **Azure AD** jako typ IdP.
    
    b. Přidejte **název** zprostředkovatele identity (například Azure AD).
    
    c. Přidejte (volitelný) **popis** zprostředkovatele identity (např. Azure AD).
    
    d. Klikněte na **Další**.
    
11. Na **stránce konfigurace zprostředkovatele identity** klepněte na tlačítko **Další**.
 
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5b.png)

12. Na stránce **Metadata idp importu SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5c.png)

    a. Klepněte na tlačítko **Vybrat soubor XML metadat** a vyberte soubor XML metadat **federace,** který jste stáhli dříve.

    b. Klepněte na tlačítko **Importovat.**
     
    c. Krátce počkejte, dokud import proběhne úspěšně.  
     
    d. Klikněte na tlačítko **Další.**
    
13. Na **stránce atributu ID uživatele a transformační** stránky klikněte na tlačítko **Další.**

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5d.png)
    
14. Na stránce **Vytvoření a aktualizace uživatele** uložte nastavení kliknutím na Uložit & **další.**
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6a.png)
    
15. Na stránce **Otestovat nastavení** klikněte na **Přeskočit test & ručně nakonfigurovat,** abyste prozatím přeskočili uživatelský test. To se provede v další části a vyžaduje některá nastavení na webu Azure Portal.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6b.png)
    
16. Chcete-li upozornění přeskočit, klepněte na tlačítko **OK.**
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon. S uživatelem otestujete jednotné přihlašování.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky zvolte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve **vlastnostech User**proveďte následující kroky:

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **Brittu Simonovou**.
  
    b. Do pole **Uživatelské** jméno <b>BrittaSimon@contoso.com</b>zadejte .

    c. Zaškrtněte políčko **Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části přidáte Brittu Simon do podnikové aplikace, která jí umožňuje používat jednotné přihlašování.

1. Na portálu Azure vyberte **Podnikové aplikace**a pak vyberte **Všechny aplikace**. 

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyhledejte podnikovou aplikaci, kterou jste vytvořili na začátku tohoto kurzu. Pokud jste podle kroků kurzu, je to jen **SAML SSO pro Jira usnesením GmbH**. Pokud jste mu dali jiný název, vyhledejte toto jméno.

    ![Odkaz SAML SSO pro Jiru podle rozlišení GmbH v seznamu Aplikace](common/all-applications.png)

3. V levém panelu klepněte na **položku Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. V **dialogovém okně Přidat** **přiřazení** vyberte Přidat uživatele a pak vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte ze seznamu Uživatelé **Brittu Simonovou** a v dolní části obrazovky klikněte na tlačítko **Vybrat.**

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte příslušnou roli pro uživatele ze seznamu a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-the-test-user-also-in-jira"></a>Vytvoření testovacího uživatele také v Jiře

Aby se uživatelé Azure AD mohli přihlásit k jednotnému přihlašování SAML pro Jira podle řešení GmbH, musí být zřízeni do jednotného přihlašování SAML pro Jira podle řešení GmbH. V případě tohoto kurzu je třeba provést zřizování ručně. Existují však také další zřizovací modely, které jsou k dispozici pro modul plug-in jednotného připojetí SAML podle rozlišení, například zřizování **just in time.** Informace o jejich dokumentaci naleznete na adrese [SAML SSO v usnesení GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Máte-li dotaz týkající se něj, obraťte se na podporu [řešení krize](https://www.resolution.de/go/support).

**Chcete-li ručně zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k instanci Jira jako správce.

2. Najeďte přes kolečko a vyberte **správu uživatelů**.

   ![Přidat zaměstnance](./media/samlssojira-tutorial/user1.png)

3. Pokud jste přesměrováni na stránku Přístup správce, zadejte **heslo** a klepněte na tlačítko **Potvrdit.**

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user2.png) 

4. V části **Karta Správa uživatelů** klikněte na vytvořit **uživatele**.

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user3-new.png) 

5. Na stránce dialogového okna **Vytvořit nového uživatele** proveďte následující kroky. Musíte vytvořit uživatele přesně jako ve službě Azure AD:

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user4-new.png) 

    a. Do textového pole **E-mailová adresa** zadejte e-mailovou adresu uživatele: <b>BrittaSimon@contoso.com</b>.

    b. Do textového pole **Celé jméno** zadejte celé jméno uživatele: **Britta Simon**.

    c. Do textového pole **Uživatelské jméno** zadejte e-mailovou adresu uživatele: <b>BrittaSimon@contoso.com</b>. 

    d. Do textového pole **Heslo** zadejte heslo uživatele.

    e. Chcete-li dokončit vytvoření uživatele, klepněte na **tlačítko Vytvořit uživatele.**

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici SAML SSO pro Jira podle rozlišení GmbH na přístupovém panelu, měli byste být automaticky přihlášeni k jednotnému přijmutí SAML pro Jira pomocí řešení GmbH, pro které nastavíte jednotné přibíjení. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Můžete také otestovat jednotné přihlášení, pokud přejdete na [https://\<server-base-url>/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Nahraďte ** \<server-base-url>** se základní adresou URL instance Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Povolit jednotné přesměrování pro Jiru

Jak je uvedeno v části dříve, v současné době existují dva způsoby, jak spustit jednotné přihlašování. Buď pomocí **portálu Azure nebo** pomocí **speciálního odkazu na instanci Jira**. Saml SSO plugin od rozlišení GmbH také umožňuje aktivovat jednotné přihlášení pouhým **přístupem k libovolné adrese URL ukazující na vaši instanci Jira**.

V podstatě budou všichni uživatelé, kteří přistupují k Jiře, přesměrováni na jednotné přihlášení po aktivaci možnosti v pluginu.

Chcete-li aktivovat přesměrování s přidělutím služby SSO, postupujte v **instanci Jira**takto :

1. Přístup ke konfigurační stránce pluginu SAML SSO v Jiře.
1. Klikněte na **Přesměrování** v levém panelu.
![](./media/samlssojira-tutorial/ssore1.png)

1. Zaškrtněte **políčko Povolit přesměrování při obnovení služby SSO**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Stiskněte tlačítko **Uložit nastavení** v pravém horním rohu.

Po aktivaci možnosti můžete stále dosáhnout výzvy uživatelského jména/hesla, pokud je zaškrtnuta možnost **Povolit nosso** tak, že přejdete na [https://\<server-base-url>/login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Jako vždy, náhradní ** \<server-base-url>** se svou základní URL.


## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

