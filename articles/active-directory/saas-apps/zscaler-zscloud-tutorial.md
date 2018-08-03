---
title: 'Kurz: Integrace Azure Active Directory se Zscalerem ZSCloud | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: a23d68e0b48a01cf98a5d1cc136a6af46895b0ee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440639"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Kurz: Integrace Azure Active Directory se službou Zscaler ZSCloud

V tomto kurzu se dozvíte, jak integrovat ZSCloud Zscalerem se službou Azure Active Directory (Azure AD).

Integrace Zscalerem ZSCloud s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Zscaler ZSCloud
- Uživatele, aby automaticky získat přihlášení k Zscalerem ZSCloud (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem ZSCloud, potřebujete následující položky:

- S předplatným služby Azure AD
- Zscalerem ZSCloud jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zscalerem ZSCloud z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Přidání Zscalerem ZSCloud z Galerie
Ke konfiguraci integrace Zscalerem ZSCloud do služby Azure AD, budete muset přidat Zscalerem ZSCloud z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Zscalerem ZSCloud z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Zscalerem ZSCloud**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_search.png)

1. Na panelu výsledků vyberte **Zscalerem ZSCloud**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a testovací služby Azure AD jednotného přihlašování se Zscalerem ZSCloud podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Zscalerem ZSCloud je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zscalerem ZSCloud potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Zscalerem ZSCloud.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem ZSCloud, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Konfigurace nastavení proxy serveru](#configuring-proxy-settings)**  – konfigurace nastavení proxy serveru v aplikaci Internet Explorer
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Zscaler ZSCloud](#creating-a-zscaler-zscloud-test-user)**  – Pokud chcete mít protějšek Britta Simon Zscalerem ZSCloud, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Zscalerem ZSCloud.

**Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem ZSCloud, proveďte následující kroky:**

1. Na webu Azure Portal na **Zscalerem ZSCloud** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_samlbase.png)

1. Na **Zscalerem ZSCloud domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

     V **přihlašovací adresa URL** textové pole, zadejte adresu URL použít vaši uživatelé k přihlašování do aplikace Zscalerem ZSCloud.
    
    > [!NOTE] 
    > Budete muset aktualizovat tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Zscalerem ZSCloud klienta](https://help.zscaler.com/zia) tuto výhodu získáte. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/zscaler-zscloud-tutorial/tutorial_general_400.png)

1. Na **Zscalerem ZSCloud konfigurace** klikněte na tlačítko **nakonfigurovat ZSCloud Zscalerem** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Zscalerem ZSCloud jako správce.

1. V nabídce v horní části klikněte na tlačítko **správu**.
   
    ![Správa](./media/zscaler-zscloud-tutorial/ic800206.png "správy")

1. V části **Správa správců & role**, klikněte na tlačítko **spravovat uživatele a ověření**.   
            
    ![Správa uživatelů a ověřování](./media/zscaler-zscloud-tutorial/ic800207.png "správu uživatelů a ověřování")

1. V **zvolte možnosti ověřování pro vaši organizaci** části, proveďte následující kroky:   
                
    ![Ověřování](./media/zscaler-zscloud-tutorial/ic800208.png "ověřování")
   
    a. Vyberte **ověření pomocí SAML jednotného přihlašování**.

    b. Klikněte na tlačítko **konfigurace SAML jednotné přihlašování – parametry**.

1. Na **konfigurace SAML jednotné přihlašování parametrů** dialogového okna stránky, proveďte následující kroky a pak klikněte na **Hotovo**

    ![Jednotné přihlašování](./media/zscaler-zscloud-tutorial/ic800209.png "jednotného přihlašování")
    
    a. Vložit **SAML jednotné přihlašování – adresa URL služby** hodnoty do **URL portálu SAML, ke kterému jsou uživatelé nasměrovaní ověřování** textového pole.
    
    b. V **atribut, který obsahuje přihlašovací jméno** textové pole, typ **NameID**.
    
    c. Pokud chcete uložit stažený certifikát, klikněte na tlačítko **Zscalerem pem**.
    
    d. Vyberte **povolení automatického zřizování SAML**.

1. Na **konfiguraci ověření uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Správa](./media/zscaler-zscloud-tutorial/ic800210.png "správy")
    
    a. Klikněte na **Uložit**.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v aplikaci Internet Explorer

1. Spustit **aplikace Internet Explorer**.

1. Vyberte **Možnosti Internetu** z **nástroje** nabídku otevřít **Možnosti Internetu** dialogového okna.   
    
     ![Možnosti Internetu](./media/zscaler-zscloud-tutorial/ic769492.png "Možnosti Internetu")

1. Klikněte na tlačítko **připojení** kartu.   
  
     ![Připojení](./media/zscaler-zscloud-tutorial/ic769493.png "připojení")

1. Klikněte na tlačítko **nastavení místní sítě** otevřít **nastavení místní sítě** dialogového okna.

1. V části Proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-zscloud-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **používat proxy server pro síť LAN**.

    b. Do textového pole Adresa zadejte **gateway.zscalerone.net**.

    c. V textovém poli portu zadejte **80**.

    d. Vyberte **obejít proxy server pro místní adresy**.

    e. Klikněte na tlačítko **OK** zavřete **nastavení místní sítě (LAN)** dialogového okna.

1. Klikněte na tlačítko **OK** zavřete **Možnosti Internetu** dialogového okna.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-zscaler-zscloud-test-user"></a>Vytvoření zkušebního uživatele Zscaler ZSCloud

Umožňuje uživatelům Azure AD se přihlaste k ZScaler ZSCloud musí být poskytnuty k ZScaler ZSCloud.  
V případě Zscalerem ZSCloud zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurace zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k vaší **Zscalerem** tenanta.

1. Klikněte na tlačítko **správu**.   
   
    ![Správa](./media/zscaler-zscloud-tutorial/ic781035.png "správy")

1. Klikněte na tlačítko **Správa uživatelů**.   
        
     ![Přidat](./media/zscaler-zscloud-tutorial/ic781037.png "přidat")

1. V **uživatelé** klikněte na tlačítko **přidat**.
      
    ![Přidat](./media/zscaler-zscloud-tutorial/ic781037.png "přidat")

1. V části přidat uživatele proveďte následující kroky:
        
    ![Přidání uživatele](./media/zscaler-zscloud-tutorial/ic781038.png "přidat uživatele")
   
    a. Typ **UserID**, **zobrazované jméno uživatele**, **heslo**, **potvrzení hesla**a pak vyberte **skupiny**a **oddělení** platného účtu AAD, které chcete zřídit.

    b. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné Zscalerem ZSCloud uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Zscalerem ZSCloud uživatelským účtům, zřídit AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem ZSCloud.

![Přiřadit uživatele][200] 

**Přiřadit Zscalerem ZSCloud Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Zscalerem ZSCloud**.

    ![Konfigurace jednotného přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Pokud chcete otestovat jednotné přihlašování – nastavení, otevřete na přístupovém panelu.

Po kliknutí na dlaždici Zscalerem ZSCloud na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Zscalerem ZSCloud.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-zscloud-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-zscloud-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-zscloud-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-zscloud-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-zscloud-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-zscloud-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-zscloud-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-zscloud-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-zscloud-tutorial/tutorial_general_203.png

