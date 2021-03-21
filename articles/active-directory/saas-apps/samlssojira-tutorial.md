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
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 450db7c01de4ea7db0133ceca962941ae1a927e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173065"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Kurz: Azure Active Directory integrace se službou SAML SSO pro JIRA podle rezoluce GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování SAML pro JIRA pomocí řešení GmbH s Azure Active Directory (Azure AD). Při integraci jednotného přihlašování SAML pro JIRA pomocí řešení GmbH s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k jednotnému přihlašování SAML pro JIRA podle rezoluce GmbH.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SAML SSO pro JIRA pomocí rezoluce GmbH s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* JEDNOTNÉ přihlašování SAML pro JIRA vyhledá předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jednotné přihlašování SAML pro JIRA podle rezoluce GmbH podporuje **aktualizace SP** a **IDP** , které zahájily jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Přidání jednotného přihlašování SAML pro JIRA podle rezoluce GmbH z Galerie

Pokud chcete nakonfigurovat integraci SAML SSO pro JIRA podle rezoluce GmbH na Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat jednotné přihlašování SAML pro JIRA podle rezoluce GmbH z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **SAML SSO pro JIRA by Resolution GmbH** .
1. Vyberte z panelu výsledků položku **SAML SSO pro JIRA podle rezoluce GmbH** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro SAML SSO for JIRA podle rezoluce GmbH

Konfigurace a testování jednotného přihlašování služby Azure AD pomocí jednotného přihlašování SAML pro JIRA podle rezoluce GmbH pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci SAML SSO pro JIRA podle rezoluce GmbH.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování služby Azure AD pomocí jednotného přihlašování SAML pro JIRA podle rezoluce GmbH, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování **[SAML pro JIRA podle rezoluce GmbH SSO](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte jednotné přihlašování SAML pro JIRA pomocí nástroje Solution Test User](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** , aby bylo možné mít protějšek B. Simon v jednotném přihlašování SAML pro JIRA podle rezoluce GmbH, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce rozhraní **SAML SSO pro JIRA podle** článku Application Integration GmbH najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurační oddíl SAML** , pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Pro identifikátor, adresu URL odpovědi a adresu URL pro přihlášení nahraďte **\<server-base-url>** základní adresou URL vaší instance JIRA. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal. Pokud máte nějaký problém, kontaktujte nás na [webu SAML SSO pro JIRA od týmu podpory pro řešení GmbH](https://www.resolution.de/go/support).

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** stáhněte **kód XML federačních metadat** a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k JEDNOTNÉmu přihlašování SAML pro JIRA podle rezoluce GmbH.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte položku **SAML SSO pro JIRA podle rezoluce GmbH**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Konfigurace jednotného přihlašování SAML pro JIRA podle rezoluce GmbH SSO 

1. V jiném okně webového prohlížeče se přihlaste ke své instanci JIRA jako správce.

2. Najeďte myší na ozubeného kola na pravé straně a klikněte na **Spravovat aplikace**.
    
    ![Snímek obrazovky zobrazující šipku ukazující na ikoně "ozubeného kola" a v rozevíracím seznamu vyberte možnost spravovat aplikace.](./media/samlssojira-tutorial/add-on-1.png)

3. Pokud jste přesměrováni na stránku pro přístup správce, zadejte **heslo** a klikněte na tlačítko **Potvrdit** .

    ![Snímek obrazovky zobrazující stránku "přístup správce"](./media/samlssojira-tutorial/add-on-2.png)

4. JIRA se normálně přesměruje na web Atlassian Marketplace. Pokud ne, klikněte na tlačítko **najít nové aplikace** na levém panelu. Vyhledejte **jednotné přihlašování (SSO) SAML pro JIRA** a kliknutím na tlačítko **nainstalovat** nainstalujte modul plug-in SAML.

    ![Snímek obrazovky se stránkou "Atlassian Marketplace pro JIRA" se šipkou ukazující na tlačítko "instalovat" pro "S" S "S" S a M L Single Sign on "(S/S) JIRA, S aplikací A M L/S O.](./media/samlssojira-tutorial/store.png)

5. Spustí se instalace modulu plug-in. Až to bude hotové, klikněte na tlačítko **Zavřít** .

    ![Snímek obrazovky se zobrazeným dialogovým oknem pro instalaci](./media/samlssojira-tutorial/store-2.png)

    ![Snímek obrazovky s informacemi o tom, že je nainstalovaný a připravený k přechodu dialog s vybraným tlačítkem "Zavřít".](./media/samlssojira-tutorial/store-3.png)

6. Pak klikněte na **Spravovat**.

    ![Snímek obrazovky, který zobrazuje "S A M L Single Signing JIRA (S/s)" aplikace s vybraným tlačítkem "spravovat".](./media/samlssojira-tutorial/store-4.png)
    
7. Následně klikněte na **Konfigurovat** a nakonfigurujte právě nainstalovaný modul plug-in.

    ![Snímek obrazovky zobrazující stránku "spravovat aplikace" s vybraným tlačítkem konfigurovat pro aplikaci S A M L SingleSignOn pro JIRA.](./media/samlssojira-tutorial/store-5.png)

8. V průvodci **konfigurací modulu plug-in SAML SingleSignon** klikněte na **Přidat nový IDP** a nakonfigurujte Azure AD jako nového poskytovatele identity.

    ![Snímek obrazovky s vybraným tlačítkem přidat nové I d P zobrazí stránku Vítejte.](./media/samlssojira-tutorial/add-on-4.png) 

9. Na stránce **Vyberte poskytovatele identity SAML** proveďte následující kroky:

    ![Snímek obrazovky se zvýrazněnou stránkou "volba vašeho zprostředkovatele identity S M L" s zvýrazněnými textovými poli "I d P" a "název" a tlačítkem "Další".](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Nastavte **Azure AD** jako typ IDP.
    
    b. Přidejte **jméno** zprostředkovatele identity (např. Azure AD).
    
    c. Přidejte (volitelné) **Popis** zprostředkovatele identity (např. Azure AD).
    
    d. Klikněte na **Next** (Další).
    
10. Na stránce **Konfigurace zprostředkovatele identity** klikněte na **Další**.
 
    ![Snímek obrazovky zobrazující stránku "Konfigurace zprostředkovatele identity".](./media/samlssojira-tutorial/configuration.png)

11. Na stránce **importovat metadata IDP SAML** proveďte následující kroky:

    ![Snímek obrazovky se stránkou s metadaty import S M L I d P se zvolenou akcí vybrat metadata X M L souboru.](./media/samlssojira-tutorial/metadata.png)

    a. Klikněte na tlačítko **Vybrat soubor XML metadat** a vyberte soubor **XML s federačními metadaty** , který jste si stáhli dřív.

    b. Klikněte na tlačítko **Import** .
     
    c. Počkejte krátce, dokud import neproběhne úspěšně.  
     
    d. Klikněte na tlačítko **Další** .
    
12. Na stránce s **uživatelským ID a transformací** klikněte na tlačítko **Další** .

    ![Snímek obrazovky zobrazující stránku atributu a transformace uživatele s vybraným tlačítkem Další](./media/samlssojira-tutorial/transformation.png)
    
13. Na stránce **Vytvoření a aktualizace uživatele** klikněte na **Uložit & vedle** Uložit nastavení.
    
    ![Snímek obrazovky zobrazující stránku vytvoření a aktualizace uživatele s vybraným tlačítkem Uložit & další](./media/samlssojira-tutorial/update.png)
    
14. Na stránce **Test nastavení** klikněte na **Přeskočit test & nakonfigurovat ručně** , aby se tento test uživatele teď přeskočil. Tato akce bude provedena v následující části a vyžaduje některá nastavení v Azure Portal.
    
    ![Snímek obrazovky se stránkou testování nastavení s vybraným tlačítkem Přeskočit test & konfigurovat ručně.](./media/samlssojira-tutorial/test.png)
    
15. Kliknutím na tlačítko **OK** přeskočíte upozornění.
    
    ![Snímek obrazovky, který zobrazuje dialogové okno s upozorněním s vybraným tlačítkem "O".](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Vytvoření jednotného přihlašování SAML pro JIRA podle řešení GmbH Test User

Aby se uživatelé Azure AD mohli přihlašovat k JEDNOTNÉmu přihlašování SAML pro JIRA podle rezoluce GmbH, musí se zřídit do jednotného přihlašování SAML pro JIRA podle rezoluce GmbH. V případě tohoto kurzu musíte zřizování provést ručně. Pro modul plug-in SAML SSO je ale k dispozici i další zřizovací modely, například při zřizování **jenom v čase** . Informace najdete v dokumentaci k [jednotnému přihlašování SAML pomocí rezoluce GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Pokud máte otázku, obraťte se na podporu [řešení](https://www.resolution.de/go/support).

**Chcete-li ručně zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k instanci JIRA jako správce.

2. Najeďte myší na ozubeného kola a vyberte **Správa uživatelů**.

   ![Snímek obrazovky, který zobrazuje šipku ukazující na ikoně "ozubeného kola", která je vybrána v rozevíracím seznamu "Správa uživatelů".](./media/samlssojira-tutorial/user-1.png)

3. Pokud jste přesměrováni na stránku pro přístup správce, zadejte **heslo** a klikněte na tlačítko **Potvrdit** .

    ![Snímek obrazovky zobrazující stránku přístup správce se zvýrazněným textovým polem heslo](./media/samlssojira-tutorial/user-2.png) 

4. V části karta **Správa uživatelů** klikněte na možnost **vytvořit uživatele**.

    ![Snímek obrazovky zobrazující kartu Správa uživatelů s vybraným tlačítkem pro vytvoření uživatele](./media/samlssojira-tutorial/user-3-new.png) 

5. Na stránce **vytvořit nového uživatele** proveďte následující kroky. Je nutné, aby byl uživatel vytvořen stejně jako v Azure AD:

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user-4-new.png) 

    a. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu uživatele:  <b>BrittaSimon@contoso.com</b> .

    b. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele: **Britta Simon**.

    c. Do textového pole **uživatelské jméno** zadejte e-mailovou adresu uživatele: <b>BrittaSimon@contoso.com</b> . 

    d. Do textového pole **heslo** zadejte heslo uživatele.

    e. Kliknutím na **vytvořit uživatele** dokončíte Vytvoření uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL jednotného přihlašování SAML pro JIRA podle adresy URL pro podpis GmbH, kde můžete spustit tok přihlášení.  

* Přihlaste se k SAML SSO pro JIRA podle adresy URL pro přihlašování na základě překladu GmbH a potom z něj zahajte tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni ke službě SAML SSO pro JIRA podle rezoluce GmbH, pro kterou jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SAML SSO for JIRA by v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlašovacích údajů a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit ke službě SAML SSO pro JIRA podle rezoluce GmbH, pro kterou jste si nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="enable-sso-redirection-for-jira"></a>Povolit přesměrování jednotného přihlašování pro JIRA

Jak je uvedeno v části dřív, teď existují dva způsoby, jak spustit jednotné přihlašování. Buď pomocí **Azure Portal** , nebo pomocí **speciálního odkazu na instanci JIRA**. Modul plug-in SAML SSO podle rezoluce GmbH také umožňuje spustit jednotné přihlašování pouhým **přístupem k libovolné adrese URL, která odkazuje na instanci JIRA**.

V podstatě budou všichni uživatelé přistupující k JIRA po aktivaci možnosti v modulu plug-in přesměrováni do jednotného přihlašování.

Pokud chcete aktivovat přesměrování jednotného přihlašování, proveďte v **instanci JIRA** toto:

1. Přejděte na stránku konfigurace modulu plug-in SAML SSO v JIRA.
1. Na levém panelu klikněte na **přesměrování** .

   ![Částečný snímek obrazovky se stránkou konfigurace modulu plug-in JIRA SAML SingleSignOn zvýraznění odkazu přesměrování v levém navigačním panelu.](./media/samlssojira-tutorial/configure-1.png)

1. **Zapněte přesměrování jednotného přihlašování**.

   ![Částečný snímek obrazovky JIRA konfigurace modulu plug-in SAML SingleSignOn, který zvýrazní vybrané zaškrtávací políčko Povolit přesměrování jednotného přihlašování (SSO)](./media/samlssojira-tutorial/configure-2.png) 

1. V pravém horním rohu stiskněte tlačítko **Uložit nastavení** .

Po aktivaci možnosti se zobrazí výzva k zadání uživatelského jména a hesla, pokud je možnost **Povolit Nosso** zapnutá přechodem na `https://<server-base-url>/login.jsp?nosso` . Jako vždy nahraďte **\<server-base-url>** základní adresou URL.

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete jednotné přihlašování SAML pro JIRA podle rezoluce GmbH, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).