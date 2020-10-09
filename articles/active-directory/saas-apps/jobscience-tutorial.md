---
title: 'Kurz: Azure Active Directory integrace s Jobscience | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jobscience.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 715e99f07d0fcbc77fd1769e5da1cae6b46ac97e
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850730"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Kurz: Azure Active Directory integrace s Jobscience

V tomto kurzu se dozvíte, jak integrovat Jobscience s Azure Active Directory (Azure AD).

Integrace Jobscience s Azure AD poskytuje následující výhody:

- Můžete řídit v Azure AD, kteří mají přístup k Jobscience.
- Uživatelům můžete povolit, aby se k účtům Azure AD automaticky přihlásili k Jobscience (jednotné přihlašování).
- Účty můžete spravovat v jednom centrálním umístění – Azure Portal

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Jobscience potřebujete následující položky:

- Předplatné Azure AD
- Předplatné s povoleným Jobsciencem jednotným přihlašováním

> [!NOTE]
> K otestování kroků v tomto kurzu nedoporučujeme používat produkční prostředí.

K otestování kroků v tomto kurzu byste měli postupovat podle těchto doporučení:

- Nepoužívejte své provozní prostředí, pokud není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete získat měsíční zkušební verzi: [zkušební nabídka](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu otestujete jednotné přihlašování Azure AD v testovacím prostředí. Scénář, který je popsaný v tomto kurzu, se skládá ze dvou hlavních stavebních bloků:

1. Přidání Jobscience z Galerie
1. Konfigurace a testování jednotného přihlašování Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Přidání Jobscience z Galerie
Pokud chcete nakonfigurovat integraci Jobscience do služby Azure AD, musíte přidat Jobscience z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Jobscience z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** . 

    ![Active Directory][1]

1. Přejděte k **podnikovým aplikacím**. Pak přejdete na **všechny aplikace**.

    ![Snímek obrazovky zobrazuje Azure Portal podnikové aplikace vybrané v části spravovat s vybranými všemi aplikacemi.][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Snímek obrazovky zobrazuje vybranou novou aplikaci.][3]

1. Do vyhledávacího pole zadejte **Jobscience**.

    ![Snímek obrazovky ukazuje přidání z galerie s Jobscience zadaným.](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Na panelu výsledků vyberte **Jobscience**a pak klikněte na **Přidat** tlačítko pro přidání aplikace.

    ![Snímek obrazovky zobrazuje výsledky, které zahrnují Jobscience.](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD
V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Jobscience na základě testovacího uživatele s názvem "Britta Simon".

Aby se jednotné přihlašování fungovalo, musí Azure AD zjistit, co se uživatel partnerského systému v Jobscience uživateli v Azure AD. Jinými slovy, je třeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Jobscience.

V Jobscience přiřaďte hodnotu **uživatelského jména** ve službě Azure AD jako hodnotu uživatelského **jména** , aby se mohl vytvořit vztah propojení.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Jobscience, musíte dokončit tyto stavební bloky:

1. **[Konfiguruje se jednotné přihlašování Azure AD](#configuring-azure-ad-single-sign-on)** , které umožní uživatelům používat tuto funkci.
1. **[Vytvoření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)** – testování jednotného přihlašování Azure AD pomocí Britta Simon
1. **[Vytvoření testovacího uživatele Jobscience](#creating-a-jobscience-test-user)** , který má protějšek Britta Simon v Jobscience, který je propojený s reprezentací uživatele Azure AD.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)** – povolení Britta Simon pro použití jednotného přihlašování Azure AD
1. **[Testování jednotného přihlašování](#testing-single-sign-on)** – ověřte, zda konfigurace funguje.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfiguruje se jednotné přihlašování Azure AD.

V této části povolíte jednotné přihlašování Azure AD v Azure Portal a nakonfigurujete jednotné přihlašování v aplikaci Jobscience.

**Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Jobscience, proveďte následující kroky:**

1. V Azure Portal na stránce integrace aplikací **Jobscience** klikněte na **jednotné přihlašování**.

    ![Snímek obrazovky s vybraným možnostmi v části spravovat v Azure Portal zobrazuje jednotné přihlašování.][4]

1. V dialogovém okně **jednotného přihlašování** vyberte **režim** jako    **přihlašování založené na SAML** pro povolení jednotného přihlašování.
 
    ![Snímek obrazovky ukazuje vybraný režim přihlašování založený na SAML.](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. V části **doména a adresy URL Jobscience** proveďte následující kroky:

    ![Snímek obrazovky ukazuje přihlášení U R L.](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL Sign-On. Tuto hodnotu získáte v [Jobscience týmu podpory klientů](http://www.jobscience.com/support) nebo v profilu jednotného přihlašování, který se vysvětluje později v tomto kurzu. 
 
1. V části **podpisový certifikát SAML** klikněte na **certifikát (Base64)** a pak soubor certifikátu uložte do počítače.

    ![Snímek obrazovky se zobrazí v podokně podpisový certifikát SAML, kde si můžete stáhnout certifikát.](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klikněte na tlačítko **Uložit** .

    ![Snímek obrazovky se zobrazením tlačítka Uložit.](./media/jobscience-tutorial/tutorial_general_400.png)

1. V části **Konfigurace Jobscience** kliknutím na **Konfigurovat Jobscience** otevřete okno **Konfigurovat přihlašování** . Zkopírujte adresu **URL pro odhlášení, ID entity SAML a adresu URL služby Single Sign-On SAML** z **oddílu rychlé reference.**

    ![Snímek obrazovky se zobrazí okno Konfigurace Jobscience.](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Přihlaste se k webu Jobscience společnosti jako správce.

1. Přejít na **Nastavení**.
   
   ![Snímek obrazovky zobrazuje položku nastavení vaší společnosti.](./media/jobscience-tutorial/IC784358.png "Nastavení")

1. V levém navigačním podokně v části **Spravovat** kliknutím na **Správa domény** rozbalte související část a potom kliknutím na **moje doména** otevřete stránku **moje doména** . 
   
   ![Moje doména](./media/jobscience-tutorial/ic767825.png "Moje doména")

1. Pokud chcete ověřit, jestli je vaše doména nastavená správně, ujistěte se, že je v**kroku 4 nasazená pro uživatele**, a zkontrolujte**Nastavení moje doména**.

    ![Doména nasazená pro uživatele](./media/jobscience-tutorial/ic784377.png "Doména nasazená pro uživatele")

1. Na webu Jobscience společnosti klikněte na **ovládací prvky zabezpečení**a pak klikněte na **Nastavení jednoho Sign-On**.
    
    ![Snímek obrazovky ukazuje nastavení jednoho Sign-On vybrané z ovládacích prvků zabezpečení.](./media/jobscience-tutorial/ic784364.png "Zabezpečovací mechanismy")

1. V části **nastavení s jedním Sign-On** proveďte následující kroky:
    
    ![Nastavení jednoho Sign-On](./media/jobscience-tutorial/ic781026.png "Nastavení jednoho Sign-On")
    
    a. Vyberte **povoleno SAML**.

    b. Klikněte na **Nový**.

1. V dialogovém okně pro **úpravu nastavení jediného Sign-On SAML** proveďte následující kroky:
    
    ![Nastavení Single Sign-On SAML](./media/jobscience-tutorial/ic784365.png "Nastavení Single Sign-On SAML")
    
    a. Do textového pole **název** zadejte název konfigurace.

    b. Do textového pole **vystavitele** vložte hodnotu **ID entity SAML**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **ID entity** zadejte `https://salesforce-jobscience.com`

    d. Klikněte na **Procházet** a nahrajte svůj certifikát Azure AD.

    e. Jako **typ identity SAML**vyberte **kontrolní výraz obsahuje ID federace z objektu User**.

    f. Jako **umístění identity SAML**, vyberte **identita je v elementu NameIdentfier příkazu Subject**.

    například Do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu **adresy url služby Single Sign-On služby SAML**, kterou jste zkopírovali z Azure Portal.

    h. Do textového pole **Adresa URL pro odhlášení zprostředkovatele identity** vložte hodnotu **adresy URL**pro odhlášení, kterou jste zkopírovali z Azure Portal.

    i. Klikněte na **Uložit**.

1. V levém navigačním podokně v části **Spravovat** kliknutím na **Správa domény** rozbalte související část a potom kliknutím na **moje doména** otevřete stránku **moje doména** . 
    
    ![Moje doména](./media/jobscience-tutorial/ic767825.png "Moje doména")

1. Na stránce **moje doména** v části **branding přihlašovací stránky** klikněte na **Upravit**.
    
    ![Snímek obrazovky s tlačítkem Upravit se zobrazí v části branding přihlašovací stránky.](./media/jobscience-tutorial/ic767826.png "Branding přihlašovací stránky")

1. Na stránce **značky přihlašovací stránky** v části **ověřovací služba** se zobrazí název vašeho **nastavení jednotného přihlašování SAML** . Vyberte ji a pak klikněte na **Uložit**.
    
    ![Snímek obrazovky s vybraným OOP se zobrazí v části branding přihlašovací stránky.](./media/jobscience-tutorial/ic784366.png "Branding přihlašovací stránky")

1. Pokud chcete získat adresu URL jednotného přihlašování inicializované v rámci SP, klikněte na **nastavení jednotného přihlašování** v části nabídky **ovládací prvky zabezpečení** .

    ![Snímek obrazovky ukazuje, jak spravovat ovládací prvky zabezpečení s vybraným nastavením jednoho Sign-On.](./media/jobscience-tutorial/ic784368.png "Zabezpečovací mechanismy")
    
    Klikněte na profil jednotného přihlašování, který jste vytvořili v předchozím kroku. Tato stránka zobrazuje adresu URL jednotného přihlašování vaší společnosti (například `https://companyname.my.salesforce.com?so=companyid` .    

> [!TIP]
> Při nastavování aplikace si teď můžete přečíst stručnou verzi těchto instrukcí v rámci [Azure Portal](https://portal.azure.com).  Po přidání této aplikace z části **Active Directory > Enterprise** Apps stačí kliknout na kartu **jednotného přihlašování** a získat přístup k integrované dokumentaci v části **Konfigurace** v dolní části. Další informace o funkci integrovaná dokumentace si můžete přečíst tady: [dokumentace k Azure AD Embedded]( https://go.microsoft.com/fwlink/?linkid=845985) .
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD
Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **Azure Portal**v levém navigačním podokně klikněte na ikonu **Azure Active Directory** .

    ![Snímek obrazovky ukazuje ikonu Azure A D v Azure Portal.](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Pokud chcete zobrazit seznam uživatelů, přejděte na **Uživatelé a skupiny** a klikněte na **Všichni uživatelé**.
    
    ![Snímek obrazovky se zobrazí uživatelé a skupiny vybrané v nabídce spravovat s vybranými možnostmi všichni uživatelé.](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít dialogové okno **uživatel** , klikněte na tlačítko **Přidat** v horní části dialogového okna.
 
    ![Snímek obrazovky zobrazující tlačítko Přidat pro otevření dialogového okna uživatele](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Na stránce **uživatelský** dialog proveďte následující kroky:
 
    ![Snímek obrazovky se zobrazí dialogové okno uživatele, kde můžete zadat hodnoty v tomto kroku.](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Do textového pole **název** zadejte **BrittaSimon**.

    b. Do textového pole **uživatelské jméno** zadejte **e-mailovou adresu** BrittaSimon.

    c. Vyberte možnost **Zobrazit heslo** a zapište hodnotu **hesla**.

    d. Klikněte na **Vytvořit**.
 
### <a name="creating-a-jobscience-test-user"></a>Vytváření Jobscience testovacího uživatele

Aby se uživatelé Azure AD mohli přihlásit k Jobscience, musí se zřídit v Jobscience. V případě Jobscience je zřizování ručním úkolem.

>[!NOTE]
>K zřizování Azure Active Directorych uživatelských účtů můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Jobscience nebo rozhraní API poskytovaná Jobscience.
>  
        
**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se k webu společnosti **Jobscience** jako správce.

1. Přejít na nastavení.
   
   ![Snímek obrazovky se zobrazí položka nastavení.](./media/jobscience-tutorial/ic784358.png "Nastavení")
1. Přejít na **Správa uživatelů \> **.
   
   ![Uživatelé](./media/jobscience-tutorial/ic784369.png "Uživatelé")
1. Klikněte na **Nový uživatel**.
   
   ![Všichni uživatelé](./media/jobscience-tutorial/ic784370.png "Všichni uživatelé")
1. V dialogovém okně **Upravit uživatele** proveďte následující kroky:
   
   ![Úprava uživatele](./media/jobscience-tutorial/ic784371.png "Úprava uživatele")
   
   a. Do textového pole **jméno a příjmení** zadejte jméno uživatele, jako je třeba Britta.
   
   b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je Simon.
   
   c. Do textového pole **alias** zadejte název aliasu uživatele, jako je Brittas.

   d. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com .

   e. Do textového pole **uživatelské jméno** zadejte uživatelské jméno jako uživatel Brittasimon@contoso.com .

   f. Do textového pole **Přezdívka jméno** zadejte Přezdívka uživatele, jako je Simon.

   například Klikněte na **Uložit**.

    
> [!NOTE]
> Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz k potvrzení jeho účtu předtím, než se aktivuje.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazuje se testovací uživatel Azure AD.

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Jobscience.

![Snímek obrazovky se zobrazeným názvem účtu.][200] 

**Pokud chcete přiřadit Britta Simon k Jobscience, proveďte následující kroky:**

1. V Azure Portal otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře a přejděte do části **podnikové aplikace** a klikněte na **všechny aplikace**.

    ![Snímek obrazovky zobrazuje podnikové aplikace v nabídce Azure Portal se všemi vybranými aplikacemi.][201] 

1. V seznamu aplikace vyberte **Jobscience**.

    ![Snímek obrazovky zobrazuje Jobscience vybrané.](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. V nabídce na levé straně klikněte na **Uživatelé a skupiny**.

    ![Snímek obrazovky se zobrazí uživatelé a skupiny vybrané z nabídky Azure Portal.][202] 

1. Klikněte na tlačítko **Přidat** . Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Snímek obrazovky zobrazující tlačítko Přidat, které se používá k přidání přiřazení.][203]

1. V dialogu **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** .

1. Klikněte na tlačítko **Vybrat** v dialogovém okně **Uživatelé a skupiny** .

1. Klikněte na tlačítko **přiřadit** v dialogovém okně **Přidat přiřazení** .
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Jobscience, měli byste se automaticky přihlásili ke své aplikaci Jobscience.
Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

