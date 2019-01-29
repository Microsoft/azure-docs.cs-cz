---
title: 'Kurz: Integrace Azure Active Directory se systémy Pega | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Pega systémy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: abeffacedd8b3cae9802e5a8c264748df313abf0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196731"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Kurz: Integrace Azure Active Directory s Pega systémy

V tomto kurzu se dozvíte, jak integrovat systémy Pega se službou Azure Active Directory (Azure AD).

Integrace Pega systémy s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k systémům Pega.
- Uživatele, aby automaticky získat přihlášeného k systémům Pega (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Pega systémy, potřebujete následující položky:

- Předplatné Azure AD
- Systémy Pega jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Pega systémů z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-pega-systems-from-the-gallery"></a>Přidání Pega systémů z Galerie
Pokud chcete nakonfigurovat integraci systémů Pega do služby Azure AD, budete muset přidat Pega systémů z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidat Pega systémů z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Pega systémy**vyberte **Pega systémy** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Systémy Pega v seznamu výsledků](./media/pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Pega systémy založené na testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v systémech Pega je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v systémech Pega potřeba navázat.

V systémech Pega přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Pega systémy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Pega systémy](#create-a-pega-systems-test-user)**  – Pokud chcete mít protějšek Britta Simon Pega systémů, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Pega systémy.

**Ke konfiguraci Azure AD jednotné přihlašování s Pega systémy, proveďte následující kroky:**

1. Na webu Azure Portal na **Pega systémy** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

1. Na **Pega systémy domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Pega systémy domény a adresy URL jednotného přihlašování – informace](./media/pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Pega systémy domény a adresy URL jednotného přihlašování – informace](./media/pegasystems-tutorial/tutorial_pegasystems_url1.png)

    V **stav přenosu** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a adresy URL stav propojení. Můžete najít hodnoty identifikátor a adresa URL odpovědi z aplikace Pega, což je vysvětleno dále v tomto kurzu. Stav přenosu, kontaktujte [tým podpory klientské systémy Pega](https://www.pega.com/contact-us) má být získána hodnota. 

1. Systémy Pega aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Tyto deklarace jsou pro konkrétní zákazníky a závisí na vašich požadavků. Následující volitelné deklarace identity jsou příkladu pouze která můžete nakonfigurovat pro vaši aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. 

    ![Konfigurace jednotného přihlašování](./media/pegasystems-tutorial/tutorial_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | uid | *********** |
    | CN  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organizace | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Telefon | *********** |

    > [!NOTE]
    > Toto jsou konkrétní hodnoty zákazníka. Zadejte odpovídající hodnoty.

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/pegasystems-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/pegasystems-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_general_400.png)
    
1. Ke konfiguraci jednotného přihlašování na **Pega systémy** straně, otevřete **Pega portál** pomocí účtu správce v jiném okně prohlížeče.

1. Vyberte **vytvořit** -> **SysAdmin** -> **ověřovací službu**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
1. Proveďte následující kroky na **vytvořit službu Aauthentication** obrazovky:

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Vyberte **SAML 2.0** z typu

    b. V **název** textového pole zadejte libovolný název například jednotného přihlašování k Azure AD

    c. V **krátký popis** textového pole zadejte popis, žádné  

    d. Klikněte na **vytvořit a otevřít** 
    
1. V **zprostředkovatele Identity (IdP) informace** části, klikněte na **metadat zprostředkovatele identity Import** a vyhledejte soubor metadat, který jste si stáhli z portálu Azure portal. Klikněte na tlačítko **odeslat** načtení metadat.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
1. To bude naplnění dat zprostředkovatele identity, jak je znázorněno níže.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
1. Proveďte následující kroky na **nastavení poskytovatele služeb (SP)** části:

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Kopírovat **identifikace entit** hodnotu a vložte zpátky na webu Azure Portal **identifikátor** textového pole.

    b.  Kopírovat **Assertion Consumer Service (ACS) umístění** hodnotu a vložte zpátky na webu Azure Portal **adresy URL odpovědi** textového pole.

    c. Vyberte **zakázat žádost o podepsání**.

1. Klikněte na **Uložit**.
    
> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/pegasystems-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/pegasystems-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/pegasystems-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/pegasystems-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-pega-systems-test-user"></a>Vytvoření zkušebního uživatele Pega systémy

Cílem této části je vytvořte uživatele Britta Simon v systémech Pega. Spojte se prosím s [tým podpory klientské systémy Pega](https://www.pega.com/contact-us) vytvořit uživatele v Pega Sysyems.


### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k systémům Pega.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit Pega systémů, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Pega systémy**.

    ![Odkaz Pega systémy v seznamu aplikací](./media/pegasystems-tutorial/tutorial_pegasystems_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Pega systémy na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Pega systémy.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/pegasystems-tutorial/tutorial_general_203.png

