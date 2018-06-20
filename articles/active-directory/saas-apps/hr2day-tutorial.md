---
title: 'Kurz: Azure Active Directory integrace s HR2day podle Merces | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HR2day podle Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: a63ef3b089ec80da6a75aaaf8a4bb2454fec6cf7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224177"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Kurz: Azure Active Directory integrace s HR2day podle Merces

V tomto kurzu zjistěte, jak integrovat HR2day podle Merces s Azure Active Directory (Azure AD).

Integrace HR2day podle Merces s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k HR2day podle Merces.
- Můžete povolit uživatelům automaticky získat přihlášení k HR2day podle Merces s účty služby Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s HR2day podle Merces, potřebujete následující položky:

- Předplatné služby Azure AD.
- HR2day podle Merces jednotné přihlašování povolené předplatné.

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Získání [jeden měsíc bezplatnou zkušební verzi Azure AD](https://azure.microsoft.com/pricing/free-trial/) Pokud ještě nemáte ho.  

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je zde uvedených zahrnuje dva hlavní stavební bloky:

1. Přidání HR2day podle Merces z galerie.
2. Konfigurace a testování Azure AD jednotného přihlašování.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Přidat HR2day podle Merces z Galerie
Při konfiguraci integrace HR2day podle Merces do služby Azure AD přidáte do seznamu spravovaných aplikací SaaS HR2day podle Merces z galerie.

**Pokud chcete přidat HR2day podle Merces z galerie, proveďte následující kroky:**

1. V [portál Azure](https://portal.azure.com), na levém navigačním podokně, vyberte **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **HR2day podle Merces**.

    ![Vytváření testovacího uživatele Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. Na panelu výsledků vyberte **HR2day podle Merces**a pak vyberte **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s HR2day podle Merces podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, kdo příslušného uživatele v HR2day podle Merces je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele Azure AD a související uživatelské v HR2day podle Merces.

V HR2day podle Merces přiřadit **uživatelské jméno** ve službě Azure AD **uživatelské jméno** k vytvoření relace.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s HR2day podle Merces, je třeba dokončit následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on): Povolit uživatelům tuto funkci používat.
2. [Vytvořit testovací uživatele Azure AD](#creating-an-azure-ad-test-user): Test Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření HR2day uživatelem Merces testovací](#creating-an-hr2day-by-merces-test-user): vytvoření protějšek Britta Simon v HR2day podle Merces propojeném s Azure AD reprezentace daného uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user): Povolit Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#testing-single-sign-on): Ověřte, zda funguje konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaše HR2day Merces aplikací.

**Ke konfiguraci Azure AD jednotné přihlašování s HR2day podle Merces, proveďte následující kroky:**

1. Na portálu Azure na **HR2day podle Merces** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Pro povolení jednotného přihlašování, v **jednotného přihlašování** dialogové okno, vyberte **režimu** jako **na základě SAML přihlašování**.
 
    ![Konfigurovat jednotné přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. V **HR2day Merces domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL pomocí následujícího vzorce: `https://<tenantname>.force.com/<instancename>`.

    b. V **identifikátor** pole, zadejte adresu URL pomocí následujícího vzorce: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se [HR2day tým podpory klienta Merces](mailto:servicedesk@merces.nl) k získání těchto hodnot. 
 


4. Na **SAML podpisový certifikát** vyberte **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. Tato část popisuje, jak uživatelům povolit ověřování na HR2day podle Merces ke svému účtu ve službě Azure AD. Budou to provedete pomocí federace, která je založená na protokolu SAML.

    Vaše HR2day aplikací Merces očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do tokenu SAML. Následující snímek obrazovky ukazuje příklad tohoto objektu. 

    ![Konfigurovat jednotné přihlašování](./media/hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Před konfigurací kontrolního výrazu SAML, bude nutné se obrátit [HR2day tým podpory Merces klienta](mailto:servicedesk@merces.nl) a požadovat hodnotu atributu jedinečný identifikátor pro vašeho klienta. Je nutné tuto hodnotu pro dokončení kroků v další části. 

6. V **jednotného přihlašování** dialogu **uživatelské atributy** nakonfigurujte atribut tokenu SAML, jak je znázorněno na následujícím obrázku. Proveďte následující kroky.
    
      | Název atributu    |   Hodnota atributu |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    
      a. Chcete-li otevřít **přidat atribut** dialogovém okně, vyberte **přidat atribut**.

    ![Konfigurovat jednotné přihlašování](./media/hr2day-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/hr2day-tutorial/tutorial_attribute_05.png)

    b. V **název** zadejte **ATTR_LOGINCLAIM**.

    c. Z **hodnotu** seznamu, vyberte **Join()**.

    d. Z **řetězec1** seznamu, vyberte **user.mail**.

    e. Pro **řetězec2**, zadejte jedinečný identifikátor, který zajišťuje HR2day týmu.

    f. V **oddělovače** zadejte **\@**.
    
    g. Vyberte **Ok**.

7. Vyberte tlačítko **Uložit**.

    ![Konfigurovat jednotné přihlašování](./media/hr2day-tutorial/tutorial_general_400.png)

8. V **HR2day Merces konfigurace** vyberte **konfigurace HR2day podle Merces** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out URL**, **SAML Entity ID**, a **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka** části.

    ![Konfigurovat jednotné přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. Chcete-li nakonfigurovat jednotné přihlašování pro aplikace, obraťte se [HR2day tým podpory klienta Merces](mailTo:servicedesk@merces.nl). Připojení stažené **Certificate(Base64)** souborů k e-mailu. Zadat taky **Sign-Out URL**, **SAML Entity ID**, a **SAML jeden přihlašování adresa URL služby** tak, aby mohly být konfigurovány pro integraci jednotné přihlašování.

    > [!NOTE]
    >Merces týmu zmínili, že tato integrace vyžaduje ID Entity na hodnotu se vzorem **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě. Přejděte k embedded dokumentace prostřednictvím **konfigurace** v dolní části. Si můžete přečíst informace o funkci embedded dokumentace v [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, na levém navigačním podokně, vyberte **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/hr2day-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom vyberte **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/hr2day-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/hr2day-tutorial/create_aaduser_03.png) 

4. V **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/hr2day-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla**a zapište si heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Vytvoření HR2day Merces testovací uživatel

Cílem této části je vytvoření uživatele volá Britta Simon v HR2day Merces. Chcete-li přidat uživatele v účtu HR2day, pracovat s [HR2day tým podpory klienta Merces](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživateli, obraťte se [HR2day tým podpory klienta Merces](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k HR2day podle Merces Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon HR2day podle Merces, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikace, přejděte do zobrazení adresáře a potom přejděte na **podnikové aplikace, které**. Potom vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **HR2day podle Merces**.

    ![Konfigurovat jednotné přihlašování](./media/hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Vyberte **přidat** tlačítko. Potom v **přidat přiřazení** dialogové okno, vyberte **uživatelů a skupin**.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** v dialogovém **uživatelé** seznamu, vyberte **Britta Simon**.

6. Klikněte **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je testování Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.  

Když vyberete HR2day podle Merces dlaždice na přístupovém panelu, budete automaticky získat přihlášení k vaší HR2day Merces aplikací.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o tom, jak integrovat SaaS aplikací s Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hr2day-tutorial/tutorial_general_01.png
[2]: ./media/hr2day-tutorial/tutorial_general_02.png
[3]: ./media/hr2day-tutorial/tutorial_general_03.png
[4]: ./media/hr2day-tutorial/tutorial_general_04.png

[100]: ./media/hr2day-tutorial/tutorial_general_100.png

[200]: ./media/hr2day-tutorial/tutorial_general_200.png
[201]: ./media/hr2day-tutorial/tutorial_general_201.png
[202]: ./media/hr2day-tutorial/tutorial_general_202.png
[203]: ./media/hr2day-tutorial/tutorial_general_203.png

