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
ms.openlocfilehash: ba3b0e06630665082b62e070dac64e8bc572f6dc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811702"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Kurz: Integrace Azure Active Directory s iProva

V tomto kurzu se dozvíte, jak integrovat iProva s Azure Active Directory (Azure AD).
IProva integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k iProva.
* Můžete povolit uživatelům být automaticky přihlášeni k iProva (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iProva, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* iProva jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* podporuje iProva **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-iprova-from-the-gallery"></a>Přidání iProva z Galerie

Konfigurace integrace iProva do služby Azure AD, budete muset přidat iProva z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat iProva z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **iProva**vyberte **iProva** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![iProva v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí iProva podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v iProva.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iProva, které potřebujete k dokončení následujících stavebních bloků:

1. **[Načítání informací o konfiguraci z iProva](#retrieve-configuration-information-from-iprova)**  – jako příprava pro další kroky.
2. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
3. **[Konfigurovat Single Sign-On iProva](#configure-iprova-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
4. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
5. **[Vytvořit testovacího uživatele iProva](#create-iprova-test-user)**  – Pokud chcete mít protějšek Britta Simon v iProva, který je propojený s Azure AD reprezentace uživatele.
6. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
7. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="retrieve-configuration-information-from-iprova"></a>Načítání informací o konfiguraci z iProva

V této části se budou načítat z iProva některé důležité informace.
Tyto informace budete potřebovat ke konfiguraci Azure AD jednotného přihlašování

1. Otevřete webový prohlížeč a přejděte **stránku s informacemi o typu SAML2** v iProva, využívají následující vzor adresy URL:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![Zobrazit informační stránku iProva SAML2](media/iprova-tutorial/iprova-saml2-info.png)

2. Na kartě prohlížeče ponechte otevřené, když budete pokračovat s dalšími kroky na nové kartě prohlížeče.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s iProva, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **iProva** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. Zadejte **identifikátor** pole s hodnotou, která se zobrazí za popisek **EntityID** na **stránku s informacemi o iProva SAML2** (stále otevřen v druhé záložce prohlížeče).

    b. Zadejte **adresy URL odpovědi** pole s hodnotou, která se zobrazí za popisek **adresy URL odpovědi** na **stránku s informacemi o iProva SAML2** (stále otevřen v druhé záložce prohlížeče).

    c. Zadejte **přihlašovací adresa URL** pole s hodnotou, která se zobrazí za popisek **přihlašovací adresa URL** na **stránku s informacemi o iProva SAML2** (stále otevřen v druhé záložce prohlížeče).

    ![iProva domény a adresy URL jednotné přihlašování – informace](common/sp-identifier-reply.png)

5. iProva aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název | Zdrojový atribut| Obor názvů |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **Namespace** seznamu, zadejte hodnotu oboru názvů, který je zobrazený pro tento řádek.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Konfigurace iProva jednotného přihlašování

1. Přihlaste se k použití iProva **správce** účtu.

2. Otevřít **přejít na** nabídky.

3. Klikněte na **správy aplikací**.

4. Klikněte na **Obecné** v **nastavení systému** panelu.

5. Klikněte na **upravit**.

6. Přejděte dolů k položce **řízení přístupu**.

    ![nastavení řízení přístupu iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Najít nastavení **automaticky přihlášeni uživatelé pomocí svých účtů sítě**a změňte ho na **Ano, ověřování pomocí SAML**. Další možnosti se teď budou zobrazovat.

8. Klikněte na tlačítko **nastavení** tlačítko.

9. Klikněte na tlačítko **Další** tlačítko.

10. iProva nyní požádá zprávu, pokud chcete stáhnout federace data z adresy URL, nebo nahrát ze souboru. Vyberte **z adresy URL** možnost.

    ![Stáhnout metadata služby Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Nyní vložte adresu URL metadat, které jste si uložili v posledním kroku **konfiguraci Azure AD jednotného přihlašování** kapitoly.

12. Klikněte na tlačítko ve tvaru šipka se stáhnout metadata ze služby Azure AD.

13. Po dokončení stahování, potvrzovací zpráva **platná Data federace soubor stažený** se zobrazí.

14. Klikněte na tlačítko **Další** tlačítko.

15. Přeskočit **Test přihlášení** teď možnost a klikněte na tlačítko **Další** tlačítko.

16. V rozevíracím seznamu s názvem **deklaraci identity pro použití**vyberte **windowsaccountname**.

17. Klikněte na tlačítko **Dokončit**.

18. Teď vrátit **upravit obecná nastavení** obrazovky. **Posuňte se dolů** do dolní části stránky a kliknutím **OK** tlačítko uložte konfiguraci.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** pole zadejte název, například **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **yourname@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k iProva.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **iProva**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **iProva**.

    ![Odkaz iProva v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-iprova-test-user"></a>Vytvoření iProva testovacího uživatele

1. Přihlaste se k použití iProva **správce** účtu.

2. Otevřít **přejít na** nabídky.

3. Klikněte na **správy aplikací**.

4. Klikněte na **uživatelé** v **uživatelé a skupiny uživatelů** panelu.

5. Klikněte na tlačítko **Přidat**.

6. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

7. V **jméno a příjmení** pole zadejte jméno a příjmení jako **BrittaSimon**.

8. Vyberte **žádné heslo (použití jednotného přihlašování)** možnost.

9. V **e-mailová adresa** typ pole **yourname@yourcompanydomain.extension** například BrittaSimon@contoso.com

10. Posuňte se dolů konec stránky a klikněte na tlačítko **Dokončit** tlačítko.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici iProva na přístupovém panelu, můžete by měl být automaticky přihlášeni k iProva, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [iProva – konfigurace typu SAML2 jednotného přihlašování](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)