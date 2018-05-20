---
title: 'Kurz: Azure Active Directory integrace s iLMS | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 0281b0fca107908654dfac31014c79c187aa9b5d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Kurz: Azure Active Directory integrace s iLMS

V tomto kurzu zjistěte, jak integrovat iLMS s Azure Active Directory (Azure AD).

Integrace iLMS s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k iLMS
- Můžete povolit uživatelům, aby automaticky získat přihlášení k iLMS (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iLMS, potřebujete následující položky:

- Předplatné služby Azure AD
- ILMS jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání iLMS z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ilms-from-the-gallery"></a>Přidání iLMS z Galerie
Při konfiguraci integrace iLMS do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS iLMS z galerie.

**Pokud chcete přidat iLMS z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **iLMS**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_search.png)

5. Na panelu výsledků vyberte **iLMS**, pak klikněte na tlačítko **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s iLMS podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v iLMS je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v iLMS musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v iLMS.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iLMS, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele iLMS](#creating-an-ilms-test-user)**  – Pokud chcete mít protějšek Britta Simon v iLMS propojeném s Azure AD reprezentace jí.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci iLMS.

**Ke konfiguraci Azure AD jednotné přihlašování s iLMS, proveďte následující kroky:**

1. Na portálu Azure na **iLMS** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_samlbase.png)

3. Na **iLMS domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url.png)

    a. V **identifikátor** textovému poli, Vložit **identifikátor** hodnotu zkopírujete z **poskytovatele služeb** části SAML nastavení iLMS správce portálu.

    b. V **adresa URL odpovědi** textovému poli, Vložit **koncový bod (URL)** hodnotu zkopírujete z **poskytovatele služeb** části SAML nastavení v portálu pro správu iLMS následující opakování `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >Tato 123456 je příkladem hodnoty identifikátoru.

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url1.png)

    V **přihlašovací adresa URL** textovému poli, Vložit **koncový bod (URL)** hodnotu zkopírujete z **poskytovatele služeb** části SAML nastavení v portálu pro správu iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

5. Pokud chcete povolit zřizování JIT, očekává iLMS aplikace SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/4.png)
    
    Vytvoření **oddělení, oblast** a **dělení** atributy a přidejte název těchto atributů v iLMS. Všechny tyto atributy, které jsou uvedené výše se vyžadují.  

    > [!NOTE] 
    > Je třeba povolit **vytvořit uživatelský účet Un-recognized** v iLMS k mapování těchto atributů. Postupujte podle pokynů [zde](http://support.inspiredelearning.com/customer/portal/articles/2204526) , kde získáte představu na konfiguraci atributy.

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | dělení | User.Department |
    | oblast | User.state |
    | oddělení | user.jobtitle |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_04.png)

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **Ok**

7. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-iLMS-tutorial/tutorial_general_400.png)

9. V okně prohlížeče jiný web, přihlaste se k vaší **portál pro správu iLMS** jako správce.

10. Klikněte na tlačítko **SSO:SAML** pod **nastavení** otevřete SAML nastavení a proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/1.png) 

    a. Rozbalte **poskytovatele služeb** části a zkopírujte **identifikátor** a **koncový bod (URL)** hodnotu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/2.png) 

    b. V části **zprostředkovatele Identity** klikněte na tlačítko **importovat Metadata**.
    
    c. Vyberte **Metadata** soubor stažený z portálu Azure z **SAML podpisový certifikát** části.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Pokud chcete povolit zřizování můžete vytvořit účty iLMS pro zrušení JIT-rozpoznat uživatele, postupujte podle následujících kroků:
        
       - Zkontrolujte **vytvořte účet bez rozpoznaný uživatel**.
       
       ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Mapování atributů ve službě Azure AD s atributy v iLMS. Ve sloupci atributu zadejte atributy název nebo výchozí hodnotu.

    e. Přejděte na **obchodní pravidla** kartě a proveďte následující kroky: 
        
       ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/5.png)

       - Zkontrolujte **vytvořit Un-recognized oblastí, rozdělení a oddělení** vytvořit oblastí, rozdělení a oddělení, které už neexistují v době jednotné přihlašování.
        
       - Zkontrolujte **aktualizace uživatelského profilu při přihlášení** k určení, zda je aktualizovat profil uživatele s každou jednotné přihlašování. 
        
       - Pokud **"Aktualizace prázdné hodnoty pro jiný povinného pole v profilu uživatele"** zaškrtnutá možnost, volitelné profil pole, která jsou prázdné po přihlášení bude také způsobit iLMS profil uživatele obsahuje prázdné hodnoty těchto polí.
        
       - Zkontrolujte **odeslat E-mail s oznámením chyba** a zadejte e-mailu uživatele, kde chcete dostávat e-mailové oznámení chyby.

11. Klikněte na tlačítko **Uložit** tlačítko pro uložení nastavení.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/save.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-ilms-test-user"></a>Vytváření testovacího uživatele iLMS

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatelé jsou automaticky vytvořené v aplikaci. JIT bude fungovat, pokud jste klikli **vytvořit uživatelský účet Un-recognized** políčko během SAML nastavení konfigurace na portál pro správu iLMS.

Pokud potřebujete vytvořit uživatele s ručně, postupujte podle následujících kroků:

1. Přihlaste se k serveru vaší společnosti iLMS jako správce.

2. Klikněte na tlačítko **"Uživatel zaregistrovat"** pod **uživatelé** otevřete **registrovat uživatele** stránky. 
   
   ![Můžete přidat zaměstnance](./media/active-directory-saas-ilms-tutorial/3.png)

3. Na **"Registrovat uživatele"** proveďte následující kroky.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-ilms-tutorial/create_testuser_add.png)

    a. V **křestní jméno** textovému poli, název typu první Britta.
   
    b. V **příjmení** textovému poli, zadejte příjmení Simon.

    c. V **ID e-mailu** textovému poli, zadejte e-mailovou adresu účtu Britta Simon.

    d. V **oblast** rozevíracího seznamu, vyberte hodnotu pro oblast.

    e. V **dělení** rozevíracího seznamu, vyberte hodnotu pro dělení.

    f. V **oddělení** rozevíracího seznamu, vyberte hodnotu pro oddělení.

    g. Klikněte na **Uložit**.

    > [!NOTE] 
    > Můžete odeslat e-mailu registrace uživatele tak, že vyberete **odesílání pošty registrace** zaškrtávací políčko.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k iLMS Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon iLMS, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **iLMS**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici iLMS na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci iLMS.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_203.png

