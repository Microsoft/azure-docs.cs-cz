---
title: 'Kurz: Azure Active Directory integrace se službou SAML SSO pro JIRA podle rezoluce GmbH | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAML SSO pro JIRA pomocí rezoluce GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: e1440feacd10935b15195110cd7ea3e20b4d523c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687613"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Kurz: Azure Active Directory integrace se službou SAML SSO pro JIRA podle rezoluce GmbH

V tomto kurzu se naučíte, jak nastavit jednotné přihlašování SAML pro JIRA pomocí řešení GmbH s Azure Active Directory (Azure AD).
Integrace jednotného přihlašování SAML pro JIRA podle rezoluce GmbH s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří se můžou přihlašovat k JIRA pomocí modulu plug-in SAML SSO podle rezoluce GmbH.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili do JIRA s účty Azure AD pomocí jednotného přihlašování SAML pro JIRA podle rezoluce GmbH (jednotné přihlašování).
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD a jednotného přihlašování SAML pro JIRA podle rezoluce GmbH budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete si ho stáhnout [sem](https://azure.microsoft.com/pricing/free-trial/) v měsíční zkušební verzi.
* JEDNOTNÉ přihlašování SAML pro JIRA podle řešení GmbH s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jednotné přihlašování SAML pro JIRA podle rezoluce GmbH podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Přidání podnikové aplikace pro jednotné přihlašování

Aby bylo možné nastavit jednotné přihlašování ve službě Azure AD, je nutné přidat novou podnikovou aplikaci. V galerii je k dispozici předem nakonfigurovaná předkonfigurovaná aplikace pro toto **rozhraní SAML SSO pro JIRA podle rezoluce GmbH**.

**K přidání jednotného přihlašování SAML pro JIRA podle rezoluce GmbH z Galerie proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a klikněte na **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAML SSO pro JIRA podle rezoluce GmbH**, v panelu výsledek vyberte **SAML SSO pro JIRA by Resolution GmbH** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci. Můžete také změnit název podnikové aplikace.

     ![Jednotné přihlašování SAML pro JIRA podle rezoluce GmbH v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Konfigurace a testování jednotného přihlašování pomocí modulu plug-in SAML SSO a Azure AD

V této části budete testovat a konfigurovat jednotné přihlašování k JIRA pro uživatele Azure AD. Tato akce se provede pro testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci SAML SSO pro JIRA podle rezoluce GmbH.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování, musíte provést následující kroky:

1. **[Nakonfigurujte podnikovou aplikaci Azure AD pro jednotné přihlašování](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** – nakonfigurujte podnikovou aplikaci Azure AD pro jednotné přihlašování.
2. **[Konfigurace modulu plug-in SAML SSO vaší instance JIRA](#configure-the-saml-sso-plugin-of-your-jira-instance)** – nakonfigurujte nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – vytvoření testovacího uživatele ve službě Azure AD.
1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – povolení testovacího uživatele pro použití jednotného přihlašování na straně Azure.
1. **[Vytvoření testovacího uživatele v JIRA](#create-the-test-user-also-in-jira)** – vytvoření testovacího uživatele protějšku v JIRA pro testovacího uživatele Azure AD.
1. **[Testování jednotného přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Nakonfigurujte podnikovou aplikaci Azure AD pro jednotné přihlašování.

V této části nastavíte jednotné přihlašování v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování pomocí jednotného přihlašování SAML pro JIRA podle rezoluce GmbH, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)v právě VYTVOŘENém **jednotném přihlašování SAML pro JIRA podle rezoluce GmbH** Enterprise Application vyberte v levém panelu **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V části **Vyberte metodu jednotného přihlašování** vyberte režim **SAML** pro povolení jednotného přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Následně kliknutím na ikonu **Upravit** otevřete základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurační oddíl SAML** , pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte následující kroky:

    ![Snímek obrazovky s oddílem konfigurace "základní S a M L" se zvýrazněnými textovými poli "identifikátor" a "odpovědět U R L" a vybraným tlačítkem Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Jednotné přihlašování SAML pro JIRA podle rozlišení domény a adres URL v protokolu GmbH](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Pro identifikátor, adresu URL odpovědi a adresu URL pro přihlášení nahraďte **\<server-base-url>** základní adresou URL vaší instance JIRA. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal. Pokud máte nějaký problém, kontaktujte nás na [webu SAML SSO pro JIRA od týmu podpory pro řešení GmbH](https://www.resolution.de/go/support).

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** stáhněte **kód XML federačních metadat** a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Konfigurace modulu plug-in SAML SSO vaší instance JIRA 

1. V jiném okně webového prohlížeče se přihlaste ke své instanci JIRA jako správce.

2. Najeďte myší na ozubeného kola na pravé straně a klikněte na **Spravovat aplikace**.
    
    ![Snímek obrazovky zobrazující šipku ukazující na ikoně "ozubeného kola" a v rozevíracím seznamu vyberte možnost spravovat aplikace.](./media/samlssojira-tutorial/addon1.png)

3. Pokud jste přesměrováni na stránku pro přístup správce, zadejte **heslo** a klikněte na tlačítko **Potvrdit** .

    ![Snímek obrazovky zobrazující stránku "přístup správce"](./media/samlssojira-tutorial/addon2.png)

4. JIRA se normálně přesměruje na web Atlassian Marketplace. Pokud ne, klikněte na tlačítko **najít nové aplikace** na levém panelu. Vyhledejte **jednotné přihlašování (SSO) SAML pro JIRA** a kliknutím na tlačítko **nainstalovat** nainstalujte modul plug-in SAML.

    ![Snímek obrazovky se stránkou "Atlassian Marketplace pro JIRA" se šipkou ukazující na tlačítko "instalovat" pro "S" S "S" S a M L Single Sign on "(S/S) JIRA, S aplikací A M L/S O.](./media/samlssojira-tutorial/store.png)

5. Spustí se instalace modulu plug-in. Až to bude hotové, klikněte na tlačítko **Zavřít** .

    ![Snímek obrazovky se zobrazeným dialogovým oknem pro instalaci](./media/samlssojira-tutorial/store-2.png)

    ![Snímek obrazovky s informacemi o tom, že je nainstalovaný a připravený k přechodu dialog s vybraným tlačítkem "Zavřít".](./media/samlssojira-tutorial/store-3.png)

6. Pak klikněte na **Spravovat**.

    ![Snímek obrazovky, který zobrazuje "S A M L Single Signing JIRA (S/s)" aplikace s vybraným tlačítkem "spravovat".](./media/samlssojira-tutorial/store-4.png)
    
8. Následně klikněte na **Konfigurovat** a nakonfigurujte právě nainstalovaný modul plug-in.

    ![Snímek obrazovky zobrazující stránku "spravovat aplikace" s vybraným tlačítkem konfigurovat pro aplikaci S A M L SingleSignOn pro JIRA.](./media/samlssojira-tutorial/store-5.png)

9. V průvodci **konfigurací modulu plug-in SAML SingleSignon** klikněte na **Přidat nový IDP** a nakonfigurujte Azure AD jako nového poskytovatele identity.

    ![Snímek obrazovky s vybraným tlačítkem přidat nové I d P zobrazí stránku Vítejte.](./media/samlssojira-tutorial/addon4.png) 

10. Na stránce **Vyberte poskytovatele identity SAML** proveďte následující kroky:

    ![Snímek obrazovky se zvýrazněnou stránkou "volba vašeho zprostředkovatele identity S M L" s zvýrazněnými textovými poli "I d P" a "název" a tlačítkem "Další".](./media/samlssojira-tutorial/addon5a.png)
 
    a. Nastavte **Azure AD** jako typ IDP.
    
    b. Přidejte **jméno** zprostředkovatele identity (např. Azure AD).
    
    c. Přidejte (volitelné) **Popis** zprostředkovatele identity (např. Azure AD).
    
    d. Klikněte na **Next** (Další).
    
11. Na stránce **Konfigurace zprostředkovatele identity** klikněte na **Další**.
 
    ![Snímek obrazovky zobrazující stránku "Konfigurace zprostředkovatele identity".](./media/samlssojira-tutorial/addon5b.png)

12. Na stránce **importovat metadata IDP SAML** proveďte následující kroky:

    ![Snímek obrazovky se stránkou s metadaty import S M L I d P se zvolenou akcí vybrat metadata X M L souboru.](./media/samlssojira-tutorial/addon5c.png)

    a. Klikněte na tlačítko **Vybrat soubor XML metadat** a vyberte soubor **XML s federačními metadaty** , který jste si stáhli dřív.

    b. Klikněte na tlačítko **Import** .
     
    c. Počkejte krátce, dokud import neproběhne úspěšně.  
     
    d. Klikněte na tlačítko **Další** .
    
13. Na stránce s **uživatelským ID a transformací** klikněte na tlačítko **Další** .

    ![Snímek obrazovky zobrazující stránku atributu a transformace uživatele s vybraným tlačítkem Další](./media/samlssojira-tutorial/addon5d.png)
    
14. Na stránce **Vytvoření a aktualizace uživatele** klikněte na **Uložit & vedle** Uložit nastavení.
    
    ![Snímek obrazovky zobrazující stránku vytvoření a aktualizace uživatele s vybraným tlačítkem Uložit & další](./media/samlssojira-tutorial/addon6a.png)
    
15. Na stránce **Test nastavení** klikněte na **Přeskočit test & nakonfigurovat ručně** , aby se tento test uživatele teď přeskočil. Tato akce bude provedena v následující části a vyžaduje některá nastavení v Azure Portal.
    
    ![Snímek obrazovky se stránkou testování nastavení s vybraným tlačítkem Přeskočit test & konfigurovat ručně.](./media/samlssojira-tutorial/addon6b.png)
    
16. Kliknutím na tlačítko **OK** přeskočíte upozornění.
    
    ![Snímek obrazovky, který zobrazuje dialogové okno s upozorněním s vybraným tlačítkem "O".](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon. S uživatelem budete testovat jednotné přihlašování.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve **vlastnostech uživatele** proveďte následující kroky:

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **Britta Simon**.
  
    b. Do pole **uživatelské jméno** zadejte <b>BrittaSimon@contoso.com</b> .

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části přidáte do podnikové aplikace Britta Simon, která umožňuje použití jednotného přihlašování.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**. 

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyhledejte podnikovou aplikaci, kterou jste vytvořili na začátku tohoto kurzu. Pokud budete postupovat podle kroků v tomto kurzu, nazývá se to **JIRA SSO SSO pro podle rezoluce GmbH**. Pokud jste mu přihlásili jiný název, vyhledejte tento název.

    ![Odkaz na rozhraní SAML SSO pro JIRA podle rezoluce GmbH v seznamu aplikací](common/all-applications.png)

3. Na levém panelu klikněte na **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-the-test-user-also-in-jira"></a>Vytvoření testovacího uživatele také v JIRA

Aby se uživatelé Azure AD mohli přihlašovat k JEDNOTNÉmu přihlašování SAML pro JIRA podle rezoluce GmbH, musí se zřídit do jednotného přihlašování SAML pro JIRA podle rezoluce GmbH. V případě tohoto kurzu musíte zřizování provést ručně. Pro modul plug-in SAML SSO je ale k dispozici i další zřizovací modely, například při zřizování **jenom v čase** . Informace najdete v dokumentaci k [jednotnému přihlašování SAML pomocí rezoluce GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Pokud máte otázku, obraťte se na podporu [řešení](https://www.resolution.de/go/support).

**Chcete-li ručně zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k instanci JIRA jako správce.

2. Najeďte myší na ozubeného kola a vyberte **Správa uživatelů**.

   ![Snímek obrazovky, který zobrazuje šipku ukazující na ikoně "ozubeného kola", která je vybrána v rozevíracím seznamu "Správa uživatelů".](./media/samlssojira-tutorial/user1.png)

3. Pokud jste přesměrováni na stránku pro přístup správce, zadejte **heslo** a klikněte na tlačítko **Potvrdit** .

    ![Snímek obrazovky zobrazující stránku přístup správce se zvýrazněným textovým polem heslo](./media/samlssojira-tutorial/user2.png) 

4. V části karta **Správa uživatelů** klikněte na možnost **vytvořit uživatele**.

    ![Snímek obrazovky zobrazující kartu Správa uživatelů s vybraným tlačítkem pro vytvoření uživatele](./media/samlssojira-tutorial/user3-new.png) 

5. Na stránce **vytvořit nového uživatele** proveďte následující kroky. Je nutné, aby byl uživatel vytvořen stejně jako v Azure AD:

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user4-new.png) 

    a. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu uživatele:  <b>BrittaSimon@contoso.com</b> .

    b. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele: **Britta Simon**.

    c. Do textového pole **uživatelské jméno** zadejte e-mailovou adresu uživatele: <b>BrittaSimon@contoso.com</b> . 

    d. Do textového pole **heslo** zadejte heslo uživatele.

    e. Kliknutím na **vytvořit uživatele** dokončíte Vytvoření uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici SSO SSO pro JIRA podle rezoluce GmbH, měli byste se automaticky přihlásit k rozhraní SAML SSO pro JIRA, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

Můžete také otestovat jednotné přihlašování, pokud přejdete na `https://<server-base-url>/plugins/servlet/samlsso` . Nahraďte **\<server-base-url>** základní adresou URL vaší instance JIRA.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Povolit přesměrování jednotného přihlašování pro JIRA

Jak je uvedeno v části dřív, teď existují dva způsoby, jak spustit jednotné přihlašování. Buď pomocí **Azure Portal** , nebo pomocí **speciálního odkazu na instanci JIRA**. Modul plug-in SAML SSO podle rezoluce GmbH také umožňuje spustit jednotné přihlašování pouhým **přístupem k libovolné adrese URL, která odkazuje na instanci JIRA**.

V podstatě budou všichni uživatelé přistupující k JIRA po aktivaci možnosti v modulu plug-in přesměrováni do jednotného přihlašování.

Pokud chcete aktivovat přesměrování jednotného přihlašování, proveďte v **instanci JIRA** toto:

1. Přejděte na stránku konfigurace modulu plug-in SAML SSO v JIRA.
1. Na levém panelu klikněte na **přesměrování** .

   ![Částečný snímek obrazovky se stránkou konfigurace modulu plug-in JIRA SAML SingleSignOn zvýraznění odkazu přesměrování v levém navigačním panelu.](./media/samlssojira-tutorial/ssore1.png)

1. **Zapněte přesměrování jednotného přihlašování**.

   ![Částečný snímek obrazovky JIRA konfigurace modulu plug-in SAML SingleSignOn, který zvýrazní vybrané zaškrtávací políčko Povolit přesměrování jednotného přihlašování (SSO)](./media/samlssojira-tutorial/ssore2.png) 

1. V pravém horním rohu stiskněte tlačítko **Uložit nastavení** .

Po aktivaci možnosti se zobrazí výzva k zadání uživatelského jména a hesla, pokud je možnost **Povolit Nosso** zapnutá přechodem na `https://<server-base-url>/login.jsp?nosso` . Jako vždy nahraďte **\<server-base-url>** základní adresou URL.


## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)