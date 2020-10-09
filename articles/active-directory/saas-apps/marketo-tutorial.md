---
title: 'Kurz: Azure Active Directory integrace se službou Marketo | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 764f01fa5966a6620612405b4df37fc5ff44f33a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857907"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Kurz: Azure Active Directory integrace se službou Marketo

V tomto kurzu se naučíte integrovat Marketo s Azure Active Directory (Azure AD).
Integrování Marketo se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Marketo.
* Uživatelům můžete povolit, aby se automaticky přihlásili ke službě Marketo (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD se službou Marketo potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jednotným přihlašováním na úrovni Market

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Marketo podporuje **IDP** jednotné přihlašování.

## <a name="adding-marketo-from-the-gallery"></a>Přidání Marketo z Galerie

Pokud chcete nakonfigurovat integraci služby Marketo do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Marketo z galerie.

**Pokud chcete přidat Marketo z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Marketo**, vyberte **Marketo** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Marketo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD se službou Marketo na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve službě Marketo.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Marketo, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování na Marketo](#configure-marketo-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele Marketo](#create-marketo-test-user)** , abyste měli protějšek Britta Simon ve službě Marketo, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí služby Marketo, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Marketo** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně Marketo a adresám URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://saml.marketo.com/sp`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pro získání těchto hodnot kontaktujte [tým podpory Marketo pro klienty](https://investors.marketo.com/contactus.cfm) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Marketo** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-marketo-single-sign-on"></a>Konfigurace služby Marketo Single Sign-On

1. Pokud chcete získat ID Munchkin vaší aplikace, přihlaste se k Marketě pomocí přihlašovacích údajů správce a proveďte následující akce:
   
    a. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** v horním navigačním podokně.
   
    ![Snímek obrazovky se zobrazí správce vybraný v navigačním podokně.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na **odkaz Munchkin**.
   
    ![Snímek obrazovky zobrazuje Munchkin vybrané z integrace.](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Zkopírujte ID Munchkin zobrazené na obrazovce a dokončete adresu URL odpovědi v Průvodci konfigurací služby Azure AD.
   
    ![Snímek obrazovky se zobrazí stránka Munchkin, kde můžete zkopírovat účet I D.](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. K nakonfigurování jednotného přihlašování v aplikaci použijte následující postup:
   
    a. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** v horním navigačním podokně.
   
    ![Snímek obrazovky se zobrazí správce vybraný v navigačním podokně.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na **jednotné přihlašování**.
   
    ![Snímek obrazovky zobrazuje jednu Sign-On vybranou z integrace.](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Chcete-li povolit nastavení SAML, klikněte na tlačítko **Upravit** .
   
    ![Snímek obrazovky ukazuje nastavení S S S, kde můžete vybrat upravit.](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Povoleno** Nastavení jednoho Sign-On.
   
    f. Do textového pole **ID vystavitele** vložte **identifikátor Azure AD**.
   
    například Do textového pole **ID entity** zadejte adresu URL `http://saml.marketo.com/sp` .
   
    h. Vyberte umístění ID uživatele jako **element identifikátoru názvu**.
   
    ![Snímek obrazovky ukazuje upravit nastavení SAML, kde můžete zadat hodnoty, které jsou popsány.](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Pokud váš identifikátor uživatele není hodnota hlavního názvu uživatele (UPN), změňte hodnotu na kartě atribut.
   
    i. Nahrajte certifikát, který jste si stáhli z Průvodce konfigurací služby Azure AD. **Uložte** nastavení.
   
    j. Upravte nastavení přesměrování stránek.
   
    k. Vložte **přihlašovací adresu URL** do textového pole **přihlašovací adresa URL** .
   
    l. Do textového pole **Adresa URL pro odhlášení** vložte **adresu URL pro odhlášení** .
   
    m. V **chybové adrese URL**zkopírujte **adresu URL instance Marketo** a kliknutím na **Uložit** uložte nastavení.
   
    ![Snímek obrazovky se zobrazí dialogové okno Upravit stránky přesměrování, kde můžete zadat hodnoty, které jsou popsány.](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Pokud chcete povolit jednotné přihlašování pro uživatele, proveďte následující akce:
   
    a. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** v horním navigačním podokně.
   
    ![Snímek obrazovky se zobrazí správce vybraný v navigačním podokně.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky **zabezpečení** a klikněte na tlačítko **Nastavení přihlášení**.
   
    ![Snímek obrazovky zobrazuje nastavení přihlášení vybrané ze zabezpečení.](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Ověřte možnost **vyžadovat jednotné přihlašování** a **uložte** nastavení.
   
    ![Snímek obrazovky zobrazuje nastavení síly hesla, kde můžete vybrat vyžadovat S S S.](./media/marketo-tutorial/tutorial_marketo_14.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Marketo.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Marketo**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Marketo**.

    ![Odkaz na Marketo v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-marketo-test-user"></a>Vytvořit testovacího uživatele Marketo

V této části vytvoříte uživatele s názvem Britta Simon ve Marketo. pomocí těchto kroků můžete vytvořit uživatele na Market platformě.

1. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.

2. Klikněte na tlačítko **správce** v horním navigačním podokně.
   
    ![Snímek obrazovky se zobrazí správce vybraný v navigačním podokně.](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Přejděte do nabídky **zabezpečení** a klikněte na **Uživatelé & rolí** .
   
    ![Snímek obrazovky ukazuje uživatele & role vybrané ze zabezpečení.](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klikněte na odkaz **pozvat nového uživatele** na kartě Uživatelé.
   
    ![Snímek obrazovky se na kartě Uživatelé zobrazí Pozvánka nového uživatele.](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. V průvodci pozvání nového uživatele vyplňte následující informace.
   
    a. Do textového pole zadejte **e-mailovou** adresu uživatele.
   
    ![Snímek obrazovky se zobrazí v průvodci pozvánkou nového uživatele první krok, ve kterém zadáte informace o uživateli.](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Zadejte **jméno** do textového pole.
   
    c. Zadejte **příjmení**  do textového pole.
   
    d. Klikněte na **Další** .

6. Na kartě **oprávnění** vyberte **položka userroles může** a klikněte na **Další** .
   
    ![Snímek obrazovky se zobrazí v průvodci pozvánkou nového uživatele první krok, ve kterém zadáte oprávnění.](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kliknutím na tlačítko **Odeslat** odešlete pozvánku uživateli.
   
    ![Snímek obrazovky se zobrazí v průvodci pozvánkou nového uživatele první krok, kam zadáte zprávu.](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Uživatel dostane e-mailové oznámení a musí kliknout na odkaz a změnit heslo pro aktivaci účtu. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Marketo na přístupovém panelu, měli byste se automaticky přihlásit ke službě Marketo, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

