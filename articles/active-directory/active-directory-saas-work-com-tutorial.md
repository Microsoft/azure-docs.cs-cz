---
title: 'Kurz: Azure Active Directory integrace s Work.com | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: bfc76d05a52d0283e3367f9c98dc8ed427cbe592
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Kurz: Azure Active Directory integrace s Work.com

V tomto kurzu zjistěte, jak integrovat Work.com s Azure Active Directory (Azure AD).

Integrace Work.com s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Work.com
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Work.com (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Work.com, potřebujete následující položky:

- Předplatné služby Azure AD
- Work.com jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat Work.com z Galerie
2. Konfigurace a otestování Azure AD jednotné přihlašování

## <a name="add-workcom-from-the-gallery"></a>Přidat Work.com z Galerie
Při konfiguraci integrace Work.com do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Work.com z galerie.

**Pokud chcete přidat Work.com z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Work.com**, vyberte **Work.com** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Přidat z Galerie](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Work.com podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Work.com je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Work.com musí navázat.

V Work.com, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Work.com, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Work.com](#create-a-workcom-test-user)**  – Pokud chcete mít protějšek Britta Simon v Work.com propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Work.com.

>[!NOTE]
>Pokud chcete konfigurovat jednotné přihlašování, budete muset nastavit vlastní název domény Work.com ještě. Musíte definovat alespoň název domény, testovací název domény a nasadíte ho do celé organizace.

**Ke konfiguraci Azure AD jednotné přihlašování s Work.com, proveďte následující kroky:**

1. Na portálu Azure na **Work.com** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Přihlašování založené na SAML](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_samlbase.png)

3. Na **Work.com domény a adresy URL** část, proveďte následující kroky:

    ![Část Work.com domény a adresy URL](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Work.com klienta](https://help.salesforce.com/articleView?id=000159855&type=3) získat tuto hodnotu. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Certifikát pro podpis SAML části](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/active-directory-saas-work-com-tutorial/tutorial_general_400.png)

6. Na **Work.com konfigurace** klikněte na tlačítko **konfigurace Work.com** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Work.com konfigurační oddíl](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_configure.png) 
7. Přihlaste se ke klientovi Work.com jako správce.

8. Přejděte na **instalační program**.
   
    ![Instalační program](./media/active-directory-saas-work-com-tutorial/ic794108.png "instalační program")

9. V levém navigačním podokně v **Správa** klikněte na tlačítko **Správa domén** rozbalte související část, a potom klikněte na **Moje domény** otevřete **Moje domény** stránky. 
   
    ![Moje doména](./media/active-directory-saas-work-com-tutorial/ic767825.png "Moje doména")

10. Chcete-li ověřit, zda vaše doména byla nastavena správně, ujistěte se, že je v "**krok 4 nasazení uživatelům**" a zkontrolovat vaše "**Moje nastavení domény**".
   
    ![Domény uživatele nejsou nasazené](./media/active-directory-saas-work-com-tutorial/ic784377.png "doménou nasazení pro uživatele")

11. Přihlaste se ke klientovi Work.com.

12. Přejděte na **instalační program**.
    
    ![Instalační program](./media/active-directory-saas-work-com-tutorial/ic794108.png "instalační program")

13. Rozbalte **ovládacích prvků zabezpečení** nabídce a pak klikněte na tlačítko **nastavení jednotného přihlašování**.
    
    ![Jednotné přihlašování v nastavení](./media/active-directory-saas-work-com-tutorial/ic794113.png "jednotné přihlašování v nastavení")

14. Na **nastavení jednotného přihlašování** dialogové okno stránky, proveďte následující kroky:
    
    ![Povolit SAML](./media/active-directory-saas-work-com-tutorial/ic781026.png "povoleno SAML")
    
    a. Vyberte **povoleno SAML**.
    
    b. Klikněte na možnost **Nové**.

15. V **SAML jeden nastavení přihlášení** část, proveďte následující kroky:
    
    ![SAML jeden přihlašování nastavení](./media/active-directory-saas-work-com-tutorial/ic794114.png "SAML jeden přihlašování nastavení")
    
    a. V **název** textovému poli, zadejte název pro svou konfiguraci.  
       
    > [!NOTE]
    > Poskytuje hodnotu pro **název** automaticky vyplnit **název rozhraní API** textové pole.
    
    b. V **vystavitele** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.
    
    c. Chcete-li nahrát certifikát stažený z portálu Azure, klikněte na tlačítko **Procházet**.
    
    d. V **Entity Id** textovému poli, typ `https://salesforce-work.com`.
    
    e. Jako **typ Identity SAML**, vyberte **kontrolní výraz obsahuje ID federace z objektu uživatele**.
    
    f. Jako **umístění Identity SAML**, vyberte **identita je v elementu NameIdentfier příkaz subjektu**.
    
    g. V **adresu URL pro přihlášení zprostředkovatele Identity** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    h. V **adresa URL odhlašovací zprostředkovatele Identity** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.
    
    i. Jako **zprostředkovatele iniciované žádosti vazby služby**, vyberte **HTTP Post**.
    
    j. Klikněte na **Uložit**.

16. Portál classic Work.com, v levém navigačním podokně klikněte na tlačítko **Správa domén** rozbalte související část, a potom klikněte na **Moje domény** otevřete **Moje domény** stránky. 
    
    ![Moje doména](./media/active-directory-saas-work-com-tutorial/ic794115.png "Moje doména")

17. Na **Moje domény** stránky v **Branding přihlašovací stránky** klikněte na tlačítko **upravit**.
    
    ![Branding přihlašovací stránky](./media/active-directory-saas-work-com-tutorial/ic767826.png "Branding přihlašovací stránky")

14. Na **Branding přihlašovací stránky** stránky v **ověřovací služby** tématu, názvu vaší **nastavení jednotného přihlašování SAML** se zobrazí. Vyberte ji a pak klikněte na tlačítko **Uložit**.
    
    ![Branding přihlašovací stránky](./media/active-directory-saas-work-com-tutorial/ic784366.png "Branding přihlašovací stránky")

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-work-com-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Uživatelé a skupiny -> všichni uživatelé](./media/active-directory-saas-work-com-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Přidat](./media/active-directory-saas-work-com-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Dialogové okno stránka uživatel](./media/active-directory-saas-work-com-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-workcom-test-user"></a>Vytvoření zkušebního uživatele Work.com
Uživatelé Azure Active Directory se moct přihlásit musí být zřízená k Work.com. V případě Work.com zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:
1. Přihlásit se k serveru vaší společnosti Work.com jako správce.

2. Přejděte na **instalační program**.
   
    ![Instalační program](./media/active-directory-saas-work-com-tutorial/IC794108.png "instalační program")
3. Přejděte na **Správa uživatelů \> uživatelé**.
   
    ![Správa uživatelů](./media/active-directory-saas-work-com-tutorial/IC784369.png "Správa uživatelů")

4. Klikněte na tlačítko **nového uživatele**.
   
    ![Všichni uživatelé](./media/active-directory-saas-work-com-tutorial/IC794117.png "všichni uživatelé")

5. V části Upravit uživatele, proveďte následující kroky, v atributech platný Azure AD účtu chcete mají být zahrnuty do související textových polí:
   
    ![Úprava uživatele](./media/active-directory-saas-work-com-tutorial/ic794118.png "Úprava uživatele")
   
    a. V **křestní jméno** textovému poli, typ **křestní jméno** uživatele **Britta**.
    
    b. V **příjmení** textovému poli, typ **příjmení** uživatele **Simon**.
    
    c. V **Alias** textovému poli, typ **název** uživatele **BrittaS**.
    
    d. V **e-mailu** textovému poli, typ **e-mailová adresa** uživatele **Brittasimon@contoso.com**.
    
    e. V **uživatelské jméno** textové pole, zadejte uživatelské jméno uživatele jako **Brittasimon@contoso.com**.
    
    f. V **Přezdívka** textovému poli, zadejte **Přezdívka** uživatele **Simon**.
    
    g. Vyberte **Role**, **uživatelské licence pro**, a **profil**.
    
    h. Klikněte na **Uložit**.  
      
    > [!NOTE]
    > Držitel účtu Azure AD dostane e-mail zahrnutím odkazu pro potvrzení účtu před stane aktivní.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Work.com.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Work.com, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Work.com**.

    ![Work.com v seznamu aplikace](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Work.com na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Work.com.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_203.png

