---
title: 'Kurz: Integrace Azure Active Directory se službou Work.com | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: f4247a24905b5865635495774412237118e3372a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427359"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Kurz: Integrace Azure Active Directory se službou Work.com

V tomto kurzu se dozvíte, jak integrovat Work.com s Azure Active Directory (Azure AD).

Work.com integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Work.com
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Work.com (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Work.com, potřebujete následující položky:

- S předplatným služby Azure AD
- Work.com jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Work.com z Galerie
1. Konfigurace a otestování služby Azure AD jednotného přihlašování

## <a name="add-workcom-from-the-gallery"></a>Přidání Work.com z Galerie
Konfigurace integrace Work.com do služby Azure AD, budete muset přidat Work.com z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Work.com z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Work.com**vyberte **Work.com** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Přidat z Galerie](./media/work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Work.com podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Work.com je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Work.com potřeba navázat.

V Work.com, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Work.com, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Work.com](#create-a-workcom-test-user)**  – Pokud chcete mít protějšek Britta Simon Work.com, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Work.com.

>[!NOTE]
>Pokud chcete nakonfigurovat jednotné přihlašování, budete muset nastavit vlastní název domény Work.com ještě. Musíte definovat alespoň názvu domény, test název domény a nasazení pro celou organizaci.

**Ke konfiguraci Azure AD jednotné přihlašování s Work.com, proveďte následující kroky:**

1. Na webu Azure Portal na **Work.com** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Přihlašování založené na SAML](./media/work-com-tutorial/tutorial_work-com_samlbase.png)

1. Na **Work.com domény a adresy URL** části, proveďte následující kroky:

    ![Část Work.com domény a adresy URL](./media/work-com-tutorial/tutorial_work-com_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Work.com klienta](https://help.salesforce.com/articleView?id=000159855&type=3) tuto výhodu získáte. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Části podpisový certifikát SAML](./media/work-com-tutorial/tutorial_work-com_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/work-com-tutorial/tutorial_general_400.png)

1. Na **Work.com konfigurace** klikněte na tlačítko **nakonfigurovat Work.com** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Oddíl konfigurace Work.com](./media/work-com-tutorial/tutorial_work-com_configure.png) 
1. Přihlaste se k tenantovi Work.com jako správce.

1. Přejděte na **nastavení**.
   
    ![Instalační program](./media/work-com-tutorial/ic794108.png "instalační program")

1. V levém navigačním podokně v **spravovat** klikněte na tlačítko **Správa domén** související rozbalíte, a pak klikněte na **Moje doména** otevřete **Moje doména** stránky. 
   
    ![Moje doména](./media/work-com-tutorial/ic767825.png "Moje doména")

1. Pokud chcete ověřit, správně nastavené vaší domény, ujistěte se, že je v "**kroku 4 nasazena u uživatelů**" a zkontrolovat vaše "**má nastavení domény**".
   
    ![Doména uživatele nasazena](./media/work-com-tutorial/ic784377.png "nasadit na uživatele domény")

1. Přihlaste se k vašemu tenantovi Work.com.

1. Přejděte na **nastavení**.
    
    ![Instalační program](./media/work-com-tutorial/ic794108.png "instalační program")

1. Rozbalte **kontrolních mechanismů pro zabezpečení** nabídky a pak klikněte na tlačítko **nastavení jednotného přihlašování**.
    
    ![Jednotné přihlašování – nastavení](./media/work-com-tutorial/ic794113.png "jednotné přihlašování – nastavení")

1. Na **nastavení jednotného přihlašování** dialogového okna stránky, proveďte následující kroky:
    
    ![Povoleno SAML](./media/work-com-tutorial/ic781026.png "povoleno SAML")
    
    a. Vyberte **povoleno SAML**.
    
    b. Klikněte na možnost **Nové**.

1. V **SAML jednotné přihlašování – nastavení** části, proveďte následující kroky:
    
    ![SAML jednotné přihlašování – nastavení](./media/work-com-tutorial/ic794114.png "SAML jednotné přihlašování – nastavení")
    
    a. V **název** textového pole zadejte název pro vaši konfiguraci.  
       
    > [!NOTE]
    > Zadání hodnoty pro **název** se automaticky vyplní **název rozhraní API** textového pole.
    
    b. V **vystavitele** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.
    
    c. Chcete-li nahrát na server certifikát stažený z webu Azure portal, klikněte na tlačítko **Procházet**.
    
    d. V **Entity Id** textové pole, typ `https://salesforce-work.com`.
    
    e. Jako **typ Identity SAML**vyberte **kontrolní výraz obsahuje ID federace z objektu uživatele**.
    
    f. Jako **umístění Identity SAML**vyberte **identita je v elementu NameIdentfier příkazu subjektu**.
    
    g. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    h. V **odhlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.
    
    i. Jako **poskytovatele inicioval žádost o vazbu služby**vyberte **HTTP Post**.
    
    j. Klikněte na **Uložit**.

1. Na portálu classic Work.com, v levém navigačním podokně klikněte na tlačítko **Správa domén** související rozbalíte, a pak klikněte na **Moje doména** otevřít **Moje doména** stránka. 
    
    ![Moje doména](./media/work-com-tutorial/ic794115.png "Moje doména")

1. Na **Moje doména** stránku, **Branding přihlašovací stránky** klikněte na tlačítko **upravit**.
    
    ![Branding přihlašovací stránky](./media/work-com-tutorial/ic767826.png "Branding přihlašovací stránky")

1. Na **Branding přihlašovací stránky** stránku, **ověřovací službu** části název vaší **nastavení jednotného přihlašování SAML** se zobrazí. Vyberte ho a pak klikněte na tlačítko **Uložit**.
    
    ![Branding přihlašovací stránky](./media/work-com-tutorial/ic784366.png "Branding přihlašovací stránky")

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/work-com-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Všichni uživatelé -> uživatele a skupiny](./media/work-com-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Přidat](./media/work-com-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Dialogové okno stránky uživatele](./media/work-com-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-workcom-test-user"></a>Vytvoření zkušebního uživatele Work.com
Pro uživatele Azure Active Directory bude moct přihlásit musí být poskytnuty k Work.com. V případě Work.com zřizování se ruční úlohy.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurace zřizování uživatelů, proveďte následující kroky:
1. Přihlaste se k webu společnosti Work.com jako správce.

1. Přejděte na **nastavení**.
   
    ![Instalační program](./media/work-com-tutorial/IC794108.png "instalační program")
1. Přejděte na **spravovat uživatele \> uživatelé**.
   
    ![Správa uživatelů](./media/work-com-tutorial/IC784369.png "Správa uživatelů")

1. Klikněte na tlačítko **nového uživatele**.
   
    ![Všichni uživatelé](./media/work-com-tutorial/IC794117.png "všichni uživatelé")

1. V části uživatel upravovat provedete následující kroky v atributech platný Azure AD účet ke zřízení do související textová pole:
   
    ![Úprava uživatele](./media/work-com-tutorial/ic794118.png "Úprava uživatele")
   
    a. V **křestní jméno** textové pole, typ **křestní jméno** uživatele **Britta**.
    
    b. V **příjmení** textové pole, typ **příjmení** uživatele **Simon**.
    
    c. V **Alias** textové pole, typ **název** uživatele **BrittaS**.
    
    d. V **e-mailu** textové pole, typ **e-mailová adresa** uživatele **Brittasimon@contoso.com**.
    
    e. V **uživatelské jméno** textové pole, typ uživatelskému jménu uživatele jako **Brittasimon@contoso.com**.
    
    f. V **Přezdívka** textového pole zadejte **Přezdívka** uživatele **Simon**.
    
    g. Vyberte **Role**, **uživatelskou licenci**, a **profilu**.
    
    h. Klikněte na **Uložit**.  
      
    > [!NOTE]
    > Držitel účtu Azure AD se zobrazí e-mailu včetně odkaz pro potvrzení účtu, pak se změní na aktivní.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Work.com použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Work.com Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Work.com**.

    ![Work.com v seznamu aplikací.](./media/work-com-tutorial/tutorial_work-com_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Work.com na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Work.com.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/work-com-tutorial/tutorial_general_01.png
[2]: ./media/work-com-tutorial/tutorial_general_02.png
[3]: ./media/work-com-tutorial/tutorial_general_03.png
[4]: ./media/work-com-tutorial/tutorial_general_04.png

[100]: ./media/work-com-tutorial/tutorial_general_100.png

[200]: ./media/work-com-tutorial/tutorial_general_200.png
[201]: ./media/work-com-tutorial/tutorial_general_201.png
[202]: ./media/work-com-tutorial/tutorial_general_202.png
[203]: ./media/work-com-tutorial/tutorial_general_203.png

