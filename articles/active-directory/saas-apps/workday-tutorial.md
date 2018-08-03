---
title: 'Kurz: Integrace Azure Active Directory s aplikací Workday | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: jeedes
ms.openlocfilehash: 65b103d9dd4a2d50d9d51aabb1728d759351a548
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420987"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Kurz: Integrace Azure Active Directory s aplikací Workday

V tomto kurzu se dozvíte, jak integrovat Workday s Azure Active Directory (Azure AD).

Workday integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Workday.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Workday (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s aplikací Workday, potřebujete následující položky:

- S předplatným služby Azure AD
- Workday jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Workday z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-workday-from-the-gallery"></a>Přidání Workday z Galerie
Pokud chcete nakonfigurovat integraci Workday do služby Azure AD, budete muset přidat Workday z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Workday z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Workday**vyberte **Workday** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![WORKDAY v seznamu výsledků](./media/workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s aplikací Workday podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek ve Workday je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve Workday.

Ve Workday, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s aplikací Workday, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Workday](#create-a-workday-test-user)**  – Pokud chcete mít protějšek Britta Simon ve Workday, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Workday.

**Ke konfiguraci Azure AD jednotné přihlašování s aplikací Workday, proveďte následující kroky:**

1. Na webu Azure Portal na **Workday** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/workday-tutorial/tutorial_workday_samlbase.png)

1. Na **Workday domény a adresy URL** části, proveďte následující kroky:

    ![WORKDAY domény a adresy URL jednotného přihlašování – informace](./media/workday-tutorial/tutorial_workday_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. V **identifikátor** textového pole zadejte adresu URL: `http://www.workday.com`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a postupujte následovně:

    ![WORKDAY domény a adresy URL jednotného přihlašování – informace](./media/workday-tutorial/tutorial_workday_url1.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://impl.workday.com/<tenant>/login-saml.htmld`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a adresy URL odpovědi. Vaše adresa URL odpovědi musí mít například subdomény: www wd2, wd3, wd3 impl, wd5, wd5 impl). Pomocí příkazu podobného tomuto "*http://www.myworkday.com*" funguje, ale "*http://myworkday.com*" nepodporuje. Kontakt [tým podpory klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) k získání těchto hodnot.  

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/workday-tutorial/tutorial_workday_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/workday-tutorial/tutorial_general_400.png)
    
1. Na **Workday konfigurace** klikněte na tlačítko **konfigurace Workday** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![WORKDAY konfigurace](./media/workday-tutorial/tutorial_workday_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Workday jako správce.

1. V **vyhledávacího pole** hledání s názvem **upravit nastavení Tenanta – zabezpečení** na horní levé straně domovské stránky.
   
    ![Upravit klienta zabezpečení](./media/workday-tutorial/IC782925.png "upravit klienta zabezpečení")

1. V **adresy URL přesměrování** části, proveďte následující kroky:
   
    ![Adresy URL přesměrování](./media/workday-tutorial/IC7829581.png "adresy URL přesměrování")
   
    a. Klikněte na tlačítko **přidat řádek**.
   
    b. V **přihlašovací adresa URL pro přesměrování** textového pole a **adresy URL přesměrování Mobile** textové pole, typ **přihlašovací adresa URL** jste zadali na **Workday domény a adresy URL** části webu Azure portal.
   
    c. Na webu Azure Portal na **nakonfigurovat přihlašování** okna, kopie **odhlašování URL**a vložte jej do **adresy URL přesměrování při odhlášení** textového pole.

    d. V **používá pro prostředí** textové pole, vyberte název prostředí.  

    >[!NOTE]
    > Hodnota atributu prostředí se váže na hodnotu adresy URL tenanta:  
    >-Pokud název domény adresy URL klienta Workday začíná impl například: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), **prostředí** atribut musí být nastaven na implementaci.  
    >– Pokud je název domény začíná znakem jiný, budete muset požádat [tým podpory klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) zobrazíte odpovídající **prostředí** hodnotu.

1. V **nastavení SAML** části, proveďte následující kroky:
   
    ![Instalační program SAML](./media/workday-tutorial/IC782926.png "nastavení SAML")
   
    a.  Vyberte **povolit ověřování SAML**.
   
    b.  Klikněte na tlačítko **přidat řádek**.

1. V **poskytovatele Identity SAML** části, proveďte následující kroky:
   
    ![Zprostředkovatelé Identity SAML](./media/workday-tutorial/IC7829271.png "SAML zprostředkovatele Identity")
   
    a. V **název zprostředkovatele identit** textového pole zadejte název zprostředkovatele (například: *SPInitiatedSSO*).
   
    b. Na webu Azure Portal na **nakonfigurovat přihlašování** okna, kopie **SAML Entity ID** hodnotu a vložte jej do **vystavitele** textového pole.

    ![Zprostředkovatelé Identity SAML](./media/workday-tutorial/IC7829272.png "SAML zprostředkovatele Identity")
   
    c. Na webu Azure Portal na **nakonfigurovat přihlašování** okna, kopie **odhlašování URL** hodnotu a vložte jej do **odhlašovací adresa URL odpovědi** textového pole.

    d. Na webu Azure Portal na **nakonfigurovat přihlašování** okna, kopie **SAML jednotné přihlašování – adresa URL služby** hodnotu a vložte jej do **adresa URL zprostředkovatele identity jednotného přihlašování služby** textového pole.

    e. V **používá pro prostředí** textové pole, vyberte název prostředí.

    f. Klikněte na tlačítko **certifikát veřejného klíče zprostředkovatele Identity**a potom klikněte na tlačítko **vytvořit**. 

    ![Vytvoření](./media/workday-tutorial/IC782928.png "vytvořit")

    g. Klikněte na tlačítko **vytvořit x509 veřejný klíč**. 

    ![Vytvoření](./media/workday-tutorial/IC782929.png "vytvořit")

1. V **veřejný klíč zobrazit x509** části, proveďte následující kroky: 
   
    ![Veřejný klíč zobrazit x509](./media/workday-tutorial/IC782930.png "zobrazení x509 veřejný klíč") 
   
    a. V **název** textového pole zadejte název vašeho certifikátu (například: *PPE\_SP*).
   
    b. V **platné od** textového pole zadejte platnou hodnotu atributu vašeho certifikátu.
   
    c.  V **platný na** textového pole zadejte platnou hodnotu atributu vašeho certifikátu.
   
    > [!NOTE]
    > Můžete získat platný datum a platný datum, od staženého certifikátu poklepáním.  Data patří **podrobnosti** kartu.
    > 
    >
   
    d.  Otevřete váš certifikát base-64 kódovaných v programu Poznámkový blok a zkopírujte jeho obsah.
   
    e.  V **certifikát** textové pole, vložte obsah schránky.
   
    f.  Klikněte na **OK**.

1. Proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Konfigurace jednotného přihlašování")
   
    a.  V **ID poskytovatele služby** textové pole, typ **http://www.workday.com**.
   
    b. Vyberte **není Deflate žádosti o ověření iniciovaného Zprostředkovatelem přihlašování**.
   
    c. Jako **požádat o podpis metody ověřování**vyberte **SHA256**. 
   
    ![Ověřování podpisu požadavku](./media/workday-tutorial/WorkdaySSOConfiguration.png "ověřování podpisu požadavku") 
   
    d. Klikněte na **OK**. 
   
    ![OK](./media/workday-tutorial/IC782933.png "OK")

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/workday-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/workday-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/workday-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/workday-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-workday-test-user"></a>Vytvoření zkušebního uživatele Workday

V této části vytvořte uživatele Britta Simon ve Workday. Práce s [tým podpory klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) přidat uživatele na platformě Workday. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Workday.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit k Workday Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Workday**.

    ![Odkaz Workday v seznamu aplikací](./media/workday-tutorial/tutorial_workday_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Workday na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Workday.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/workday-tutorial/tutorial_general_01.png
[2]: ./media/workday-tutorial/tutorial_general_02.png
[3]: ./media/workday-tutorial/tutorial_general_03.png
[4]: ./media/workday-tutorial/tutorial_general_04.png

[100]: ./media/workday-tutorial/tutorial_general_100.png

[200]: ./media/workday-tutorial/tutorial_general_200.png
[201]: ./media/workday-tutorial/tutorial_general_201.png
[202]: ./media/workday-tutorial/tutorial_general_202.png
[203]: ./media/workday-tutorial/tutorial_general_203.png
