---
title: 'Kurz: Azure Active Directory integrace s TalentLMS | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 1c81d3e01170b2d134f83551911f9fcbd6e5f6e9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Kurz: Azure Active Directory integrace s TalentLMS

V tomto kurzu zjistěte, jak integrovat TalentLMS s Azure Active Directory (Azure AD).

Integrace TalentLMS s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k TalentLMS
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TalentLMS (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TalentLMS, potřebujete následující položky:

- Předplatné služby Azure AD
- TalentLMS jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání TalentLMS z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-talentlms-from-the-gallery"></a>Přidání TalentLMS z Galerie
Při konfiguraci integrace TalentLMS do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS TalentLMS z galerie.

**Pokud chcete přidat TalentLMS z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **TalentLMS**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_search.png)

5. Na panelu výsledků vyberte **TalentLMS**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s TalentLMS podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v TalentLMS je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v TalentLMS musí navázat.

V TalentLMS, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TalentLMS, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele TalentLMS](#creating-a-talentlms-test-user)**  – Pokud chcete mít protějšek Britta Simon v TalentLMS propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci TalentLMS.

**Ke konfiguraci Azure AD jednotné přihlašování s TalentLMS, proveďte následující kroky:**

1. Na portálu Azure na **TalentLMS** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_samlbase.png)

3. Na **TalentLMS domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.TalentLMSapp.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `http://<tenant-name>.talentlms.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory TalentLMS klienta](https://www.talentlms.com/contact) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnotu z certifikátu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-talentlms-tutorial/tutorial_general_400.png)

6. Na **TalentLMS konfigurace** klikněte na tlačítko **konfigurace TalentLMS** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_configure.png)  

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti TalentLMS jako správce.

8. V **účet & nastavení** klikněte na položku **uživatelé** karta.
   
    ![Účet & nastavení](./media/active-directory-saas-talentlms-tutorial/IC777296.png "účet a nastavení")

9. Klikněte na tlačítko **jednotné přihlašování (SSO)**,

10. V části jednotné přihlašování proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/active-directory-saas-talentlms-tutorial/IC777297.png "jednotného přihlašování")   

    a. Z **typ jednotného přihlašování k integraci** seznamu, vyberte **SAML 2.0**.

    b. V **zprostředkovatele Identity (IDP)** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.
 
    c. Vložení **kryptografický otisk** hodnotu z portálu Azure do **otisků prstů certifikátů** textové pole.    

    d.  V **vzdáleného přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
 
    e. V **vzdálenou adresou URL odhlašování** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    f. Vyplňte následující údaje: 

    * V **TargetedID** textovému poli, typu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
     
    * V **křestní jméno** textovému poli, typu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    
    * V **příjmení** textovému poli, typu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
    
    * V **e-mailu** textovému poli, typu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    
11. Klikněte na **Uložit**.
 
> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-talentlms-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-talentlms-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-talentlms-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-talentlms-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-talentlms-test-user"></a>Vytvoření zkušebního uživatele TalentLMS

Pokud chcete povolit uživatelům Azure AD přihlášení k TalentLMS, musí být zřízená do TalentLMS. V případě TalentLMS zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **TalentLMS** klienta.

2. Klikněte na tlačítko **uživatelé**a potom klikněte na **přidat uživatele**.

3. Na **přidat uživatele** dialogové okno stránky, proveďte následující kroky:
   
    ![Přidat uživatele](./media/active-directory-saas-talentlms-tutorial/IC777299.png "přidat uživatele")  

    a. V **křestní jméno** textovému poli, zadejte jméno uživatele jako **Britta**.

    b. V **příjmení** textovému poli, zadejte příjmení uživatele jako **Simon**.
 
    c. V **e-mailová adresa** textovému poli, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

    d. Klikněte na tlačítko **přidat uživatele**.

>[!NOTE]
>Můžete použít všechny ostatní TalentLMS uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované TalentLMS zřídit AAD uživatelské účty.
 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu TalentLMS.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon TalentLMS, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **TalentLMS**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici TalentLMS na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci TalentLMS

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_203.png

