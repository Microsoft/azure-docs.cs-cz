---
title: 'Kurz: Azure Active Directory integrace s Zscaler dva | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler dva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 066581456b0f3dcbe4793c95cdb511e52413d1fb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211566"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Kurz: Azure Active Directory integrace s Zscaler dvě

V tomto kurzu zjistěte, jak integrovat Zscaler dvě s Azure Active Directory (Azure AD).

Integrace s Azure AD Zscaler dva poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke dvěma Zscaler
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Zscaler dva (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zscaler dva, potřebujete následující položky:

- Předplatné služby Azure AD
- Zscaler dva jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zscaler dva z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-two-from-the-gallery"></a>Přidání Zscaler dva z Galerie
Pokud chcete nakonfigurovat integraci Zscaler dva do služby Azure AD, potřebujete přidat Zscaler dva z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Zscaler dva z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Zscaler dva**.

    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-two-tutorial/tutorial_zscalertwo_search.png)

5. Na panelu výsledků vyberte **Zscaler dva**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-two-tutorial/tutorial_zscalertwo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscaler dva podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co protějšku uživatele ve dvou Zscaler je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Zscaler dva musí navázat.

V Zscaler dva přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscaler dva, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Konfigurace nastavení proxy serveru](#configuring-proxy-settings)**  – Pokud chcete nakonfigurovat nastavení proxy serveru v Internet Exploreru
3. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytváření Zscaler dva testovací uživatele](#creating-a-zscaler-two-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zscaler dvě propojené služby Azure AD reprezentace daného uživatele.
5. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
6. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Zscaler dva.

**Ke konfiguraci Azure AD jednotné přihlašování s Zscaler dva, proveďte následující kroky:**

1. Na portálu Azure na **Zscaler dva** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/zscaler-two-tutorial/tutorial_zscalertwo_samlbase.png)

3. Na **Zscaler dvě domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/zscaler-two-tutorial/tutorial_zscalertwo_url.png)

   Do textového pole Přihlašovací adresa URL zadejte adresu URL používá uživatelům přihlášení do aplikace ZScaler dva.

    > [!NOTE] 
    > Budete muset aktualizovat tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory dva klientské Zscaler](https://www.zscaler.com/company/contact) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/zscaler-two-tutorial/tutorial_zscalertwo_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/zscaler-two-tutorial/tutorial_general_400.png)

6. Na **dvě konfigurační Zscaler** klikněte na tlačítko **nakonfigurovat dva Zscaler** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/zscaler-two-tutorial/tutorial_zscalertwo_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se na váš web ZScaler dvě společnosti jako správce.

8. V nabídce v horní části, klikněte na tlačítko **správy**.
   
    ![Správa](./media/zscaler-two-tutorial/ic800206.png "správy")

9. V části **spravovat správci & role**, klikněte na tlačítko **Správa uživatelů a ověřování**.   
            
    ![Správa uživatelů a ověřování](./media/zscaler-two-tutorial/ic800207.png "správu uživatelů a ověřování")

10. V **zvolte možnosti ověřování pro vaši organizaci** část, proveďte následující kroky:   
                
    ![Ověřování](./media/zscaler-two-tutorial/ic800208.png "ověřování")
   
    a. Vyberte **ověření pomocí SAML Single Sign-On**.

    b. Klikněte na tlačítko **konfigurace SAML jeden přihlašování parametrů**.

11. Na **konfigurace SAML jeden přihlašování parametry** dialogové okno stránky, proveďte následující kroky a pak klikněte na **provést**

    ![Jednotné přihlašování](./media/zscaler-two-tutorial/ic800209.png "jednotného přihlašování")
    
    a. Vložení **SAML jeden přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure do **URL SAML portálu, ke které se odesílají uživatele pro ověřování** textové pole.
    
    b. V **atribut obsahující přihlašovací jméno** textovému poli, typ **NameID**.
    
    c. Chcete-li nahrát stažený certifikát, klikněte na tlačítko **Zscaler pem**.
    
    d. Vyberte **zapnout automatické zřizování SAML**.

12. Na **konfigurace ověřování uživatele** dialogové okno proveďte následující kroky:

    ![Správa](./media/zscaler-two-tutorial/ic800210.png "správy")
    
    a. Klikněte na **Uložit**.

    b. Klikněte na tlačítko **aktivovat nyní**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v Internet Exploreru

1. Spustit **aplikace Internet Explorer**.

2. Vyberte **Možnosti Internetu** z **nástroje** nabídku pro otevření **Možnosti Internetu** dialogové okno.   
    
     ![Možnosti Internetu](./media/zscaler-two-tutorial/ic769492.png "Možnosti Internetu")

3. Klikněte **připojení** kartě.   
  
     ![Připojení](./media/zscaler-two-tutorial/ic769493.png "připojení")

4. Klikněte na tlačítko **nastavení místní sítě** otevřete **nastavení místní sítě** dialogové okno.

5. V části Proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/zscaler-two-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **použít proxy server pro síť LAN**.

    b. Do textového pole adresu zadejte **gateway.zscalertwo.net**.

    c. Do textového pole Port zadejte **80**.

    d. Vyberte **Nepoužívat proxy server pro místní adresy**.

    e. Klikněte na tlačítko **OK** zavřete **nastavení místní sítě (LAN)** dialogové okno.

6. Klikněte na tlačítko **OK** zavřete **Možnosti Internetu** dialogové okno.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-two-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-two-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-two-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/zscaler-two-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-zscaler-two-test-user"></a>Vytváření Zscaler dva testovací uživatele

Povolit uživatelům Azure AD přihlášení do dvou ZScaler, musí být zřízená k ZScaler dva. V případě ZScaler dva zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k vaší **Zscaler dva** klienta.

2. Klikněte na tlačítko **správy**.   
   
    ![Správa](./media/zscaler-two-tutorial/ic781035.png "správy")

3. Klikněte na tlačítko **Správa uživatelů**.   
        
     ![Přidat](./media/zscaler-two-tutorial/ic781036.png "přidat")

4. V **uživatelé** , klikněte na **přidat**.
      
    ![Přidat](./media/zscaler-two-tutorial/ic781037.png "přidat")

5. V části přidat uživatele proveďte následující kroky:
        
    ![Přidat uživatele](./media/zscaler-two-tutorial/ic781038.png "přidat uživatele")
   
    a. Typ **UserID**, **zobrazované uživatelské jméno**, **heslo**, **Potvrdit heslo**a potom vyberte **skupiny** a **oddělení** platný Azure AD účet chcete zřídit.

    b. Klikněte na **Uložit**.

> [!NOTE]
> Ostatní ZScaler dvou nástrojů vytvoření účtu uživatele nebo rozhraní API poskytovaných ZScaler dva můžete použít ke zřízení uživatelských účtů Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Zscaler dva.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Zscaler dva, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zscaler dva**.

    ![Konfigurovat jednotné přihlašování](./media/zscaler-two-tutorial/tutorial_zscalertwo_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Zscaler dva na přístupovém panelu, jste měli získat automaticky přihlášení k Zscaler dvě aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-two-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-two-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-two-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-two-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-two-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-two-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-two-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-two-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-two-tutorial/tutorial_general_203.png

