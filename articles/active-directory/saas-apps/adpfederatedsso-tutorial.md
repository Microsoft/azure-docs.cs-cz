---
title: 'Kurz: Integrace Azure Active Directory s ADP | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.openlocfilehash: 981f4d78a351b6d2b61253407e5021dbeb285204
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819539"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Kurz: Integrace Azure Active Directory s ADP

V tomto kurzu se dozvíte, jak integrovat ADP s Azure Active Directory (Azure AD).
ADP integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ADP.
* Můžete povolit uživatelům být automaticky přihlášeni k ADP (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ADP, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* ADP jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje ADP **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-adp-from-the-gallery"></a>Přidání ADP z Galerie

Konfigurace integrace ADP do služby Azure AD, budete muset přidat ADP z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ADP z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ADP**vyberte **ADP** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![ADP v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s ADP podle testu uživateli **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ADP.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ADP, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace ADP Single Sign-On](#configure-adp-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele ADP](#create-adp-test-user)**  – Pokud chcete mít protějšek Britta Simon ADP, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ADP, proveďte následující kroky:

1. Na webu Azure Portal na **ADP** stránky integrace aplikace, klikněte na **karta Vlastnosti** a proveďte následující kroky: 

    ![Vlastnosti jednotného přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Nastavte **mohou se uživatelé přihlásit** hodnota pro pole **Ano**.

    b. Kopírování **adresa URL portálu User access** a vložte ji **část konfigurovat přihlašovací adresa URL**, což je vysvětleno dále v tomto kurzu.

    c. Nastavte **přiřazení uživatelů povinné** hodnota pro pole **Ano**.

    d. Nastavte **viditelné pro uživatele** hodnota pro pole **ne**.

2. V [webu Azure portal](https://portal.azure.com/)na **ADP** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![ADP domény a adresy URL jednotného přihlašování – informace](common/idp-identifier.png)

    V **identifikátor (Entity ID)** textové pole, zadejte adresu URL:  `https://fed.adp.com`

6. ADP aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna. Název deklarace bude vždy **"PersonImmutableID"** a hodnotu z nich jsme zjištěno, že na mapu s **employeeid**. 

    Mapování uživatelů ze služby Azure AD k ADP se provede na **employeeid** , ale to můžete namapovat na jinou hodnotu na základě svého nastavení aplikace. Proto prosím práci s [tým podpory ADP](https://www.adp.com/contact-us/overview.aspx) nejprve k použijte správný identifikátor uživatele a mapování danou hodnotu s **"PersonImmutableID"** deklarací identity.

    ![image](common/edit-attribute.png)

7. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název | Zdrojový atribut | 
    | ---------------| --------------- |
    | PersonImmutableID  | user.employeeid |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

    > [!NOTE] 
    > Než budete moct nakonfigurovat kontrolní výraz SAML, budete muset požádat vašeho [tým podpory ADP](https://www.adp.com/contact-us/overview.aspx) a žádat o hodnotu identifikátor atributu jedinečného uživatele pro vašeho tenanta. Ke konfiguraci vlastních deklarací identity pro vaše aplikace tuto hodnotu budete potřebovat. 

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>Konfigurace ADP jednotné přihlašování

Ke konfiguraci jednotného přihlašování na **ADP** straně, budete muset nahrát na stažený **soubor XML s metadaty** na [ADP webu](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Tento proces může trvat několik dní.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurace služby ADP pro federovaný přístup

>[!Important]
> Vaši zaměstnanci, kteří požadují federovaný přístup k vašim službám ADP musí být přiřazena ADP služby App Service a následně, uživatelé musí přiřadit ke konkrétní službě ADP.
Přijetí potvrzení od zástupce ADP nakonfigurujte pro řízení přístupu uživatele do konkrétní služby ADP ADP služeb a přiřazovat a spravovat uživatele.

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ADP**vyberte **ADP** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![ADP v seznamu výsledků](common/search-new-app.png)

5. Na webu Azure Portal na vaší **ADP** stránky integrace aplikace, klikněte na **karta Vlastnosti** a proveďte následující kroky:  

    ![Linkedproperties jednotné přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Nastavte **mohou se uživatelé přihlásit** hodnota pro pole **Ano**.

    b.  Nastavte **přiřazení uživatelů povinné** hodnota pro pole **Ano**.

    c.  Nastavte **viditelné pro uživatele** hodnota pro pole **Ano**.

6. V [webu Azure portal](https://portal.azure.com/)na **ADP** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

7. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **režimu** jako **propojené**. propojit aplikaci **ADP**.

    ![Jednotné přihlašování propojené](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Přejděte **konfigurovat přihlašovací adresa URL** části, proveďte následující kroky:

    ![Prop jednotné přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Vložit **adresa URL portálu User access**, který jste zkopírovali z výše **karta Vlastnosti** (z hlavní ADP aplikace).
                                                             
    b. Následují 5 aplikace, které podporují různé **adresy URL stavu Relay**. Je nutné přidat odpovídající **Relay stavu URL** ručně na hodnotu pro konkrétní aplikaci **adresa URL portálu User access**.
    
    * **Pracovníci ADP nyní**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **Pracovníci ADP teď Vylepšená doba**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Uložit** provedené změny.

10. Přijetí potvrzení od zástupce ADP začněte test s uživateli jednu nebo dvě.

    a. Přiřadíte několik uživatelů ve službě ADP aplikaci otestovat federovaný přístup.

    b. Test je úspěšný, když uživatelé přístup k app service ADP v galerii a jejich ADP službě se můžete dostat.
 
11. Na potvrzení test proběhl úspěšně přiřaďte federované ADP služby pro jednotlivé uživatele nebo skupiny uživatelů, které je vysvětlen později v tomto kurzu a zavedení na svoje zaměstnance.
 
### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k ADP použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ADP**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **ADP**.

    ![Odkaz ADP v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-adp-test-user"></a>Vytvoření ADP testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v ADP. Práce s [tým podpory ADP](https://www.adp.com/contact-us/overview.aspx) k přidání uživatelů v účtu ADP. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ADP na přístupovém panelu, můžete by měl být automaticky přihlášeni k ADP, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

