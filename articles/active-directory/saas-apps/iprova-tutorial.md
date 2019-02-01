---
title: 'Kurz: Integrace Azure Active Directory s iProva | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: e07f893296ec20e8c722fc977f93a38a797fe5c9
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490052"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Kurz: Integrace Azure Active Directory s iProva

V tomto kurzu se dozvíte, jak integrovat iProva s Azure Active Directory (Azure AD).
IProva integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k iProva.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k iProva (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iProva, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/) webu.
* IProva podporou jednotného přihlašování k odběru.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotné přihlašování:

* iProva podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování.

## <a name="add-iprova-from-the-gallery"></a>Přidání iProva z Galerie

Pokud chcete nakonfigurovat integraci iProva do služby Azure AD, přidejte na seznam spravovaných aplikací SaaS iProva z galerie.

Chcete-li přidat iProva z galerie, postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **iProva**. Vyberte **iProva** z panelu výsledek a pak vyberte **přidat** pro přidání aplikace.

     ![iProva v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí iProva podle testovacího uživatele s názvem Britta Simon.
Pro jednotné přihlašování pro práci je potřeba vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v iProva.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iProva, proveďte následující stavebních bloků:

- [Načítání informací o konfiguraci z iProva](#retrieve-configuration-information-from-iprova) jako příprava pro další kroky.
- [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
- [Konfigurace iProva jednotného přihlašování](#configure-iprova-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
- [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
- [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
- [Vytvořit testovacího uživatele iProva](#create-an-iprova-test-user) mít protějšek Britta Simon v iProva, který je propojený s Azure AD zastoupení uživatele.
- [Otestovat jednotné přihlašování](#test-single-sign-on) ověřit, jestli funguje v konfiguraci.

### <a name="retrieve-configuration-information-from-iprova"></a>Načítání informací o konfiguraci z iProva

V této části se načíst informace z iProva ke konfiguraci Azure AD jednotného přihlašování.

1. Otevřete webový prohlížeč a přejděte **informace o typu SAML2** stránky v iProva pomocí následující vzor adresy URL:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![Zobrazit informační stránku iProva SAML2](media/iprova-tutorial/iprova-saml2-info.png)

2. Na kartě prohlížeče ponechte otevřené, když budete pokračovat s dalšími kroky na nové kartě prohlížeče.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s iProva, postupujte podle těchto kroků.

1. V [webu Azure portal](https://portal.azure.com/)na **iProva** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogové okno, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno.

    ![Upravit ikonu v základní konfiguraci SAML](common/edit-urls.png)

4. V **základní konfiguraci SAML** , postupujte podle těchto kroků.

    a. Zadejte **identifikátor** pole s hodnotou, která se zobrazí za popisek **EntityID** na **iProva informace o typu SAML2** stránky. Tato stránka je stále otevřen v druhé záložce prohlížeče.

    b. Zadejte **adresy URL odpovědi** pole s hodnotou, která se zobrazí za popisek **adresy URL odpovědi** na **iProva informace o typu SAML2** stránky. Tato stránka je stále otevřen v druhé záložce prohlížeče.

    c. Zadejte **přihlašovací adresa URL** pole s hodnotou, která se zobrazí za popisek **přihlašovací adresa URL** na **iProva informace o typu SAML2** stránky. Tato stránka je stále otevřen v druhé záložce prohlížeče.

    ![iProva domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

5. Aplikace iProva očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **atributy uživatele** dialogové okno.

    ![Dialogové okno atributů uživatele](common/edit-attribute.png)

6. V **deklarace identity uživatelů** tématu **atributy uživatele** dialogového okna nastavte atribut tokenu SAML, jak je znázorněno na předchozím obrázku. Postupujte následovně.

    | Název | Zdrojový atribut| Obor názvů |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Vyberte **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogové okno.

    ![Deklarace identity uživatele](common/new-save-attribute.png)

    ![Správa dialogové okno deklarace identity uživatele](common/new-attribute-details.png)

    b. V **název** zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **Namespace** seznamu, zadejte hodnotu oboru názvů zobrazený pro tento řádek.

    d. Vyberte **zdroj** možnost jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Vyberte **OK**.

    g. Vyberte **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **kopírování** ikonu zkopírujte **adresa Url federačních metadat aplikace**  a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Konfigurace iProva jednotného přihlašování

1. Přihlaste se k iProva pomocí **správce** účtu.

2. Otevřít **přejít na** nabídky.

3. Vyberte **správy aplikací**.

4. Vyberte **Obecné** v **nastavení systému** panelu.

5. Vyberte **Upravit**.

6. Přejděte dolů k položce **řízení přístupu**.

    ![nastavení řízení přístupu iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Najít nastavení **automaticky přihlášeni uživatelé pomocí svých účtů sítě**a změňte ho na **Ano, ověřování pomocí SAML**. Nyní se zobrazí další možnosti.

8. Vyberte **nastavit**.

9. Vyberte **Další**.

10. iProva zeptá, zda chcete stáhnout data federace z adresy URL nebo nahrajte ho ze souboru. Vyberte **z adresy URL** možnost.

    ![Stáhnout metadata služby Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Vložte adresu URL metadat, které jste si uložili v předchozím kroku v části "Konfigurace služby Azure AD single sign-on".

12. Vyberte tlačítko ve tvaru šipka se stáhnout metadata ze služby Azure AD.

13. Po dokončení stahování, potvrzovací zpráva **platná Data federace soubor stažený** se zobrazí.

14. Vyberte **Další**.

15. Přeskočit **Test přihlášení** a vyberte možnost prozatím **Další**.

16. V **deklaraci identity pro použití** rozevíracího seznamu vyberte **windowsaccountname**.

17. Vyberte **Finish** (Dokončit).

18. Teď vrátit **upravit obecná nastavení** obrazovky. Posuňte se dolů dolní části stránky a vyberte **OK** uložte konfiguraci.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal s názvem Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.

    ![Uživatelé a skupiny a všechny odkazy na uživatele](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V **uživatele** dialogové okno pole, postupujte podle těchto kroků.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte název, například **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte *yourname@yourcompanydomain.extension*. 
    Příklad: BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k iProva.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **iProva**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **iProva**.

    ![Odkaz iProva v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Propojení uživatelů a skupin](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Dialogové okno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v **uživatelé** seznamu a klikněte na tlačítko **vyberte** v dolní části obrazovky.

6. Pokud očekáváte, že všechny role hodnotu v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Zvolte **vyberte** v dolní části obrazovky.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-an-iprova-test-user"></a>Vytvořit testovacího uživatele iProva

1. Přihlaste se k iProva pomocí **správce** účtu.

2. Otevřít **přejít na** nabídky.

3. Vyberte **správy aplikací**.

4. Vyberte **uživatelé** v **uživatelé a skupiny uživatelů** panelu.

5. Vyberte **Přidat**.

6. V **uživatelské jméno** zadejte *brittasimon@yourcompanydomain.extension*. 
    Příklad: BrittaSimon@contoso.com.

7. V **jméno a příjmení** zadejte úplný název, jako je **BrittaSimon**.

8. Vyberte **žádné heslo (použití jednotného přihlašování)** možnost.

9. V **e-mailová adresa** zadejte *yourname@yourcompanydomain.extension*. 
   Příklad: BrittaSimon@contoso.com.

10. Posuňte se dolů konec stránky a vyberte **Dokončit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Při výběru dlaždice iProva na přístupovém panelu, můžete by měl být automaticky přihlášeni k iProva, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [iProva – konfigurace typu SAML2 jednotného přihlašování](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)
