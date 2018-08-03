---
title: 'Kurz: Integrace Azure Active Directory s Beta Zscalerem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 29637f8a733e9f92f37144491bef4ab4ba5aae07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433356"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Kurz: Integrace Azure Active Directory s Zscaler Beta

V tomto kurzu se dozvíte, jak integrovat Beta Zscalerem se službou Azure Active Directory (Azure AD).

Integrace Zscalerem Beta s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Zscaler Beta
- Uživatele, aby automaticky získat přihlášení k Zscalerem Beta (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem Beta, potřebujete následující položky:

- S předplatným služby Azure AD
- Beta Zscalerem jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zscalerem Beta z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-beta-from-the-gallery"></a>Přidání Zscalerem Beta z Galerie
Pokud chcete nakonfigurovat integraci Zscalerem Beta do služby Azure AD, budete muset přidat Zscalerem Beta z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Zscalerem Beta z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Zscalerem Beta**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_search.png)

1. Na panelu výsledků vyberte **Zscalerem Beta**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a test Azure AD jednotné přihlašování s Beta Zscalerem podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek ve verzi Beta Zscalerem je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve verzi Beta Zscalerem potřeba navázat.

Ve verzi Beta Zscalerem, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem Beta, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Konfigurace nastavení proxy serveru](#configuring-proxy-settings)**  – konfigurace nastavení proxy serveru v aplikaci Internet Explorer
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Zscalerem Beta](#creating-a-zscaler-beta-test-user)**  – Pokud chcete mít protějšek Britta Simon Zscalerem Beta, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Zscalerem Beta.

**Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem Beta, proveďte následující kroky:**

1. Na webu Azure Portal na **Zscalerem Beta** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_samlbase.png)

1. Na **Zscalerem beta verze domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_url.png)

    V textovém poli přihlašovací adresa URL zadejte adresu URL používají vaši uživatelé k přihlašování do aplikace Zscalerem Beta.

    > [!NOTE] 
    > Budete muset aktualizovat tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Zscalerem beta verzi klienta](https://www.zscaler.com/company/contact) tuto výhodu získáte. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/zscaler-beta-tutorial/tutorial_general_400.png)

1. Na **Zscalerem beta verze konfigurace** klikněte na tlačítko **konfigurace Beta Zscalerem** otevřete **nakonfigurovat přihlašování** okno. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Zscalerem Beta jako správce.

1. V nabídce v horní části klikněte na tlačítko **správu**.
   
    ![Správa](./media/zscaler-beta-tutorial/ic800206.png "správy")

1. V části **Správa správců & role**, klikněte na tlačítko **spravovat uživatele a ověření**.   
            
    ![Správa uživatelů a ověřování](./media/zscaler-beta-tutorial/ic800207.png "správu uživatelů a ověřování")

1. V **zvolte možnosti ověřování pro vaši organizaci** části, proveďte následující kroky:   
                
    ![Ověřování](./media/zscaler-beta-tutorial/ic800208.png "ověřování")
   
    a. Vyberte **ověření pomocí SAML jednotného přihlašování**.

    b. Klikněte na tlačítko **konfigurace SAML jednotné přihlašování – parametry**.

1. Na **konfigurace SAML jednotné přihlašování parametrů** dialogového okna stránky, proveďte následující kroky a pak klikněte na **Hotovo**

    ![Jednotné přihlašování](./media/zscaler-beta-tutorial/ic800209.png "jednotného přihlašování")
    
    a. Vložit **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal do **URL portálu SAML, ke kterému jsou uživatelé nasměrovaní ověřování** textového pole.
    
    b. V **atribut, který obsahuje přihlašovací jméno** textové pole, typ **NameID**.
    
    c. Pokud chcete uložit stažený certifikát, klikněte na tlačítko **Zscalerem pem**.
    
    d. Vyberte **povolení automatického zřizování SAML**.

1. Na **konfiguraci ověření uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Správa](./media/zscaler-beta-tutorial/ic800210.png "správy")
    
    a. Klikněte na **Uložit**.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v aplikaci Internet Explorer

1. Spustit **aplikace Internet Explorer**.

1. Vyberte **Možnosti Internetu** z **nástroje** nabídku otevřít **Možnosti Internetu** dialogového okna.   
    
     ![Možnosti Internetu](./media/zscaler-beta-tutorial/ic769492.png "Možnosti Internetu")

1. Klikněte na tlačítko **připojení** kartu.   
  
     ![Připojení](./media/zscaler-beta-tutorial/ic769493.png "připojení")

1. Klikněte na tlačítko **nastavení místní sítě** otevřít **nastavení místní sítě** dialogového okna.

1. V části Proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-beta-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **používat proxy server pro síť LAN**.

    b. Do textového pole Adresa zadejte **gateway.zscalerbeta.net**.

    c. V textovém poli portu zadejte **80**.

    d. Vyberte **obejít proxy server pro místní adresy**.

    e. Klikněte na tlačítko **OK** zavřete **nastavení místní sítě (LAN)** dialogového okna.

1. Klikněte na tlačítko **OK** zavřete **Možnosti Internetu** dialogového okna.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-beta-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-beta-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-beta-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-beta-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-zscaler-beta-test-user"></a>Vytvoření zkušebního uživatele Zscalerem Beta

Povolit uživatele Azure AD se přihlaste k Zscalerem Beta, musí být zřízená na Zscalerem Beta. V případě Zscalerem beta verzí zřizování je ruční úlohy.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurace zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k vaší **Zscalerem Beta** tenanta.

1. Klikněte na tlačítko **správu**.   
   
    ![Správa](./media/zscaler-beta-tutorial/ic781035.png "správy")

1. Klikněte na tlačítko **Správa uživatelů**.   
        
     ![Přidat](./media/zscaler-beta-tutorial/ic781036.png "přidat")

1. V **uživatelé** klikněte na tlačítko **přidat**.
      
    ![Přidat](./media/zscaler-beta-tutorial/ic781037.png "přidat")

1. V části přidat uživatele proveďte následující kroky:
        
    ![Přidání uživatele](./media/zscaler-beta-tutorial/ic781038.png "přidat uživatele")
   
    a. Typ **UserID**, **zobrazované jméno uživatele**, **heslo**, **potvrzení hesla**a pak vyberte **skupiny**a **oddělení** platný Azure AD účtu, které chcete zřídit.

    b. Klikněte na **Uložit**.

> [!NOTE]
> Ke zřízení uživatelských účtů služby Azure AD můžete použít jiné nástroje pro tvorbu Zscalerem Beta uživatelského účtu nebo rozhraní API poskytovaných Zscalerem Beta.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem Beta.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Zscalerem Beta, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Zscalerem Beta**.

    ![Konfigurace jednotného přihlašování](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zscalerem Beta na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Zscalerem Beta.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-beta-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-beta-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-beta-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-beta-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-beta-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-beta-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-beta-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-beta-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-beta-tutorial/tutorial_general_203.png

