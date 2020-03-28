---
title: 'Kurz: Integrace služby Azure Active Directory se službou JEDNOTNÉHO PŘIHLADA SAML pro soutok podle překladu GmbH | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a jednotném přihlašování SAML pro confluence by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9676a71940569b26d6b0b6bfef767108ae57d953
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161211"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Kurz: Integrace služby Azure Active Directory s jednotném přihlašovacím službou SAML pro soužení podle řešení GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašovací služby SAML pro confluence podle řešení GmbH s Azure Active Directory (Azure AD).
Integrace jednotného přihlašování SAML pro confluence podle řešení GmbH s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k jednotnému přihlašovacím místě SAML pro confluence podle řešení GmbH.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k jednotnému přihlašování SAML pro confluence podle řešení GmbH (Single Sign-On) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s jednotného přihlašovacího zabezpečení SAML pro confluence podle řešení GmbH, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Saml Jednotné přihlašování pro confluence podle rozlišení GmbH jednotné přihlášení povoleno předplatné

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAML SSO pro confluence podle rozlišení GmbH podporuje **SP** a **IDP** inicioval jednotné hospo-

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Přidání SAML SSO pro Confluence podle rozlišení GmbH z galerie

Chcete-li nakonfigurovat integraci jednotného přihlašovacího zařízení SAML pro souběh podle řešení GmbH do azure ad, je třeba přidat jednotné přihlašovací služby SAML pro confluence podle překladu GmbH z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat jednotné přihlašovací právo SAML pro confluence podle rozlišení GmbH z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Ve vyhledávacím poli zadejte **SAML SSO pro Confluence by resolution GmbH**, zvolte **SAML SSO pro Confluence by resolution GmbH** z panelu výsledků a pak kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![SAML SSO pro confluence podle rozlišení GmbH v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro soutok podle řešení GmbH na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v jednotném přihlašování SAML pro confluence podle řešení GmbH.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro confluence podle řešení GmbH, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování SAML pro soutok podle rozlišení GmbH Single Sign-On](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte jednotné přihlašování SAML pro confluence podle rozlišení gmbh testovací uživatel](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** – mít protějšek Britta Simon v SAML Jednotné přihlašování pro confluence podle řešení GmbH, který je propojen s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování SAML pro confluence podle řešení GmbH, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **SAML SSO for Confluence by resolution GmbH** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciovaného iniciátorem **Protokolu IDP:**

    ![Saml Jednotné přihlašování pro soutok podle rozlišení GmbH Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném službou SP:

    ![Saml Jednotné přihlašování pro soutok podle rozlišení GmbH Domény a adresy URL jednotné přihlašovací informace](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Obraťte se [na saml jednotného přihlašovacího místa pro confluence podle řešení GmbH Klient tým podpory](https://www.resolution.de/go/support) získat tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Konfigurace jednotného přihlašování SAML pro soutok podle rozlišení GmbH Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste ke svému **jednotnému přihlašování SAML pro confluence podle překladu portálu pro správu společnosti GmbH** jako správce.

2. Najeďte přes kolečko a klikněte **na doplňky**.
    
    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon1.png)

3. Budete přesměrováni na stránku Přístup správce. Zadejte heslo a klepněte na **tlačítko Potvrdit.**

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon2.png)

4. Na kartě **ATLASSIAN MARKETPLACE** klikněte na **Najít nové doplňky**. 

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon.png)

5. Vyhledejte **saml jednotné přihlášení na (SSO) pro confluence** a klepněte na tlačítko **Nainstalovat** nainstalovat nainstalovat nový plugin SAML.

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon7.png)

6. Spustí se instalace pluginu. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon8.png)

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon9.png)

7.  Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Chcete-li nakonfigurovat nový plugin, klepněte na **tlačítko Konfigurovat.**

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon11.png)

9. Tento nový plugin lze také nalézt v záložce **USERS & SECURITY.**

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Na stránce **Konfigurace modulu plug-in SAML SingleSignOn** klikněte na **tlačítko Přidat nové tlačítko IdP** a nakonfigurujte nastavení zprostředkovatele identity.

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon4.png)

11. Na **stránce Vyberte si poskytovatele identity SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Nastavte **Azure AD** jako typ IdP.
    
    b. Přidejte **název** zprostředkovatele identity (např. Azure AD).
    
    c. Přidejte **popis** zprostředkovatele identity (např. Azure AD).
    
    d. Klikněte na **Další**.
    
12. Na **konfigurační stránce zprostředkovatele identity** klikněte na tlačítko **Další.**

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon5b.png)

13. Na stránce **Metadata idp importu SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Klikněte na tlačítko **Načíst soubor** a vyberte soubor XML metadat, který jste stáhli v kroku 5.

    b. Klikněte na **tlačítko Importovat.**
    
    c. Krátce počkejte, dokud import nebude úspěšný.
    
    d. Klikněte na tlačítko **Další.**
    
14. Na **stránce atributu ID uživatele a transformační** stránky klikněte na tlačítko **Další.**

    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. Na stránce **Vytvoření a aktualizace uživatele** mi tešete nastavení na Uložit & **další.**   
    
    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. Na **stránce Test nastavení** klikněte na Přeskočit test & **nakonfigurovat ručně** přeskočit uživatelský test pro tuto chvíli. To se provede v další části a vyžaduje některá nastavení na webu Azure Portal. 
    
    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. V zobrazeném dialogovém okně, ve **které se zobrazí přeskočení testovacích prostředků...**, klepněte na tlačítko **OK**.
    
    ![Konfigurace jednotného přihlašování](./media/samlssoconfluence-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k jednotnému přihlašování SAML pro confluence podle řešení GmbH.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte jednotné **přihlašování SAML pro soutok podle překladu GmbH**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **jednotné přihlašování SAML pro confluence podle rozlišení GmbH**.

    ![Odkaz SAML SSO pro confluence by resolution GmbH v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Vytvoření jednotného přihlašovacího úbytku SAML pro soutok podle rozlišení uživatele testu GmbH

Aby se uživatelé Azure AD mohli přihlásit k jednotnému přihlašování SAML pro confluence by resolution GmbH, musí být zřízeni do jednotného přihlašování SAML pro confluence podle řešení GmbH.  
V saml jednotného přihlašování pro soutok podle řešení GmbH zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému jednotnému přihlašování SAML pro confluence podle řešení společnosti GmbH jako správce.

2. Najeďte přes kolečko a klikněte na **správu uživatelů**.

    ![Přidat zaměstnance](./media/samlssoconfluence-tutorial/user1.png) 

3. V části Uživatelé klikněte na kartu **Přidat uživatele.** Na stránce dialogového okna **Přidat uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/samlssoconfluence-tutorial/user2.png) 

    a. Do textového pole **Uživatelské jméno** zadejte e-mail uživatele, jako je Britta Simon.

    b. Do textového pole **Celé jméno** zadejte celé jméno uživatele, jako je Britta Simon.

    c. Do textového pole **E-mail** zadejte Brittasimon@contoso.come-mailovou adresu uživatele, který se líbí .

    d. Do textového pole **Heslo** zadejte heslo pro Brittu Simonovou.

    e. Klikněte na **Potvrdit heslo** znovu zadat heslo.
    
    f. Klikněte na **tlačítko Přidat.**

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici JEDNOTNÉHO PŘIHLADĚK SAML pro soutok podle rozlišení GmbH na přístupovém panelu, měli byste být automaticky přihlášeni k jednotnému přihlašovacím příkazu SAML pro confluence podle řešení GmbH, pro které nastavíte jednotné přihlašovací právo. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

