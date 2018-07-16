---
title: 'Kurz: Integrace Azure Active Directory se službou ADP | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: jeedes
ms.openlocfilehash: 75b84c2856373126ceba0fc536e41d270f4d2d05
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048775"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Kurz: Integrace Azure Active Directory se službou ADP

V tomto kurzu se dozvíte, jak integrovat ADP s Azure Active Directory (Azure AD).

ADP integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ADP.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ADP (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ADP, potřebujete následující položky:

- S předplatným služby Azure AD
- Předplatné ADP povoleno

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ADP z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-adp-from-the-gallery"></a>Přidání ADP z Galerie
Konfigurace integrace ADP do služby Azure AD, budete muset přidat ADP z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ADP z galerie, postupujte následovně:**

1.  Přihlaste se k prostředí vašeho poskytovatele identit Microsoft Azure jako správce.

2. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

3. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
4. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

5. Do vyhledávacího pole zadejte **ADP**vyberte **ADP** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![ADP v seznamu výsledků](./media/adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí ADP podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ADP je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ADP potřeba navázat.

V ADP, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ADP, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele ADP](#create-an-adp-test-user)**  – Pokud chcete mít protějšek Britta Simon ADP, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ADP.

**Ke konfiguraci Azure AD jednotné přihlašování s ADP, proveďte následující kroky:**

1. Na webu Azure Portal na **ADP** stránky integrace aplikace, klikněte na **karta Vlastnosti** a proveďte následující kroky: 

    ![Vlastnosti jednotného přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Nastavte **mohou se uživatelé přihlásit** hodnota pro pole **Ano**.

    b. Kopírování **adresa URL portálu User access** a vložte ji **část konfigurovat přihlašovací adresa URL**, což je vysvětleno dále v tomto kurzu.

    c. Nastavte **přiřazení uživatelů povinné** hodnota pro pole **Ano**.

    d. Nastavte **viditelné pro uživatele** hodnota pro pole **ne**.

2. Klikněte na tlačítko **jednotného přihlašování** na **ADP** stránky integrace aplikace.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

3. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. Na **ADP domény a adresy URL** části, proveďte následující kroky:

    ![ADP domény a adresy URL jednotného přihlašování – informace](./media/adpfederatedsso-tutorial/tutorial_adp_url.png)

    V **identifikátor** textového pole zadejte adresu URL: `https://fed.adp.com` 
    
5. Aplikace ADP očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto. Název deklarace bude vždy **"PersonImmutableID"** a jehož hodnota jsme namapované na **employeeid**. 

    Tady se provede mapování uživatelů ze služby Azure AD k ADP na **employeeid** , ale to můžete namapovat na jinou hodnotu na základě svého nastavení aplikace. Proto prosím práci s [tým podpory ADP](https://www.adp.com/contact-us/overview.aspx) nejprve k použijte správný identifikátor uživatele a mapování danou hodnotu s **"PersonImmutableID"** deklarací identity.

    ![Konfigurace jednotného přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | PersonImmutableID | User.EmployeeID |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

    > [!NOTE] 
    > Než budete moct nakonfigurovat kontrolní výraz SAML, budete muset požádat vašeho [tým podpory ADP](https://www.adp.com/contact-us/overview.aspx) a žádat o hodnotu atributu jedinečného identifikátoru pro vašeho tenanta. Ke konfiguraci vlastních deklarací identity pro vaše aplikace tuto hodnotu budete potřebovat. 

7. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. Ke konfiguraci jednotného přihlašování na **ADP** straně, budete muset nahrát na stažený **soubor XML s metadaty** na [ADP webu](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Tento proces může trvat několik dní. 

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurace služby ADP pro federovaný přístup

>[!Important]
> Vaši zaměstnanci, kteří požadují federovaný přístup k vašim službám ADP musí být přiřazena ADP služby App Service a následně, uživatelé musí přiřadit ke konkrétní službě ADP.
Přijetí potvrzení od zástupce ADP nakonfigurujte pro řízení přístupu uživatele do konkrétní služby ADP ADP služeb a přiřazovat a spravovat uživatele.

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ADP**vyberte **ADP** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![ADP v seznamu výsledků](./media/adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. Na webu Azure Portal na vaší **ADP** stránky integrace aplikace, klikněte na **karta Vlastnosti** a proveďte následující kroky:  

    ![Linkedproperties jednotné přihlašování](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Nastavte **mohou se uživatelé přihlásit** hodnota pro pole **Ano**.

    b.  Nastavte **přiřazení uživatelů povinné** hodnota pro pole **Ano**.

    c.  Nastavte **viditelné pro uživatele** hodnota pro pole **Ano**.

6. Klikněte na tlačítko **jednotného přihlašování** na **ADP** stránky integrace aplikace.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

7. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **propojené přihlášení**. propojit aplikaci **ADP**.

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

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/adpfederatedsso-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/adpfederatedsso-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/adpfederatedsso-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/adpfederatedsso-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-adp-test-user"></a>Vytvořit testovacího uživatele ADP

Cílem této části je vytvořte uživatele Britta Simon v ADP. Práce s [tým podpory ADP](https://www.adp.com/contact-us/overview.aspx) k přidání uživatelů v účtu ADP.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k ADP použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit ADP Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ADP**.

    ![Odkaz ADP v seznamu aplikací](./media/adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ADP na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci ADP.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/adpfederatedsso-tutorial/tutorial_general_203.png

