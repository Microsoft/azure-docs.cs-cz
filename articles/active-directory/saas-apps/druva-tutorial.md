---
title: 'Kurz: Integrace Azure Active Directory se službou Druva | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a platformy Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: 985304244acdfafa4fa99dbbe876f35b3e6c58b2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439357"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Kurz: Integrace Azure Active Directory se službou Druva

V tomto kurzu se dozvíte, jak integrovat platformy Druva s Azure Active Directory (Azure AD).

Integrace platformy Druva s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Druva.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Druva (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Druva, potřebujete následující položky:

- S předplatným služby Azure AD
- Platformy Druva jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Druva z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-druva-from-the-gallery"></a>Přidání Druva z Galerie
Konfigurace integrace platformy Druva do služby Azure AD, budete muset přidat Druva z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Druva z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **platformy Druva**vyberte **platformy Druva** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Platformy Druva v seznamu výsledků](./media/druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí platformy Druva podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Druva je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Druva.

V Druva, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí platformy Druva, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Druva](#create-a-druva-test-user)**  – Pokud chcete mít protějšek Britta Simon Druva, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci platformy Druva.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí platformy Druva, proveďte následující kroky:**

1. Na webu Azure Portal na **platformy Druva** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/druva-tutorial/tutorial_druva_samlbase.png)

1. Na **Druva domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/druva-tutorial/tutorial_druva_url.png)

    V **identifikátor** textového pole zadejte hodnotu řetězce: `druva-cloud`
    
1. Zkontrolujte **zobrazit pokročilé nastavení URL**. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/druva-tutorial/tutorial_druva_url1.png)
    
    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://cloud.druva.com/home`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/druva-tutorial/tutorial_druva_certificate.png) 

1. Aplikace platformy Druva očekává, že kontrolní výrazy SAML v určitém formátu, které je potřeba přidat vlastní atribut mapování vaší **atributy tokenu SAML** konfigurace. 

    ![Konfigurace jednotného přihlašování](./media/druva-tutorial/tutorial_druva_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku a proveďte následující kroky:

    | Název atributu      | Hodnota atributu      |
    | ------------------- | -------------------- |
    | neobjeví jako synchronizovaný\_auth\_tokenu |Zadejte token vygenerovanou hodnotu |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.
    
    ![Konfigurace jednotného přihlašování](./media/druva-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurace jednotného přihlašování](./media/druva-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek. Token vygenerovanou hodnotu je vysvětlen později v kurzu.
    
    d. Klikněte na tlačítko **OK**.    

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/druva-tutorial/tutorial_general_400.png)

1. Na **konfigurace platformy Druva** klikněte na tlačítko **konfigurovat platformy Druva** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/druva-tutorial/tutorial_druva_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Druva jako správce.

1. Přejděte na **spravovat \> nastavení**.

    ![Nastavení](./media/druva-tutorial/ic795091.png "nastavení")

1. V dialogovém okně Nastavení jednotného přihlašování proveďte následující kroky:

    ![Jednotné přihlašování – nastavení](./media/druva-tutorial/ic795092.png "jednotné přihlašování – nastavení")
    
    a. V **přihlašovací adresa URL zprostředkovatele ID** textového pole vložte hodnotu **jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
        
    b. V **odhlašovací adresa URL zprostředkovatele ID** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal
        
    c. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **ID zprostředkovatele certifikátu** textové pole
     
    d. Chcete-li otevřít **nastavení** klikněte na **Uložit**.

1. Na **nastavení** klikněte na **vygenerovat Token jednotného přihlašování**.

    ![Nastavení](./media/druva-tutorial/ic795093.png "nastavení")

1. Na **jednotné přihlašování ověřovací Token** dialogového okna, proveďte následující kroky:

    ![Token jednotného přihlašování](./media/druva-tutorial/ic795094.png "Token jednotného přihlašování")
    
    a. Klikněte na tlačítko **kopírování**, vložit zkopírovat hodnotu v **hodnotu** textového pole v **přidat atribut** části webu Azure Portal.
    
    b. Klikněte na **Zavřít**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/druva-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/druva-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/druva-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/druva-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-druva-test-user"></a>Vytvoření zkušebního uživatele Druva

Chcete-li povolit uživatele Azure AD k přihlášení do platformy Druva, musí být poskytnuty do platformy Druva. V případě platformy Druva zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **platformy Druva** společnosti serveru jako správce.

1. Přejděte na **spravovat \> uživatelé**.
   
   ![Správa uživatelů](./media/druva-tutorial/ic795097.png "Správa uživatelů")

1. Klikněte na tlačítko **vytvořit nový**.
   
   ![Správa uživatelů](./media/druva-tutorial/ic795098.png "Správa uživatelů")

1. V dialogovém okně Vytvořit nové uživatele proveďte následující kroky:
   
   ![Vytvoření NewUser](./media/druva-tutorial/ic795099.png "vytvořit NewUser")
   
   a. V **e-mailová adresa** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.
   
   b. V **název** textového pole zadejte jméno uživatele, jako je **BrittaSimon**.
   
   c. Klikněte na tlačítko **vytvořit uživatele**.

>[!NOTE]
>Můžete použít jakékoli jiné platformy Druva uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Druva zřízení uživatelských účtů služby Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu platformy Druva.

![Přiřazení role uživatele][200] 

**Přiřadit Druva Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **platformy Druva**.

    ![Propojení platformy Druva v seznamu aplikací](./media/druva-tutorial/tutorial_druva_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Druva na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci platformy Druva.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/druva-tutorial/tutorial_general_01.png
[2]: ./media/druva-tutorial/tutorial_general_02.png
[3]: ./media/druva-tutorial/tutorial_general_03.png
[4]: ./media/druva-tutorial/tutorial_general_04.png

[100]: ./media/druva-tutorial/tutorial_general_100.png

[200]: ./media/druva-tutorial/tutorial_general_200.png
[201]: ./media/druva-tutorial/tutorial_general_201.png
[202]: ./media/druva-tutorial/tutorial_general_202.png
[203]: ./media/druva-tutorial/tutorial_general_203.png

