---
title: 'Kurz: Integrace služby Azure Active Directory s Marketem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 0488fd1e9bc10d61d6660745acfc8c39becf3a89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159472"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Kurz: Integrace služby Azure Active Directory s Marketem

V tomto kurzu se dozvíte, jak integrovat Marketo s Azure Active Directory (Azure AD).
Integrace Marketo s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Marketo.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke společnosti Marketo (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Marketo, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením společnosti Marketo

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Marketo podporuje **IDP** inicioval jednotné přihlašovat

## <a name="adding-marketo-from-the-gallery"></a>Přidání Marketo z galerie

Chcete-li nakonfigurovat integraci Marketo do Azure AD, musíte přidat Marketo z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Marketo z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Marketo**, z panelu výsledků vyberte **Marketo** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Marketo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Marketo na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Marketu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Marketo, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace marketo jednotného přihlášení](#configure-marketo-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Marketo](#create-marketo-test-user)** – chcete-li mít protějšek Britta Simon v Marketo, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí marketo, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Marketo** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Marketo Doména a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://saml.marketo.com/sp`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL identifikátoru a odpovědi. Obraťte se na [tým podpory klienta Marketo,](https://investors.marketo.com/contactus.cfm) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Marketo** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-marketo-single-sign-on"></a>Konfigurace jednotného přihlášení marketo

1. Chcete-li získat Munchkin Id vaší aplikace, přihlaste se do Marketo pomocí přihlašovacích údajů správce a proveďte následující akce:
   
    a. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.
   
    b. V horním navigačním podokně klikněte na tlačítko **Správce.**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky Integrace a klepněte na **odkaz Munchkin**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Zkopírujte ID Munchkin zobrazené na obrazovce a vyplňte adresu URL odpovědi v průvodci konfigurací Azure AD.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Chcete-li nakonfigurovat zabezpečení v aplikaci, postupujte podle následujících kroků:
   
    a. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.
   
    b. V horním navigačním podokně klikněte na tlačítko **Správce.**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky Integrace a klepněte **na položku Jednotné přihlášení**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Chcete-li povolit nastavení SAML, klepněte na tlačítko **Upravit.**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Povoleno** Nastavení jednotného přihlašování.
   
    f. Vložte **identifikátor Azure AD**do textového pole **ID vystavitisty.**
   
    g. Do textového pole **ID entity** `http://saml.marketo.com/sp`zadejte adresu URL jako .
   
    h. Vyberte prvek Lokace ID uživatele jako **identifikátor jména**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Pokud váš identifikátor uživatele není hodnota UPN, změňte hodnotu na kartě Atribut.
   
    i. Nahrajte certifikát, který jste stáhli z Průvodce konfigurací služby Azure AD. **Uložte** nastavení.
   
    j. Upravte nastavení Přesměrovat stránky.
   
    k. Vložte **přihlašovací adresu URL** do textového pole Přihlašovací adresa **URL.**
   
    l. Vložte **adresu URL odhlášení** do textového pole **Adresa URL odhlášení.**
   
    m. V **adrese URL chyby**zkopírujte **adresu URL instance Marketo** a klepnutím na tlačítko **Uložit** nastavení uložte.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Chcete-li povolit automatické přispojené k ono pro uživatele, proveďte následující akce:
   
    a. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.
   
    b. V horním navigačním podokně klikněte na tlačítko **Správce.**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky **Zabezpečení** a klepněte na **položku Nastavení přihlášení**.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Zkontrolujte **možnost Vyžadovat přihlašování** a **uložte** nastavení.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_14.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Marketo.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Marketo**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost Marketo**.

    ![Odkaz Marketo v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-marketo-test-user"></a>Vytvořit uživatele testu Marketo

V této části vytvoříte uživatele s názvem Britta Simon v Marketo. Podle těchto kroků vytvořte uživatele v platformě Marketo.

1. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.

2. V horním navigačním podokně klikněte na tlačítko **Správce.**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Přejděte do nabídky **Zabezpečení** a klikněte na **Uživatelé & role.**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klikněte na odkaz **Pozvat nového uživatele** na kartě Uživatelé.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. V průvodci Pozvat nového uživatele vyplňte následující informace
   
    a. Zadání **e-mailové** adresy uživatele do textového pole
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Zadání **křestního jména** do textového pole
   
    c. Zadejte **příjmení** do textového pole.
   
    d. Klikněte na **Další.**

6. Na kartě **Oprávnění** vyberte **userRoles** a klikněte na **Další.**
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kliknutím na tlačítko **Odeslat** odešlete pozvánku pro uživatele.
   
    ![Konfigurace jednotného přihlašování](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Uživatel obdrží e-mailové oznámení a musí kliknout na odkaz a změnit heslo pro aktivaci účtu. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Marketo na přístupovém panelu, můžete by měl být automaticky přihlášeni k Marketo, u kterého nastavíte jednotné připojetí. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

