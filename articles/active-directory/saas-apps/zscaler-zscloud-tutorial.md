---
title: 'Kurz: Azure Active Directory integrace s Zscaler ZSCloud | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: 43b122275a2a76c300bd3593e4aa321ba94b9ad8
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903334"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Kurz: Azure Active Directory integrace s Zscaler ZSCloud

V tomto kurzu zjistěte, jak integrovat Zscaler ZSCloud s Azure Active Directory (Azure AD).

Integrace Zscaler ZSCloud s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Zscaler ZSCloud
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Zscaler ZSCloud (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zscaler ZSCloud, potřebujete následující položky:

- Předplatné služby Azure AD
- Zscaler ZSCloud jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zscaler ZSCloud z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Přidání Zscaler ZSCloud z Galerie
Při konfiguraci integrace Zscaler ZSCloud do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Zscaler ZSCloud z galerie.

**Pokud chcete přidat Zscaler ZSCloud z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Zscaler ZSCloud**.

    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_search.png)

5. Na panelu výsledků vyberte **Zscaler ZSCloud**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a testu Azure AD jednotné přihlašování s Zscaler ZSCloud podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Zscaler ZSCloud je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Zscaler ZSCloud musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Zscaler ZSCloud.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscaler ZSCloud, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Konfigurace nastavení proxy serveru](#configuring-proxy-settings)**  – Pokud chcete nakonfigurovat nastavení proxy serveru v Internet Exploreru
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zscaler ZSCloud](#creating-a-zscaler-zscloud-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zscaler ZSCloud propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Zscaler ZSCloud.

**Ke konfiguraci Azure AD jednotné přihlašování s Zscaler ZSCloud, proveďte následující kroky:**

1. Na portálu Azure na **Zscaler ZSCloud** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_samlbase.png)

3. Na **Zscaler ZSCloud domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

     V **přihlašovací adresa URL** textové pole, zadejte adresu URL používá uživatelům přihlášení do aplikace ZScaler ZSCloud.
    
    > [!NOTE] 
    > Budete muset aktualizovat tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Zscaler ZSCloud klienta](https://support.zscaler.com/hc/articles/210172606-Zscaler-is-Expanding-Its-Zscloud-Global-Footprint) získat tuto hodnotu. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/zscaler-zscloud-tutorial/tutorial_general_400.png)

6. Na **Zscaler ZSCloud konfigurace** klikněte na tlačítko **konfigurace Zscaler ZSCloud** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se na váš web společnosti ZScaler ZSCloud jako správce.

8. V nabídce v horní části, klikněte na tlačítko **správy**.
   
    ![Správa](./media/zscaler-zscloud-tutorial/ic800206.png "správy")

9. V části **spravovat správci & role**, klikněte na tlačítko **Správa uživatelů a ověřování**.   
            
    ![Správa uživatelů a ověřování](./media/zscaler-zscloud-tutorial/ic800207.png "správu uživatelů a ověřování")

10. V **zvolte možnosti ověřování pro vaši organizaci** část, proveďte následující kroky:   
                
    ![Ověřování](./media/zscaler-zscloud-tutorial/ic800208.png "ověřování")
   
    a. Vyberte **ověření pomocí SAML Single Sign-On**.

    b. Klikněte na tlačítko **konfigurace SAML jeden přihlašování parametrů**.

11. Na **konfigurace SAML jeden přihlašování parametry** dialogové okno stránky, proveďte následující kroky a pak klikněte na **provést**

    ![Jednotné přihlašování](./media/zscaler-zscloud-tutorial/ic800209.png "jednotného přihlašování")
    
    a. Vložení **SAML jeden přihlašování adresa URL služby** hodnotu do **URL SAML portálu, ke které se odesílají uživatele pro ověřování** textové pole.
    
    b. V **atribut obsahující přihlašovací jméno** textovému poli, typ **NameID**.
    
    c. Chcete-li nahrát stažený certifikát, klikněte na tlačítko **Zscaler pem**.
    
    d. Vyberte **zapnout automatické zřizování SAML**.

12. Na **konfigurace ověřování uživatele** dialogové okno proveďte následující kroky:

    ![Správa](./media/zscaler-zscloud-tutorial/ic800210.png "správy")
    
    a. Klikněte na **Uložit**.

    b. Klikněte na tlačítko **aktivovat nyní**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v Internet Exploreru

1. Spustit **aplikace Internet Explorer**.

2. Vyberte **Možnosti Internetu** z **nástroje** nabídku pro otevření **Možnosti Internetu** dialogové okno.   
    
     ![Možnosti Internetu](./media/zscaler-zscloud-tutorial/ic769492.png "Možnosti Internetu")

3. Klikněte **připojení** kartě.   
  
     ![Připojení](./media/zscaler-zscloud-tutorial/ic769493.png "připojení")

4. Klikněte na tlačítko **nastavení místní sítě** otevřete **nastavení místní sítě** dialogové okno.

5. V části Proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-zscloud-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **použít proxy server pro síť LAN**.

    b. Do textového pole adresu zadejte **gateway.zscalerone.net**.

    c. Do textového pole Port zadejte **80**.

    d. Vyberte **Nepoužívat proxy server pro místní adresy**.

    e. Klikněte na tlačítko **OK** zavřete **nastavení místní sítě (LAN)** dialogové okno.

6. Klikněte na tlačítko **OK** zavřete **Možnosti Internetu** dialogové okno.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-zscaler-zscloud-test-user"></a>Vytvoření zkušebního uživatele Zscaler ZSCloud

Pokud chcete povolit uživatelům Azure AD přihlášení k ZScaler ZSCloud, musí být zřízená na ZScaler ZSCloud.  
V případě ZScaler ZSCloud zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k vaší **Zscaler** klienta.

2. Klikněte na tlačítko **správy**.   
   
    ![Správa](./media/zscaler-zscloud-tutorial/ic781035.png "správy")

3. Klikněte na tlačítko **Správa uživatelů**.   
        
     ![Přidat](./media/zscaler-zscloud-tutorial/ic781037.png "přidat")

4. V **uživatelé** , klikněte na **přidat**.
      
    ![Přidat](./media/zscaler-zscloud-tutorial/ic781037.png "přidat")

5. V části přidat uživatele proveďte následující kroky:
        
    ![Přidat uživatele](./media/zscaler-zscloud-tutorial/ic781038.png "přidat uživatele")
   
    a. Typ **UserID**, **zobrazované uživatelské jméno**, **heslo**, **Potvrdit heslo**a potom vyberte **skupiny** a **oddělení** platného účtu AAD chcete zřídit.

    b. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít všechny ostatní ZScaler ZSCloud uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované ZScaler ZSCloud zřídit AAD uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Zscaler ZSCloud.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Zscaler ZSCloud, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zscaler ZSCloud**.

    ![Konfigurovat jednotné přihlašování](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Pokud chcete testovat vaše nastavení jednotného přihlašování, otevřete Panel přístupu.

Když kliknete na dlaždici Zscaler ZSCloud na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Zscaler ZSCloud.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

