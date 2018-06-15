---
title: 'Kurz: Azure Active Directory integrace s ServiceChannel | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ServiceChannel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.openlocfilehash: 2b1c7461cb1ed232290cf78a03449611f9ef95da
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349647"
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Kurz: Azure Active Directory integrace s ServiceChannel

V tomto kurzu zjistěte, jak integrovat ServiceChannel s Azure Active Directory (Azure AD).

Integrace ServiceChannel s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ServiceChannel
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ServiceChannel (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ServiceChannel, potřebujete následující položky:

- Předplatné služby Azure AD
- ServiceChannel jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ServiceChannel z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-servicechannel-from-the-gallery"></a>Přidání ServiceChannel z Galerie
Při konfiguraci integrace ServiceChannel do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS ServiceChannel z galerie.

**Pokud chcete přidat ServiceChannel z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **ServiceChannel**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_000.png)

5. Na panelu výsledků vyberte **ServiceChannel**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ServiceChannel podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ServiceChannel je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v ServiceChannel musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v ServiceChannel.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ServiceChannel, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ServiceChannel](#creating-a-servicechannel-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v aplikaci ServiceChannel.

**Ke konfiguraci Azure AD jednotné přihlašování s ServiceChannel, proveďte následující kroky:**

1. Na portálu Azure Management portal na **ServiceChannel** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování na.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_01.png)

3. Na **ServiceChannel domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. V **identifikátor** textovému poli, zadejte hodnotu jako: `http://adfs.<domain>.com/adfs/service/trust`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE] 
    > Upozorňujeme, že tyto nejsou skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečným identifikátorem a adresa URL odpovědi. Zde, doporučujeme vám použít jedinečnou hodnotu řetězce v identifikátoru. Obraťte se na [tým podpory ServiceChannel](https://servicechannel.zendesk.com/hc/en-us) k získání těchto hodnot.

4. Aplikace ServiceChannel očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tento. **NameIdentifier (identifikátor uživatele)** je povinný jenom deklarace identity a výchozí hodnota je **user.userprincipalname** ale ServiceChannel očekává to nejde mapovat s **user.mail**. Pokud máte v úmyslu povolit zřizování uživatelů jenom v době, by měl jak je uvedeno níže přidejte následující deklarace identity. **Role** deklarace identity musí být namapovaný na **user.assignedroles** obsahující roli uživatele.  

    Najdete Průvodce ServiceChannel [sem](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example) další pokyny na deklaracích identity.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Kliknutím na [sem](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/) vědět, jak nakonfigurovat **Role** ve službě Azure AD

5. V **uživatelské atributy** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** a nastavit atributy.

    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | Role| user.assignedroles |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **Ok**
    
6. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_05.png) 

7. Klikněte na **Uložit**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicechannel-tutorial/tutorial_general_400.png)

8. Na **ServiceChannel konfigurace** klikněte na tlačítko **konfigurace ServiceChannel** otevřete **konfigurovat přihlášení** okno. Upozorňujeme **SAML Enitity ID** z **Stručná referenční příručka** části.

9. Konfigurace jednotného přihlašování na **ServiceChannel** straně, budete muset odeslat stažené **certifikátu (Base64)** a **SAML Entity ID** k [ServiceChannel tým podpory](https://servicechannel.zendesk.com/hc/en-us). Toto se bude nastavení aby bylo možné používat jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 

### <a name="creating-a-servicechannel-test-user"></a>Vytvoření zkušebního uživatele ServiceChannel

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele automaticky se vytvoří v aplikaci. Pro úplné uživatelské zřizování, obraťte se na [ServiceChannel tým podpory](https://servicechannel.zendesk.com/hc/en-us)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k ServiceChannel Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon ServiceChannel, proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ServiceChannel**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_app01.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici ServiceChannel na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci ServiceChannel.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_203.png