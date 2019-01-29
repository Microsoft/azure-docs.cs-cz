---
title: 'Kurz: Integrace Azure Active Directory s ADP Globalview | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ADP Globalview.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: f5d11dd1ed8e23414eed5c9d7cf5c248b58aaa8a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163717"
---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>Kurz: Integrace Azure Active Directory s ADP Globalview

V tomto kurzu se dozvíte, jak integrovat ADP Globalview s Azure Active Directory (Azure AD).

ADP Globalview integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ADP Globalview
- Uživatele, aby automaticky získat přihlášení k ADP Globalview (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ADP Globalview, potřebujete následující položky:

- Předplatné Azure AD
- ADP Globalview jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ADP Globalview z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-adp-globalview-from-the-gallery"></a>Přidání ADP Globalview z Galerie
Konfigurace integrace ADP Globalview do služby Azure AD, budete muset přidat ADP Globalview z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ADP Globalview z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **ADP Globalview**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/adglobalview-tutorial/tutorial_adpglobalview_search.png)

5. Na panelu výsledků vyberte **ADP Globalview**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/adglobalview-tutorial/tutorial_adpglobalview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a test Azure AD jednotné přihlašování s ADP Globalview podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ADP Globalview je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ADP Globalview potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v ADP Globalview.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ADP Globalview, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváří se testovací uživatelské jméno ADP Globalview](#creating-an-adp-globalview-test-user)**  – Pokud chcete mít protějšek Britta Simon ADP Globalview, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ADP Globalview.

**Ke konfiguraci Azure AD jednotné přihlašování s ADP Globalview, proveďte následující kroky:**

1. Na webu Azure Portal na **ADP Globalview** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_samlbase.png)

3. Na **ADP Globalview domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_url.png)

     V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.globalview.adp.com/federate` nebo `https://<subdomain>.globalview.adp.com/federate2`

    > [!NOTE] 
    > Hodnota není skutečný. Identifikátor skutečné zaktualizujte příslušnou hodnotu. Kontakt [ADP Globalview podporu](https://www.adp.com/contact-us/overview.aspx) má být získána hodnota.
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_certificate.png) 

5. Aplikace ADP GlobalView očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. 

6. Následující snímek obrazovky ukazuje příklad pro něj. Názvy deklaraci identity vždy být **"PersonImmutableID"** a hodnoty, které jsme namapované na ExtensionAttribute2, který obsahuje EmployeeID uživatele. Tady se provádí mapování uživatelů ze služby Azure AD k ADP GlobalView na EmployeeID, ale můžete ho namapovat na jinou hodnotu také v závislosti na nastavení aplikace. Můžete spolupracovat s týmem ADP GlobalView nejprve k použijte správný identifikátor uživatele a mapování danou hodnotu s **"PersonImmutableID"** deklarací identity. Můžete také namapovat deklarace identity e-mailu a ID uživatele, jak je znázorněno na obrázku.

    ![Konfigurace jednotného přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_attribute.png)

7. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | personalimmutableid | user.extensionattribute2 |
    | e-mail               | user.mail |
    | ID uživatele              | user.userprincipalname|
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/adglobalview-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/adglobalview-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

    > [!NOTE] 
    > Než budete moct nakonfigurovat kontrolní výraz SAML, budete muset požádat vašeho [ADP Globalview podporu](https://www.adp.com/contact-us/overview.aspx) a žádat o hodnotu atributu jedinečného identifikátoru pro vašeho tenanta. Ke konfiguraci vlastních deklarací identity pro vaše aplikace tuto hodnotu budete potřebovat. 

8. Na **ADP Globalview konfigurace** klikněte na tlačítko **konfigurace ADP Globalview** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_configure.png) 

9. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/adglobalview-tutorial/tutorial_general_400.png)

10. Ke konfiguraci jednotného přihlašování na **ADP Globalview** straně, je nutné odeslat na stažený **certifikát (Base64)**, **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** k [ADP Globalview podporu](https://www.adp.com/contact-us/overview.aspx).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/adglobalview-tutorial/create_aaduser_01.png) 

2.  Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/adglobalview-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/adglobalview-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/adglobalview-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-adp-globalview-test-user"></a>Vytvoření ADP Globalview testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v ADP GlobalView. Práce s [ADP Globalview podporu](https://www.adp.com/contact-us/overview.aspx) k přidání uživatelů v účtu ADP GlobalView. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k ADP Globalview.

![Přiřadit uživatele][200] 

**Přiřadit ADP Globalview Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ADP Globalview**.

    ![Konfigurace jednotného přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.  

Po kliknutí na dlaždici ADP GlobalView na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci ADP GlobalView.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adglobalview-tutorial/tutorial_general_01.png
[2]: ./media/adglobalview-tutorial/tutorial_general_02.png
[3]: ./media/adglobalview-tutorial/tutorial_general_03.png
[4]: ./media/adglobalview-tutorial/tutorial_general_04.png

[100]: ./media/adglobalview-tutorial/tutorial_general_100.png

[200]: ./media/adglobalview-tutorial/tutorial_general_200.png
[201]: ./media/adglobalview-tutorial/tutorial_general_201.png
[202]: ./media/adglobalview-tutorial/tutorial_general_202.png
[203]: ./media/adglobalview-tutorial/tutorial_general_203.png

