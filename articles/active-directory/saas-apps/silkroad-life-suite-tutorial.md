---
title: 'Kurz: Integrace Azure Active Directory sadou SilkRoad životnost | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SilkRoad života Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 962b3f0e18c2dbedf80c0cfca6bc8275b394307b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046011"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Kurz: Integrace Azure Active Directory sadou SilkRoad životnosti

V tomto kurzu se dozvíte, jak integrovat SilkRoad života Suite s Azure Active Directory (Azure AD).

Integrace SilkRoad života Suite s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k sadě životnosti SilkRoad.
- Uživatele, aby automaticky získat přihlášeného k sadě SilkRoad života (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD sadou SilkRoad životnosti, potřebujete následující položky:

- S předplatným služby Azure AD
- Suite SilkRoad života jednotného přihlašování povoleno předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání životnosti Suite SilkRoad z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Přidání životnosti Suite SilkRoad z Galerie
Pokud chcete nakonfigurovat integraci sady života SilkRoad do služby Azure AD, potřebujete přidat SilkRoad života Suite z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SilkRoad života Suite z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SilkRoad života Suite**vyberte **SilkRoad života Suite** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Sada SilkRoad životnosti v seznamu výsledků](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí sady života SilkRoad podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek sady SilkRoad životní je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v sadě života SilkRoad potřeba navázat.

V sadě SilkRoad životnosti, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování sadou SilkRoad životnosti, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SilkRoad života Suite](#create-a-silkroad-life-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon životní sady SilkRoad, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vaší aplikaci SilkRoad života Suite.

**Ke konfiguraci Azure AD jednotného přihlašování sadou SilkRoad životnosti, proveďte následující kroky:**

1. Na webu Azure Portal na **SilkRoad života Suite** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. Na **SilkRoad života sada domény a adresy URL** části, proveďte následující kroky:

    ![SilkRoad života sada domény a adresy URL jednotného přihlašování – informace](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory SilkRoad života Suite klienta](https://www.silkroad.com/locations/) k získání těchto hodnot. 

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. Na **SilkRoad života Suite konfigurace** klikněte na **konfiguraci sady života SilkRoad** otevřete **nakonfigurovat přihlašování** okno. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace sady SilkRoad životnosti](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Přihlašování k webu společnosti SilkRoad jako správce. 
 
    >[!NOTE] 
    > Pokud chcete získat přístup k aplikaci SilkRoad života Suite ověřování pro konfigurace federace se službou Microsoft Azure AD, obraťte se prosím na podporu SilkRoad nebo vaším zástupcem SilkRoad služby.

8. Přejděte na **poskytovatele služeb**a potom klikněte na tlačítko **federace podrobnosti**. 
   
    ![Azure AD jednotné přihlašování][10]

9. Klikněte na tlačítko **stáhnout federačních metadat**a poté uložte soubor metadat ve vašem počítači.
   
    ![Azure AD jednotné přihlašování][11] 

10. Ve vaší **SilkRoad** aplikace, klikněte na tlačítko **ověřování zdroje**.
   
    ![Azure AD jednotné přihlašování][12] 

11. Klikněte na tlačítko **přidat ověření – zdroj**. 
   
    ![Azure AD jednotné přihlašování][13] 

12. V **přidat zdroj ověřování** části, proveďte následující kroky: 
   
    ![Azure AD jednotné přihlašování][14]
  
    a. V části **možnost 2 – soubor metadat**, klikněte na tlačítko **Procházet** nahrát soubor metadat stažené z webu Azure portal.
  
    b. Klikněte na tlačítko **vytvořit zprostředkovatelů Identity pomocí Souborová Data**.

13. V **ověřování zdroje** klikněte na tlačítko **upravit**. 
    
     ![Azure AD jednotné přihlašování][15] 

14. Na **upravit zdroj ověřování** dialogového okna, proveďte následující kroky: 
    
     ![Azure AD jednotné přihlašování][16] 

    a. Jako **povoleno**vyberte **Ano**.

    b. V **EntityId** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.
   
    c. V **Popis zprostředkovatele identity** textového pole zadejte popis pro vaši konfiguraci (například: *jednotného přihlašování k Azure AD*).

    d. V **soubor metadat** nahrávání do textového pole **metadat** soubor, který jste si stáhli z webu Azure portal.
  
    e. V **název zprostředkovatele identity** textového pole zadejte název, který je specifický pro konfiguraci (například: *Azure SP*).
  
    f. V **odhlašovací adresa URL služby** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.

    g. V **přihlašování – adresa URL služby** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    h. Klikněte na **Uložit**.

15. Zakažte všechny ostatní zdroje ověřování. 
    
     ![Azure AD jednotné přihlašování][17]

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/silkroad-life-suite-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/silkroad-life-suite-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Vytvoření zkušebního uživatele SilkRoad života Suite

V této části vytvořte uživatele Britta Simon sady SilkRoad život. Práce s [tým podpory SilkRoad života Suite klienta](https://www.silkroad.com/locations/) přidat uživatele na platformě SilkRoad života Suite. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k sadě SilkRoad životního.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit SilkRoad života Suite, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SilkRoad života Suite**.

    ![Odkaz SilkRoad života Suite v seznamu aplikací](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SilkRoad života Suite na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SilkRoad života Suite.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png
