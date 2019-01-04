---
title: 'Kurz: Integrace Azure Active Directory s SuccessFactors | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 467db4046c0600142338dcfa39e136f45255caba
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976745"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Kurz: Integrace Azure Active Directory s SuccessFactors

V tomto kurzu se dozvíte, jak integrovat SuccessFactors s Azure Active Directory (Azure AD).

SuccessFactors integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SuccessFactors.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SuccessFactors (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SuccessFactors, potřebujete následující položky:

- Předplatné Azure AD
- SuccessFactors jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SuccessFactors z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-successfactors-from-the-gallery"></a>Přidání SuccessFactors z Galerie

Konfigurace integrace SuccessFactors do služby Azure AD, budete muset přidat SuccessFactors z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SuccessFactors z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SuccessFactors**vyberte **SuccessFactors** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SuccessFactors v seznamu výsledků](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí SuccessFactors podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SuccessFactors je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SuccessFactors potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SuccessFactors, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SuccessFactors](#creating-a-successfactors-test-user)**  – Pokud chcete mít protějšek Britta Simon SuccessFactors, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SuccessFactors.

**Ke konfiguraci Azure AD jednotné přihlašování s SuccessFactors, proveďte následující kroky:**

1. Na webu Azure Portal na **SuccessFactors** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![SuccessFactors domény a adresy URL jednotného přihlašování – informace](./media/successfactors-tutorial/tutorial_successfactors_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory SuccessFactors klienta](https://www.successfactors.com/content/ssf-site/en/support.html) k získání těchto hodnot. 

5. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

6. Na **nastavení SuccessFactors** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace SuccessFactors](common/configuresection.png)

7. V okně jiné webové prohlížeče, přihlaste se k vaší **portál pro správu SuccessFactors** jako správce.
    
8. Navštivte **zabezpečení aplikací** a nativní pro **jednotné přihlašování na funkci**. 

9. Umístit libovolné hodnotě v **obnovit Token** a klikněte na tlačítko **uložit Token** pro povolení jednotného přihlašování SAML.
   
    ![Konfigurace jednotného přihlašování na straně aplikace][11]

    > [!NOTE] 
    > Tato hodnota se používá jako přepínač zapnuto/vypnuto. Pokud je libovolná hodnota uložen, jednotné přihlašování SAML nastavená na ON. Pokud je uložen prázdnou hodnotu jednotného přihlašování SAML je vypnuto.

10. Nativní pro následující snímek obrazovky a proveďte následující akce:
   
    ![Konfigurace jednotného přihlašování na straně aplikace][12]
   
    a. Vyberte **SAML v2 SSO** přepínač
   
    b. Nastavte **název uplatnění strany SAML**(například SAML vystavitele + název společnosti).
   
    c. V **URL vystavitele** vložit do textového pole **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.
   
    d. Vyberte **kontrolní výraz** jako **vyžadují povinné podpis**.
   
    e. Vyberte **povolené** jako **povolit příznak SAML**.
   
    f. Vyberte **ne** jako **podpisu požadavku přihlášení (SF generované/SP/RP)**.
   
    g. Vyberte **profilu prohlížeče nebo Pozálohovacího** jako **profilu SAML**.
   
    h. Vyberte **ne** jako **vynutit období platný certifikát**.
   
    i. Zkopírujte obsah souboru staženého certifikátu z webu Azure portal a vložte jej do **ověření certifikátu SAML** textového pole.

    > [!NOTE] 
    > Obsah certifikátu musí začínat certifikátu a koncovou značkou certifikátu.

11. Přejděte na SAML V2 a pak proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování na straně aplikace][13]
   
    a. Vyberte **Ano** jako **podporují iniciovaného Zprostředkovatelem přihlašování globální odhlášení**.
   
    b. V **globální odhlašovací adresa URL služby (LogoutRequest cíl)** vložit do textového pole **odhlašování URL** hodnotu, která jste zkopírovali formuláře na webu Azure portal.
   
    c. Vyberte **ne** jako **vyžadují sp musíte používat šifrování všech element NameID**.
   
    d. Vyberte **neurčené** jako **formátem**.
   
    e. Vyberte **Ano** jako **povolit sp iniciované přihlášení (AuthnRequest)**.
   
    f. V **požadavků na odeslání jako vystavitel pořádaného microsoftem** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure portal.

12. Tyto kroky provést, pokud chcete, aby uživatelská jména přihlášení malá a velká písmena.
   
    ![Konfigurace jednotného přihlašování][29]
    
    a. Navštivte **nastavení společnosti**(u dolního).
   
    b. Zaškrtněte políčko vedle **povolit uživatelské jméno bez písmen**.
   
    c.Click **Uložit**.
   
    > [!NOTE] 
    > Pokud se pokusíte tuto možnost povolte, systém ověří, pokud vytvoří duplicitní název přihlašovacího SAML. Například pokud má zákazník uživatelských jmen uživatel1 tak pro uživatele user1. Trvá daleko rozlišování velikosti písmen díky tyto duplikáty. Systém zobrazí chybovou zprávu a neumožňuje tuto funkci. Zákazník musí změňte jednu z uživatelská jména, proto je napsaný různé.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-a-successfactors-test-user"></a>Vytvoření zkušebního uživatele SuccessFactors

Přihlaste se k SuccessFactors Azure AD uživatelům umožnit, musí být poskytnuty do SuccessFactors.  
V případě SuccessFactors zřizování se ruční úlohy.

Chcete-li získat uživatelé vytvoření ve SuccessFactors, budete muset požádat [tým podpory SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k SuccessFactors použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **SuccessFactors**.

    ![Konfigurace jednotného přihlašování](./media/successfactors-tutorial/tutorial_successfactors_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SuccessFactors na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SuccessFactors.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
