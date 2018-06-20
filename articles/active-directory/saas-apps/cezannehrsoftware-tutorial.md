---
title: 'Kurz: Azure Active Directory integrace s HR softwaru Cezanne | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Cezanne HR softwarem a Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: c44d1219c9f352a868aff3305372537ecd7a45b7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225761"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Kurz: Azure Active Directory integrace s Cezanne HR softwaru

V tomto kurzu zjistěte, jak integrovat Cezanne HR softwaru s Azure Active Directory (Azure AD).

Integrace Cezanne HR softwaru s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k softwaru Cezanne oddělení lidských zdrojů.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k softwaru HR Cezanne (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cezanne HR softwaru, potřebujete následující položky:

- Předplatné služby Azure AD
- Cezanne HR softwaru jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání softwaru HR Cezanne z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Přidání softwaru HR Cezanne z Galerie
Při konfiguraci integrace Cezanne HR softwaru do služby Azure AD, potřebujete přidat Cezanne HR softwaru z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat Cezanne HR softwaru z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cezanne HR softwaru**, vyberte **Cezanne HR softwaru** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Cezanne HR softwaru v seznamu výsledků](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cezanne HR Software založený na testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Cezanne HR softwaru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Cezanne HR softwaru je nutné stanovit.

V softwaru Cezanne oddělení lidských zdrojů, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cezanne HR softwaru, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele HR softwaru Cezanne](#create-a-cezannehrsoftware-test-user)**  – Pokud chcete mít protějšek Britta Simon v Cezanne HR Software, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Cezanne HR softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s Cezanne HR softwarem, proveďte následující kroky:**

1. Na portálu Azure na **Cezanne HR softwaru** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Na **Cezanne HR softwaru domény a adresy URL** část, proveďte následující kroky:

    ![Cezanne HR softwaru domény a adresy URL jednotné přihlašování informace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. V **identifikátor** textovému poli, zadejte adresu URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL přihlašování a adresa URL odpovědi. Obraťte se na [tým podpory klientský Software HR Cezanne](https://cezannehr.com/services/support/) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

6. Na **Cezanne HR softwarové konfigurace** klikněte na tlačítko **konfigurace softwaru HR Cezanne** otevřete **konfigurovat přihlášení** okno.

    ![Konfigurace softwaru Cezanne personálního oddělení](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

7. Přejděte dolů k položce **Stručná referenční příručka** části. Kopírování **SAML jeden přihlašování adresa URL služby a SAML Entity ID** z **Stručná referenční příručka části.**

    ![Konfigurace softwaru Cezanne personálního oddělení](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

8. V okně prohlížeče jiných webových přihlašování ke klientovi Cezanne HR softwaru jako správce.

9. V levém navigačním podokně klikněte na tlačítko **nastavení systému**. Přejděte na **nastavení zabezpečení**. Potom přejděte na **konfigurace přihlášení**.

    ![Konfigurace straně jediné přihlášení na aplikaci](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

10. V **umožňují uživatelům přihlásit pomocí následující služby Jednotné přihlašování (SSO)** panel, zaškrtněte **SAML 2.0** a vyberte položku **Upřesnit konfiguraci** možnost.

    ![Konfigurace straně jediné přihlášení na aplikaci](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

11. Klikněte na tlačítko **přidat nové** tlačítko.

    ![Konfigurace straně jediné přihlášení na aplikaci](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

12. Proveďte následující kroky na **zprostředkovatelů IDENTITY SAML 2.0** části.

    ![Konfigurace straně jediné přihlášení na aplikaci](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Zadejte název zprostředkovatele Identity jako **zobrazovaný název**.

    b. V **identifikátor Entity** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure. 

    c. Změna **SAML vazby** na "POST".

    d. V **koncový bod služby tokenu zabezpečení** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    e. Do textového pole Název atributu ID uživatele, zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Klikněte na tlačítko **nahrát** ikonu, na kterou odešlete certifikát stažený z portálu Azure.
    
    g. Klikněte na tlačítko **OK**. 

13. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace straně jediné přihlášení na aplikaci](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Vytvoření zkušebního uživatele Cezanne HR softwaru

Pokud chcete povolit uživatelům Azure AD přihlášení do Cezanne HR softwaru, musí být zřízená do Cezanne HR softwaru. V případě Cezanne HR Software zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1.  Přihlaste se k serveru vaší společnosti Cezanne HR softwaru jako správce.

2.  V levém navigačním podokně klikněte na tlačítko **nastavení systému**. Přejděte na **Správa uživatelů**. Potom přejděte na **přidat nové uživatele**.

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "nového uživatele")

3.  Na **osoba podrobnosti** část, proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "nového uživatele")
    
    a. Nastavit **interní uživatele** jako OFF.
    
    b. V **křestní jméno** jako typ křestní jméno uživatele k textovému poli, **Britta**.  
 
    c. V **příjmení** jako typ příjmení uživatele k textovému poli, **Simon**.
    
    d. V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, Brittasimon@contoso.com.

4.  Na **informace o účtu** část, proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "nového uživatele")
    
    a. V **uživatelské jméno** jako typ e-mailu uživatele k textovému poli, Brittasimon@contoso.com.
    
    b. V **heslo** textovému poli, zadejte heslo uživatele.
    
    c. Vyberte **HR Professional** jako **Role zabezpečení**.
    
    d. Klikněte na **OK**.

5. Přejděte na **jednotné přihlašování** a vyberte **přidat nové** v **SAML 2.0 identifikátory** oblasti.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "uživatele")

6. Vyberte zprostředkovatele Identity pro **zprostředkovatele Identity** a do textového pole z **uživatelský identifikátor**, zadejte e-mailovou adresu účtu Britta Simon.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "uživatele")
    
7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "uživatele")

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Cezanne HR softwaru.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Cezanne HR softwaru, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Cezanne HR softwaru**.

    ![Odkaz Cezanne HR softwaru v seznamu aplikací](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Cezanne HR softwaru na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Cezanne HR softwaru.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png

