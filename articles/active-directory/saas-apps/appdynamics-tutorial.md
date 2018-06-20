---
title: 'Kurz: Azure Active Directory integrace s AppDynamics | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AppDynamics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: eb79001f6c74526ec2470612adaf290230d2a017
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227560"
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Kurz: Azure Active Directory integrace s AppDynamics

V tomto kurzu zjistěte, jak integrovat AppDynamics s Azure Active Directory (Azure AD).

Integrace AppDynamics s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k AppDynamics
- Můžete povolit uživatelům, aby automaticky získat přihlášení k AppDynamics (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AppDynamics, potřebujete následující položky:

- Předplatné služby Azure AD
- AppDynamics jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání AppDynamics z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-appdynamics-from-the-gallery"></a>Přidání AppDynamics z Galerie
Při konfiguraci integrace AppDynamics do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS AppDynamics z galerie.

**Pokud chcete přidat AppDynamics z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **AppDynamics**.

    ![Vytváření testovacího uživatele Azure AD](./media/appdynamics-tutorial/tutorial_appdynamics_search.png)

5. Na panelu výsledků vyberte **AppDynamics**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/appdynamics-tutorial/tutorial_appdynamics_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s AppDynamics podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v AppDynamics je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v AppDynamics musí navázat.

V AppDynamics, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AppDynamics, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele AppDynamics](#creating-an-appdynamics-test-user)**  – Pokud chcete mít protějšek Britta Simon v AppDynamics propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci AppDynamics.

**Ke konfiguraci Azure AD jednotné přihlašování s AppDynamics, proveďte následující kroky:**

1. Na portálu Azure na **AppDynamics** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_samlbase.png)

3. Na **AppDynamics domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.saas.appdynamics.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory AppDynamics klienta](https://www.appdynamics.com/support/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/appdynamics-tutorial/tutorial_general_400.png)

6. Na **AppDynamics konfigurace** klikněte na tlačítko **konfigurace AppDynamics** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti AppDynamics jako správce.

8. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a potom klikněte na **správy**.
   
    ![Správa](./media/appdynamics-tutorial/ic790216.png "správy")

9. Klikněte **zprostředkovatele ověřování** kartě.
   
    ![Zprostředkovatel ověřování](./media/appdynamics-tutorial/ic790224.png "zprostředkovatele ověřování")

10. V **zprostředkovatele ověřování** část, proveďte následující kroky:
   
    ![Konfigurace SAML](./media/appdynamics-tutorial/ic790225.png "konfigurace SAML")   

    a. Jako **zprostředkovatele ověřování**, vyberte **SAML**.

    b. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    c. V **adresy URL odhlašovací** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.
       
    d. V poznámkovém bloku otevřete váš kódování base-64 kódovaného certifikátu, zkopírujte obsah ho do schránky a vložte jej do **certifikát** textbox

    e. Klikněte na **Uložit**.

     ![Uložit](./media/appdynamics-tutorial/ic777673.png "uložit")

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/appdynamics-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/appdynamics-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/appdynamics-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/appdynamics-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-appdynamics-test-user"></a>Vytváření testovacího uživatele AppDynamics

Pokud chcete povolit uživatelům Azure AD přihlášení k AppDynamics, musí být zřízená do AppDynamics. V případě AppDynamics zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti AppDynamics jako správce.

2. Přejděte na **uživatelé**a potom klikněte na **+** otevřete **vytvořit uživatele** dialogové okno.
   
    ![Uživatelé](./media/appdynamics-tutorial/ic790229.png "uživatelů")

3. V **vytvořit uživatele** část, proveďte následující kroky:
   
    ![Vytvoření uživatele](./media/appdynamics-tutorial/ic790230.png "vytvoření uživatele")
   
    a. Typ **uživatelské jméno**, **název**, **e-mailu**, **nové heslo**, **opakujte nové heslo** platný aad účet, který chcete mají být zahrnuty do související textových polí.

    b. Klikněte na **Uložit**.

    >[!NOTE]
    >Můžete použít všechny ostatní AppDynamics uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované AppDynamics ke zřízení uživatelských účtů Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu AppDynamics.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon AppDynamics, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **AppDynamics**.

    ![Konfigurovat jednotné přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici AppDynamics na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci AppDynamics.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/appdynamics-tutorial/tutorial_general_01.png
[2]: ./media/appdynamics-tutorial/tutorial_general_02.png
[3]: ./media/appdynamics-tutorial/tutorial_general_03.png
[4]: ./media/appdynamics-tutorial/tutorial_general_04.png

[100]: ./media/appdynamics-tutorial/tutorial_general_100.png

[200]: ./media/appdynamics-tutorial/tutorial_general_200.png
[201]: ./media/appdynamics-tutorial/tutorial_general_201.png
[202]: ./media/appdynamics-tutorial/tutorial_general_202.png
[203]: ./media/appdynamics-tutorial/tutorial_general_203.png

