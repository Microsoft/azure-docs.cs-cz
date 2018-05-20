---
title: 'Kurz: Azure Active Directory integrace s Zscaler jeden | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler jeden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 1dcf9dfac84dcc4231660de9efba5af249e7f4db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Kurz: Azure Active Directory integrace s Zscaler jeden

V tomto kurzu zjistěte, jak integrovat Zscaler jeden s Azure Active Directory (Azure AD).

Integrace s Azure AD Zscaler jeden poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k jedné Zscaler
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Zscaler jeden (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zscaler jeden, potřebujete následující položky:

- Předplatné služby Azure AD
- Jednu Zscaler jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zscaler jeden z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-one-from-the-gallery"></a>Přidání Zscaler jeden z Galerie
Chcete-li nakonfigurovat integraci Zscaler jeden do služby Azure AD, přidejte Zscaler jeden z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Zscaler jeden z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Zscaler jeden**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_search.png)

5. Na panelu výsledků vyberte **Zscaler jeden**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscaler jeden podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Zscaler jeden je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Zscaler jeden musí navázat.

V jedné Zscaler přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscaler jeden, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Konfigurace nastavení proxy serveru](#configuring-proxy-settings)**  – Pokud chcete nakonfigurovat nastavení proxy serveru v Internet Exploreru
3. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytváření Zscaler jeden testovací uživatele](#creating-a-zscaler-one-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zscaler jednu propojenou s Azure AD reprezentace daného uživatele.
5. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
6. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v Zscaler jednu aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s Zscaler jeden, proveďte následující kroky:**

1. Na portálu Azure na **Zscaler jeden** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_samlbase.png)

3. Na **Zscaler jednu doménu a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_url.png)

    Do textového pole Přihlašovací adresa URL zadejte adresu URL používá uživatelům přihlášení do aplikace Zscaler jeden.

    > [!NOTE] 
    > Budete muset aktualizovat tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory pro jednoho klienta Zscaler](https://www.zscaler.com/company/contact) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_400.png)

6. Na **jednu konfiguraci Zscaler** klikněte na tlačítko **nakonfigurovat jeden Zscaler** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k vaší Zscaler jedné lokalitě společnosti jako správce.

8. V nabídce v horní části, klikněte na tlačítko **správy**.
   
    ![Správa](./media/active-directory-saas-zscaler-one-tutorial/ic800206.png "správy")

9. V části **spravovat správci & role**, klikněte na tlačítko **Správa uživatelů a ověřování**.   
            
    ![Správa uživatelů a ověřování](./media/active-directory-saas-zscaler-one-tutorial/ic800207.png "správu uživatelů a ověřování")

10. V **zvolte možnosti ověřování pro vaši organizaci** část, proveďte následující kroky:   
                
    ![Ověřování](./media/active-directory-saas-zscaler-one-tutorial/ic800208.png "ověřování")
   
    a. Vyberte **ověření pomocí SAML Single Sign-On**.

    b. Klikněte na tlačítko **konfigurace SAML jeden přihlašování parametrů**.

11. Na **konfigurace SAML jeden přihlašování parametry** dialogové okno stránky, proveďte následující kroky a pak klikněte na **provést**

    ![Jednotné přihlašování](./media/active-directory-saas-zscaler-one-tutorial/ic800209.png "jednotného přihlašování")
    
    a. Vložení **SAML jeden přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure do **URL SAML portálu, ke které se odesílají uživatele pro ověřování** textové pole.
    
    b. V **atribut obsahující přihlašovací jméno** textovému poli, typ **NameID**.
    
    c. Chcete-li nahrát stažený certifikát, klikněte na tlačítko **Zscaler pem**.
    
    d. Vyberte **zapnout automatické zřizování SAML**.

12. Na **konfigurace ověřování uživatele** dialogové okno proveďte následující kroky:

    ![Správa](./media/active-directory-saas-zscaler-one-tutorial/ic800210.png "správy")
    
    a. Klikněte na **Uložit**.

    b. Klikněte na tlačítko **aktivovat nyní**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v Internet Exploreru

1. Spustit **aplikace Internet Explorer**.

2. Vyberte **Možnosti Internetu** z **nástroje** nabídku pro otevření **Možnosti Internetu** dialogové okno.   
    
     ![Možnosti Internetu](./media/active-directory-saas-zscaler-one-tutorial/ic769492.png "Možnosti Internetu")

3. Klikněte **připojení** kartě.   
  
     ![Připojení](./media/active-directory-saas-zscaler-one-tutorial/ic769493.png "připojení")

4. Klikněte na tlačítko **nastavení místní sítě** otevřete **nastavení místní sítě** dialogové okno.

5. V části Proxy server proveďte následující kroky:   
   
    ![Proxy server](./media/active-directory-saas-zscaler-one-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **použít proxy server pro síť LAN**.

    b. Do textového pole adresu zadejte **gateway.zscalerone.net**.

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

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-zscaler-one-test-user"></a>Vytváření Zscaler jeden testovací uživatele

Povolit uživatelům Azure AD přihlášení k Zscaler jeden, musí být zřízená Zscaler k jednomu. V případě jedné Zscaler zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k vaší **Zscaler jeden** klienta.

2. Klikněte na tlačítko **správy**.   
   
    ![Správa](./media/active-directory-saas-zscaler-one-tutorial/ic781035.png "správy")

3. Klikněte na tlačítko **Správa uživatelů**.   
        
     ![Přidat](./media/active-directory-saas-zscaler-one-tutorial/ic781036.png "přidat")

4. V **uživatelé** , klikněte na **přidat**.
      
    ![Přidat](./media/active-directory-saas-zscaler-one-tutorial/ic781037.png "přidat")

5. V části přidat uživatele proveďte následující kroky:
        
    ![Přidat uživatele](./media/active-directory-saas-zscaler-one-tutorial/ic781038.png "přidat uživatele")
   
    a. Typ **UserID**, **zobrazované uživatelské jméno**, **heslo**, **Potvrdit heslo**a potom vyberte **skupiny** a **oddělení** platný Azure AD účet chcete zřídit.

    b. Klikněte na **Uložit**.

> [!NOTE]
> Další nástroje pro vytvoření účtu Zscaler jeden uživatel nebo rozhraní API poskytovaných Zscaler jeden můžete použít ke zřízení uživatelských účtů Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Zscaler jeden.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Zscaler jeden, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zscaler jeden**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Zscaler jeden na přístupovém panelu, jste měli získat automaticky přihlášení k Zscaler jedné aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_203.png

