---
title: 'Kurz: Azure Active Directory integrace s TimeOffManager | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 462a77e56f2dc28a3a3258ab44a1486a4dd257e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Kurz: Azure Active Directory integrace s TimeOffManager

V tomto kurzu zjistěte, jak integrovat TimeOffManager s Azure Active Directory (Azure AD).

Integrace TimeOffManager s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k TimeOffManager
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TimeOffManager (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TimeOffManager, potřebujete následující položky:

- Předplatné služby Azure AD
- TimeOffManager jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat TimeOffManager z Galerie
2. Konfigurace a otestování Azure AD jednotné přihlašování

## <a name="add-timeoffmanager-from-the-gallery"></a>Přidat TimeOffManager z Galerie
Při konfiguraci integrace TimeOffManager do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS TimeOffManager z galerie.

**Pokud chcete přidat TimeOffManager z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **TimeOffManager**, vyberte **TimeOffManager** z panelu výsledek a pak klikněte na tlačítko **přidat** tlačítko Přidat aplikaci.

    ![Přidat z Galerie](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s TimeOffManager podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v TimeOffManager je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v TimeOffManager musí navázat.

V TimeOffManager, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TimeOffManager, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele TimeOffManager](#create-a-timeoffmanager-test-user)**  – Pokud chcete mít protějšek Britta Simon v TimeOffManager propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci TimeOffManager.

**Ke konfiguraci Azure AD jednotné přihlašování s TimeOffManager, proveďte následující kroky:**

1. Na portálu Azure na **TimeOffManager** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Přihlášení na základě SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

3. Na **TimeOffManager domény a adresy URL** část, proveďte následující kroky:

     ![Část TimeOffManager domény a adresy URL](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL odpovědi. Můžete získat tuto hodnotu z **jednotného přihlašování na stránce nastavení** kterého se vysvětluje dále v kurzu nebo kontaktujte [tým podpory TimeOffManager](http://www.timeoffmanager.com/contact-us.aspx).
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Certifikát pro podpis SAML části](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

5. Cílem této části se popisují, jak uživatelům povolit ověřování na TimeOffManger ke svému účtu ve službě Azure AD využívající federaci na základě protokolu SAML.
    
    Aplikace TimeOffManger očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tento.

    ![atributy tokenu SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "atributy tokenu saml")
    
    | Název atributu | Hodnota atributu |
    | --- | --- |
    | FirstName |User.givenName |
    | Příjmení |User.Surname |
    | Email |User.Mail |
    
    a.  Pro každý řádek dat v předchozí tabulce, klikněte na tlačítko **přidat atribut uživatele**.
    
    ![atributy tokenu SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "atributy tokenu saml")
    
    ![atributy tokenu SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "atributy tokenu saml")
    
    b.  V **název atributu** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c.  V **hodnota atributu** textovému poli, vyberte hodnotu atributu zobrazený pro tento řádek.
    
    d.  Klikněte na tlačítko **OK**.
    
6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_400.png)

7. Na **TimeOffManager konfigurace** klikněte na tlačítko **konfigurace TimeOffManager** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![TimeOffManager konfigurační oddíl](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

8. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti TimeOffManager.

9. Přejděte na **účet \> účet možnosti \> jednotné přihlašování v nastavení**.
   
   ![Jednotné přihlašování v nastavení](./media/active-directory-saas-timeoffmanager-tutorial/ic795917.png "jednotné přihlašování v nastavení")
7. V **nastavení jednotného přihlašování** část, proveďte následující kroky:
   
   ![Jednotné přihlašování v nastavení](./media/active-directory-saas-timeoffmanager-tutorial/ic795918.png "jednotné přihlašování v nastavení")
   
   a. V poznámkovém bloku otevřete váš kódování base-64 kódovaného certifikátu, zkopírujte obsah ho do schránky a vložte celý certifikát do **certifikát X.509** textové pole.
   
   b. V **Idp vystavitele** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.
   
   c. V **adresu URL koncového bodu IdP** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
   
   d. Jako **vynutit SAML**, vyberte **ne**.
   
   e. Jako **automaticky vytvářet uživatelé**, vyberte **Ano**.
   
   f. V **adresy URL odhlašovací** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.
   
   g. Klikněte na tlačítko **uložit změny**.

11. V **jednotného přihlašování nastavení** stránky, zkopírujte hodnotu **adresa URL služby příjemce Assertion** a vložte jej do **adresa URL odpovědi** textového pole pod **TimeOffManager domény a adresy URL** části na portálu Azure. 

      ![Jednotné přihlašování v nastavení](./media/active-directory-saas-timeoffmanager-tutorial/ic795915.png "jednotné přihlašování v nastavení")

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Uživatelé a skupiny--> všechny uživatele](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Tlačítko Přidat](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Dialogové okno stránka uživatel](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-timeoffmanager-test-user"></a>Vytvoření zkušebního uživatele TimeOffManager

Pokud chcete povolit uživatelům Azure AD přihlášení do TimeOffManager, musí být zřízená k TimeOffManager.  

TimeOffManager podporuje jenom při zřizování uživatelů čas. Neexistuje žádná položka akce za vás.  

Uživatelé se přidávají automaticky během první přihlášení pomocí jednotného přihlašování na.

>[!NOTE]
>Můžete použít všechny ostatní TimeOffManager uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované TimeOffManager ke zřízení uživatelských účtů Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu TimeOffManager.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon TimeOffManager, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **TimeOffManager**.

    ![TimeOffManager v seznamu aplikací](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici TimeOffManager na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci TimeOffManager. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_203.png

