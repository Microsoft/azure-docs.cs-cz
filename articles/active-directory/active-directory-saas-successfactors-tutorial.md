---
title: 'Kurz: Azure Active Directory integrace s SuccessFactors | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 63ee7f7a08d16048eb3f0672cac4c2f41a3ed0f4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Kurz: Azure Active Directory integrace s SuccessFactors

V tomto kurzu zjistěte, jak integrovat SuccessFactors s Azure Active Directory (Azure AD).

Integrace SuccessFactors s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k SuccessFactors.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SuccessFactors (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SuccessFactors, potřebujete následující položky:

- Předplatné služby Azure AD
- SuccessFactors jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SuccessFactors z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-successfactors-from-the-gallery"></a>Přidání SuccessFactors z Galerie
Při konfiguraci integrace SuccessFactors do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS SuccessFactors z galerie.

**Pokud chcete přidat SuccessFactors z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SuccessFactors**, vyberte **SuccessFactors** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![SuccessFactors v seznamu výsledků](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s SuccessFactors podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v SuccessFactors je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v SuccessFactors musí navázat.

V SuccessFactors, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SuccessFactors, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SuccessFactors](#create-a-successfactors-test-user)**  – Pokud chcete mít protějšek Britta Simon v SuccessFactors propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SuccessFactors.

**Ke konfiguraci Azure AD jednotné přihlašování s SuccessFactors, proveďte následující kroky:**

1. Na portálu Azure na **SuccessFactors** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. Na **SuccessFactors domény a adresy URL** část, proveďte následující kroky:

    ![SuccessFactors domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory SuccessFactors klienta](https://www.successfactors.com/en_us/support.html) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-successfactors-tutorial/tutorial_general_400.png)
    
6. Na **SuccessFactors konfigurace** klikněte na tlačítko **konfigurace SuccessFactors** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_configure.png) 

7. V okně prohlížeče jiný web, přihlaste se k vaší **portál pro správu SuccessFactors** jako správce.
    
8. Navštivte **zabezpečení aplikací** a nativní pro **jeden znak na funkci**. 

9. Umístění všechny hodnoty v **resetovat tokenu** a klikněte na tlačítko **uložit tokenu** umožňující jednotného přihlašování SAML.
   
    ![Konfigurace jednotného přihlašování na straně aplikace][11]

    > [!NOTE] 
    > Tato hodnota se používá jako přepínač zapnout nebo vypnout. Pokud je uloženo žádnou hodnotu, jednotné přihlašování SAML je ON. Pokud je uloženo na prázdnou hodnotu jednotné přihlašování SAML je VYPNUTÝ.

10. Nativní na následující snímek obrazovky a proveďte následující akce:
   
    ![Konfigurace jednotného přihlašování na straně aplikace][12]
   
    a. Vyberte **jednotné přihlašování SAML v2** přepínač
   
    b. Nastavte **SAML potvrzující strany název**(například SAML vystavitele + název společnosti).
   
    c. V **URL vystavitele** textovému poli, Vložit **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure.
   
    d. Vyberte **odpovědi (zákazníka generované nebo IdP/přístupový bod)** jako **vyžadují povinné podpis**.
   
    e. Vyberte **povoleno** jako **povolit SAML příznak**.
   
    f. Vyberte **ne** jako **podpisu požadavku přihlášení (SF generované/SP/RP)**.
   
    g. Vyberte **profil prohlížeče nebo Pozálohovacího** jako **SAML profil**.
   
    h. Vyberte **ne** jako **vynutit období platný certifikát**.
   
    i. Kopírovat obsah souboru certifikátu stažený z portálu Azure a vložte ji do **ověření certifikátu SAML** textové pole.

    > [!NOTE] 
    > Obsahu certifikátu musí začínat certifikátu a koncovou značkou certifikátu.

11. Přejděte do SAML V2 a pak proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování na straně aplikace][13]
   
    a. Vyberte **Ano** jako **podporu spouštěná SP globální odhlášení**.
   
    b. V **globální odhlášení adresa URL služby (LogoutRequest cíl)** textovému poli, Vložit **Sign-Out URL** hodnotu, která jste zkopírovali formuláři na portálu Azure.
   
    c. Vyberte **ne** jako **vyžadují sp musí zašifrovat všechny element NameID**.
   
    d. Vyberte **neurčené** jako **NameID formátu**.
   
    e. Vyberte **Ano** jako **povolit sp iniciované přihlášení (AuthnRequest)**.
   
    f. V **odeslán požadavek na jako vydavatel společnosti** textovému poli, vložte **SAML jeden přihlašování adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure.

12. Proveďte tyto kroky, pokud chcete, aby uživatelská jména přihlášení malá a velká písmena.
   
    ![Konfigurovat jednotné přihlašování][29]
    
    a. Navštivte **nastavení společnosti**(u dolního).
   
    b. Zaškrtněte políčko téměř **povolit uživatelské jméno bez rozlišování**.
   
    c.Click **Uložit**.
   
    > [!NOTE] 
    > Pokud se pokusíte povolit tuto funkci, systém zkontroluje, pokud vytvoří duplicitní SAML přihlašovací jméno. Například pokud zákazník má uživatelských jmen uživatel1 a user1. Pořízení rychle rozlišování umožňuje tyto duplikáty. Systém vám dává chybovou zprávu a není funkce. Zákazník musí změnit jeden z uživatelských jménech tak, aby je napsán jiný.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-successfactors-test-user"></a>Vytvoření zkušebního uživatele SuccessFactors

Pokud chcete povolit uživatelům Azure AD přihlášení k SuccessFactors, musí být zřízená do SuccessFactors.  
V případě SuccessFactors zřizování je ruční úloha.

Chcete-li získat uživatelé vytvoření ve SuccessFactors, budete muset kontaktovat [tým podpory SuccessFactors](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu SuccessFactors.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon SuccessFactors, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SuccessFactors**.

    ![V seznamu aplikací na SuccessFactors odkaz](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici SuccessFactors na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci SuccessFactors.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_203.png

