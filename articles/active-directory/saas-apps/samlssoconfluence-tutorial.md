---
title: 'Kurz: Azure Active Directory integrace se službou SAML SSO pro Confluence podle rezoluce GmbH | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAML SSO pro Confluence pomocí rezoluce GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 64e358ef6c20c72b1a6a406df1e49ca5a9763b1c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094240"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Kurz: Azure Active Directory integrace se službou SAML SSO pro Confluence podle rezoluce GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování SAML pro Confluence pomocí řešení GmbH s Azure Active Directory (Azure AD). Při integraci jednotného přihlašování SAML pro Confluence pomocí řešení GmbH s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k jednotnému přihlašování SAML pro Confluence podle rezoluce GmbH.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SAML SSO pro Confluence pomocí rezoluce GmbH s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* JEDNOTNÉ přihlašování SAML pro Confluence vyhledá předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jednotné přihlašování SAML pro Confluence podle rezoluce GmbH podporuje **aktualizace SP a IDP,** které iniciovaly jednotné přihlašování

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Přidání jednotného přihlašování SAML pro Confluence podle rezoluce GmbH z Galerie

Pokud chcete nakonfigurovat integraci SAML SSO pro Confluence podle rezoluce GmbH na Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat jednotné přihlašování SAML pro Confluence podle rezoluce GmbH z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **SAML SSO pro Confluence by Resolution GmbH** .
1. Vyberte z panelu výsledků položku **SAML SSO pro Confluence podle rezoluce GmbH** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro SAML SSO for Confluence podle rezoluce GmbH

Konfigurace a testování jednotného přihlašování služby Azure AD pomocí jednotného přihlašování SAML pro Confluence podle rezoluce GmbH pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci SAML SSO pro Confluence podle rezoluce GmbH.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování služby Azure AD pomocí jednotného přihlašování SAML pro Confluence podle rezoluce GmbH, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurujte jednotné přihlašování SAML pro Confluence podle rezoluce GmbH SSO](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvořte jednotné přihlašování SAML pro Confluence podle řešení GmbH Test User](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** – Pokud chcete mít protějšek Britta Simon v jednotném přihlašování SAML pro Confluence podle rezoluce GmbH, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce rozhraní **SAML SSO pro Confluence podle** článku Application Integration GmbH najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [podporu SAML SSO pro Confluence podle týmu podpory pro klienty](https://www.resolution.de/go/support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k JEDNOTNÉmu přihlašování SAML pro Confluence podle rezoluce GmbH.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte položku **SAML SSO pro Confluence podle rezoluce GmbH**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Konfigurace jednotného přihlašování SAML pro Confluence podle rezoluce GmbH SSO

1. V jiném okně webového prohlížeče se přihlaste ke svému **jednotnému přihlašování SAML pro Confluence pomocí portálu pro správu s rozlišením GmbH** jako správce.

2. Najeďte myší na ozubeného kola a klikněte na **Doplňky**.
    
    ![Snímek obrazovky, na kterém je vybraná ikona "ozubeného kola" a v rozevíracím seznamu je vybraná možnost Přidat doplňky](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. Budete přesměrováni na stránku pro přístup správce. Zadejte heslo a klikněte na tlačítko **Potvrdit** .

    ![Snímek obrazovky zobrazující stránku "přístup správce" s vybraným tlačítkem potvrdit.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. Na kartě **tržiště ATLASSIAN** klikněte na **najít nové doplňky**. 

    ![Snímek obrazovky, který zobrazuje kartu "Attlassian Marketplace" s vybranými možnostmi najít nové doplňky.](./media/saml-sso-confluence-tutorial/add-on.png)

5. Prohledejte **jednotné přihlašování (SSO) SAML pro Confluence** a kliknutím na tlačítko **nainstalovat** nainstalujte nový modul plug-in SAML.

    ![Snímek obrazovky se stránkou "najít nové doplňky" s jediným přihlašováním (s/S) pro Confluence do vyhledávacího pole a vybraným tlačítkem nainstalovat.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. Spustí se instalace modulu plug-in. Klikněte na **Zavřít**.

    ![Snímek obrazovky se zobrazeným dialogovým oknem pro instalaci](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Snímek obrazovky s informacemi o tom, že je nainstalovaný a připravený k přechodu dialog s vybranou akcí zavřít](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Klikněte na **Manage** (Spravovat).

    ![Snímek obrazovky, který zobrazuje stránku aplikace "S jedním přihlašováním (S/S) pro Confluence" s vybraným tlačítkem "spravovat".](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Kliknutím na **Konfigurovat** Nakonfigurujte nový modul plug-in.

    ![Snímek obrazovky zobrazující stránku Správa s vybraným tlačítkem konfigurovat](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Tento nový modul plug-in je taky možné najít na kartě **uživatelé & zabezpečení** .

    ![Snímek obrazovky zobrazující kartu "uživatelé & zabezpečení" s vybraným "S A M L SingleSignOn".](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Na stránce **Konfigurace modulu plug-in SAML SingleSignon** klikněte na tlačítko **Přidat nové IDP** a nakonfigurujte nastavení poskytovatele identity.

    ![Snímek obrazovky se stránkou konfigurace modulu plug-in SingleSignOn s informacemi o tom, že je vybráno tlačítko Přidat nové I d P.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Na stránce **zvolit poskytovatele identity SAML** proveďte následující kroky:

    ![Snímek obrazovky se zvýrazněnou stránkou "zvolit poskytovatele identity S M L", který má zvýrazněné textové pole "I d P", "Name" a "Description".](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Nastavte **Azure AD** jako typ IDP.
    
    b. Přidejte **jméno** zprostředkovatele identity (např. Azure AD).
    
    c. Přidejte **Popis** zprostředkovatele identity (např. Azure AD).
    
    d. Klikněte na **Next** (Další).
    
12. Na stránce **Konfigurace zprostředkovatele identity** klikněte na tlačítko **Další** .

    ![Snímek obrazovky zobrazující stránku "Konfigurace zprostředkovatele identity" s vybraným tlačítkem "Další".](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Na stránce **importovat metadata IDP SAML** proveďte následující kroky:

    ![Snímek obrazovky se stránkou "importovat S A M L I d P metadata" se zvolenými tlačítky importovat, načíst soubor a další.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Klikněte na tlačítko **načíst soubor** a vyberte soubor XML s metadaty, který jste stáhli v kroku 5.

    b. Klikněte na tlačítko **importovat** .
    
    c. Chvíli počkejte, dokud nebude import úspěšný.
    
    d. Klikněte na tlačítko **Další** .
    
14. Kliknutím na tlačítko Další na stránce **atributu ID uživatele a transformaci** klikněte na tlačítko **Další** .

    ![Snímek obrazovky zobrazující stránku atributu ID uživatele a transformace s vybraným tlačítkem Další](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. Na stránce **Vytvoření a aktualizace uživatele** klikněte na **Uložit & vedle** možnosti Uložit nastavení.   
    
    ![Snímek obrazovky zobrazující stránku vytvoření a aktualizace uživatele s vybraným tlačítkem Uložit & další](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. Na stránce **Testovat nastavení** klikněte na **Přeskočit test & nakonfigurovat ručně** , aby se tento test uživatele teď přeskočil. Tato akce se provede v následující části a vyžaduje některá nastavení v Azure Portal. 
    
    ![Snímek obrazovky se stránkou testování nastavení s vybraným tlačítkem Přeskočit test & konfigurovat ručně.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. V dialogovém okně zobrazení, které **přeskočí test znamená...**, klikněte na tlačítko **OK**.
    
    ![Konfigurace jednoho Sign-On](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Vytvoření jednotného přihlašování SAML pro Confluence podle řešení GmbH Test User

Aby se uživatelé Azure AD mohli přihlásit ke SAML SSO pro Confluence podle rezoluce GmbH, musí se zřídit do jednotného přihlašování SAML pro Confluence podle rezoluce GmbH.  
V případě jednotného přihlašování SAML pro Confluence podle rezoluce GmbH je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému jednotnému přihlašování SAML pro Confluence pomocí webu usnesení GmbH jako správce.

2. Najeďte myší na ozubeného kola a klikněte na **Správa uživatelů**.

    ![Snímek obrazovky s vybranou ikonou "ozubeného kola" a vybranou z nabídky "Správa uživatelů".](./media/saml-sso-confluence-tutorial/user-1.png) 

3. V části Uživatelé klikněte na kartu **Přidat uživatele** . Na stránce **Přidat uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. Do textového pole **uživatelské jméno** zadejte e-mail uživatele, jako je Britta Simon.

    b. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je Britta Simon.

    c. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com .

    d. Do textového pole **heslo** zadejte heslo pro Britta Simon.

    e. Klikněte na **Potvrdit heslo** znovu zadejte heslo.
    
    f. Klikněte na tlačítko **Přidat** .

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL jednotného přihlašování SAML pro Confluence podle adresy URL pro podpis GmbH, kde můžete spustit tok přihlášení.  

* Přihlaste se k SAML SSO pro Confluence podle adresy URL pro přihlašování na základě překladu GmbH a potom z něj zahajte tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni ke službě SAML SSO pro Confluence podle rezoluce GmbH, pro kterou jste si nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SAML SSO pro Confluence podle rezoluce GmbH v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k rozhraní SAML SSO pro Confluence pomocí rezoluce GmbH, pro kterou jste si nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete jednotné přihlašování SAML pro Confluence podle rezoluce GmbH, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
