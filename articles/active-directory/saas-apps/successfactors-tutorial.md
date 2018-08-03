---
title: 'Kurz: Integrace Azure Active Directory se službou SuccessFactors | Dokumentace Microsoftu'
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
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 870a753a8f10255a602616ab54234b295f4d6e13
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431479"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Kurz: Integrace Azure Active Directory se službou SuccessFactors

V tomto kurzu se dozvíte, jak integrovat SuccessFactors s Azure Active Directory (Azure AD).

SuccessFactors integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SuccessFactors.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SuccessFactors (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SuccessFactors, potřebujete následující položky:

- S předplatným služby Azure AD
- SuccessFactors jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SuccessFactors z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-successfactors-from-the-gallery"></a>Přidání SuccessFactors z Galerie
Konfigurace integrace SuccessFactors do služby Azure AD, budete muset přidat SuccessFactors z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SuccessFactors z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **SuccessFactors**vyberte **SuccessFactors** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SuccessFactors v seznamu výsledků](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí SuccessFactors podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SuccessFactors je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SuccessFactors potřeba navázat.

V SuccessFactors, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SuccessFactors, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele SuccessFactors](#create-a-successfactors-test-user)**  – Pokud chcete mít protějšek Britta Simon SuccessFactors, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SuccessFactors.

**Ke konfiguraci Azure AD jednotné přihlašování s SuccessFactors, proveďte následující kroky:**

1. Na webu Azure Portal na **SuccessFactors** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/successfactors-tutorial/tutorial_successfactors_samlbase.png)

1. Na **SuccessFactors domény a adresy URL** části, proveďte následující kroky:

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
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory SuccessFactors klienta](https://www.successfactors.com/en_us/support.html) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/successfactors-tutorial/tutorial_general_400.png)
    
1. Na **SuccessFactors konfigurace** klikněte na tlačítko **nakonfigurovat SuccessFactors** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/successfactors-tutorial/tutorial_successfactors_configure.png) 

1. V okně jiné webové prohlížeče, přihlaste se k vaší **portál pro správu SuccessFactors** jako správce.
    
1. Navštivte **zabezpečení aplikací** a nativní pro **jednotné přihlašování na funkci**. 

1. Umístit libovolné hodnotě v **obnovit Token** a klikněte na tlačítko **uložit Token** pro povolení jednotného přihlašování SAML.
   
    ![Konfigurace jednotného přihlašování na straně aplikace][11]

    > [!NOTE] 
    > Tato hodnota se používá jako přepínač zapnuto/vypnuto. Pokud je libovolná hodnota uložen, jednotné přihlašování SAML nastavená na ON. Pokud je uložen prázdnou hodnotu jednotného přihlašování SAML je vypnuto.

1. Nativní pro následující snímek obrazovky a proveďte následující akce:
   
    ![Konfigurace jednotného přihlašování na straně aplikace][12]
   
    a. Vyberte **SAML v2 SSO** přepínač
   
    b. Nastavte **název uplatnění strany SAML**(například SAML vystavitele + název společnosti).
   
    c. V **URL vystavitele** vložit do textového pole **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure portal.
   
    d. Vyberte **odpovědi (zákazník generované/IdP/Asie a Tichomoří)** jako **vyžadují povinné podpis**.
   
    e. Vyberte **povolené** jako **povolit příznak SAML**.
   
    f. Vyberte **ne** jako **podpisu požadavku přihlášení (SF generované/SP/RP)**.
   
    g. Vyberte **profilu prohlížeče nebo Pozálohovacího** jako **profilu SAML**.
   
    h. Vyberte **ne** jako **vynutit období platný certifikát**.
   
    i. Zkopírujte obsah souboru staženého certifikátu z webu Azure portal a vložte jej do **ověření certifikátu SAML** textového pole.

    > [!NOTE] 
    > Obsah certifikátu musí začínat certifikátu a koncovou značkou certifikátu.

1. Přejděte na SAML V2 a pak proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování na straně aplikace][13]
   
    a. Vyberte **Ano** jako **podporují iniciovaného Zprostředkovatelem přihlašování globální odhlášení**.
   
    b. V **globální odhlašovací adresa URL služby (LogoutRequest cíl)** vložit do textového pole **odhlašování URL** hodnotu, která jste zkopírovali formuláře na webu Azure portal.
   
    c. Vyberte **ne** jako **vyžadují sp musíte používat šifrování všech element NameID**.
   
    d. Vyberte **neurčené** jako **formátem**.
   
    e. Vyberte **Ano** jako **povolit sp iniciované přihlášení (AuthnRequest)**.
   
    f. V **požadavků na odeslání jako vystavitel pořádaného microsoftem** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure portal.

1. Tyto kroky provést, pokud chcete, aby uživatelská jména přihlášení malá a velká písmena.
   
    ![Konfigurace jednotného přihlašování][29]
    
    a. Navštivte **nastavení společnosti**(u dolního).
   
    b. Zaškrtněte políčko vedle **povolit uživatelské jméno bez písmen**.
   
    c.Click **Uložit**.
   
    > [!NOTE] 
    > Pokud se pokusíte tuto možnost povolte, systém ověří, pokud vytvoří duplicitní název přihlašovacího SAML. Například pokud má zákazník uživatelských jmen uživatel1 tak pro uživatele user1. Trvá daleko rozlišování velikosti písmen díky tyto duplikáty. Systém zobrazí chybovou zprávu a neumožňuje tuto funkci. Zákazník musí změňte jednu z uživatelská jména, proto je napsaný různé.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/successfactors-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/successfactors-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/successfactors-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/successfactors-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-successfactors-test-user"></a>Vytvoření zkušebního uživatele SuccessFactors

Přihlaste se k SuccessFactors Azure AD uživatelům umožnit, musí být poskytnuty do SuccessFactors.  
V případě SuccessFactors zřizování se ruční úlohy.

Chcete-li získat uživatelé vytvoření ve SuccessFactors, budete muset požádat [tým podpory SuccessFactors](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k SuccessFactors použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit SuccessFactors Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **SuccessFactors**.

    ![Odkaz SuccessFactors v seznamu aplikací](./media/successfactors-tutorial/tutorial_successfactors_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SuccessFactors na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SuccessFactors.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/successfactors-tutorial/tutorial_general_01.png
[2]: ./media/successfactors-tutorial/tutorial_general_02.png
[3]: ./media/successfactors-tutorial/tutorial_general_03.png
[4]: ./media/successfactors-tutorial/tutorial_general_04.png

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/successfactors-tutorial/tutorial_general_05.png
[15]: ./media/successfactors-tutorial/tutorial_general_06.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/successfactors-tutorial/tutorial_general_100.png

[200]: ./media/successfactors-tutorial/tutorial_general_200.png
[201]: ./media/successfactors-tutorial/tutorial_general_201.png
[202]: ./media/successfactors-tutorial/tutorial_general_202.png
[203]: ./media/successfactors-tutorial/tutorial_general_203.png

