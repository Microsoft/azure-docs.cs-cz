---
title: 'Kurz: Azure Active Directory integrace s PurelyHR | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PurelyHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: ba24422f7ff5759153a6e067cc61251b43a7a091
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286928"
---
# <a name="tutorial-azure-active-directory-integration-with-purelyhr"></a>Kurz: Azure Active Directory integrace s PurelyHR

V tomto kurzu zjistěte, jak integrovat PurelyHR s Azure Active Directory (Azure AD).

Integrace PurelyHR s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k PurelyHR
- Můžete povolit uživatelům, aby automaticky získat přihlášení k PurelyHR (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s PurelyHR, potřebujete následující položky:

- Předplatné služby Azure AD
- PurelyHR jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání PurelyHR z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-purelyhr-from-the-gallery"></a>Přidání PurelyHR z Galerie
Při konfiguraci integrace PurelyHR do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS PurelyHR z galerie.

**Pokud chcete přidat PurelyHR z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **PurelyHR**.

    ![Vytváření testovacího uživatele Azure AD](./media/purelyhr-tutorial/tutorial_purelyhr_search.png)

5. Na panelu výsledků vyberte **PurelyHR**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/purelyhr-tutorial/tutorial_purelyhr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s PurelyHR podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v PurelyHR je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v PurelyHR musí navázat.

V PurelyHR, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s PurelyHR, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele PurelyHR](#creating-a-purelyhr-test-user)**  – Pokud chcete mít protějšek Britta Simon v PurelyHR propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci PurelyHR.

**Ke konfiguraci Azure AD jednotné přihlašování s PurelyHR, proveďte následující kroky:**

1. Na portálu Azure na **PurelyHR** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/purelyhr-tutorial/tutorial_purelyhr_samlbase.png)

3. Na **PurelyHR domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/purelyhr-tutorial/tutorial_purelyhr_url.png)
   
    V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyID>.purelyhr.com/sso-consume`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/purelyhr-tutorial/tutorial_purelyhr_url1.png)
    
    V **přihlašovací adresa URL** textovému poli, zadejte hodnotu pomocí následujícího vzorce: `https://<companyID>.purelyhr.com/sso-initiate`
     
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujte skutečná adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory PurelyHR klienta](http://support.purelyhr.com/) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/purelyhr-tutorial/tutorial_purelyhr_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/purelyhr-tutorial/tutorial_general_400.png)
    
7. Na **PurelyHR konfigurace** klikněte na tlačítko **konfigurace PurelyHR** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/purelyhr-tutorial/tutorial_purelyhr_configure.png) 

8. Konfigurace jednotného přihlašování na **PurelyHR** straně, přihlašovací údaje pro jejich web jako správce.

9. Otevřete **řídicí panel** z možností na panelu nástrojů a klikněte na **nastavení jednotného přihlašování k**.

10. Vložení hodnoty do polí, jak je popsáno níže-

    ![Konfigurovat jednotné přihlašování](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Otevřete **Certificate(Bas64)** stáhli z portálu Azure v programu Poznámkový blok a zkopírujte hodnotu certifikátu. Vložit zkopírovaný hodnotu do **certifikát X.509** pole.

    b. V **URL vystavitele Idp** pole, vložte **SAML Entity ID** zkopírovaných z portálu Azure.

    c. V **adresu URL koncového bodu Idp** pole, vložte **SAML jeden přihlašování adresa URL služby** zkopírovaných z portálu Azure. 

    d. Zkontrolujte **automaticky vytvářet uživatelé** zaškrtávací políčko pro povolení automatického uživatele zřizování v PurelyHR.

    e. Klikněte na tlačítko **uložit změny** uložte nastavení.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/purelyhr-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/purelyhr-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/purelyhr-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/purelyhr-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-purelyhr-test-user"></a>Vytvoření zkušebního uživatele PurelyHR

Pokud chcete povolit uživatelům Azure AD přihlášení k PurelyHR, musí být zřízená do PurelyHR. V PurelyHR zřizování je automatická úloha a nejsou potřeba žádné ruční kroky, pokud je povoleno automatické uživatele zřizování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu PurelyHR.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon PurelyHR, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **PurelyHR**.

    ![Konfigurovat jednotné přihlašování](./media/purelyhr-tutorial/tutorial_purelyhr_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Klikněte na dlaždici vyrovná se se zatížením pro správu vzdělávacího procesu na přístupovém panelu, můžete získat automaticky přihlášení k aplikaci vyrovná se se zatížením pro správu vzdělávacího procesu.

Další informace o na přístupovém panelu najdete v tématu. [Úvod do přístupového panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/purelyhr-tutorial/tutorial_general_01.png
[2]: ./media/purelyhr-tutorial/tutorial_general_02.png
[3]: ./media/purelyhr-tutorial/tutorial_general_03.png
[4]: ./media/purelyhr-tutorial/tutorial_general_04.png

[100]: ./media/purelyhr-tutorial/tutorial_general_100.png

[200]: ./media/purelyhr-tutorial/tutorial_general_200.png
[201]: ./media/purelyhr-tutorial/tutorial_general_201.png
[202]: ./media/purelyhr-tutorial/tutorial_general_202.png
[203]: ./media/purelyhr-tutorial/tutorial_general_203.png

