---
title: 'Kurz: Integrace Azure Active Directory s kanálu ServiceChannel | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a kanálu ServiceChannel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.openlocfilehash: 4fb0d0d91dbdbb437bedd0ea58d95e08694dcaab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154249"
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Kurz: Integrace Azure Active Directory s kanálu ServiceChannel

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) kanálu ServiceChannel.

Integrace kanálu ServiceChannel s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke kanálu ServiceChannel
- Můžete povolit uživatelům, aby automaticky získat přihlášení k kanálu ServiceChannel (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na portálu pro správu Azure

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s kanálu ServiceChannel, potřebujete následující položky:

- Předplatné Azure AD
- Kanálu ServiceChannel jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání kanálu ServiceChannel z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-servicechannel-from-the-gallery"></a>Přidání kanálu ServiceChannel z Galerie
Konfigurace integrace kanálu ServiceChannel do služby Azure AD, budete muset přidat kanálu ServiceChannel z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat kanálu ServiceChannel z galerie, postupujte následovně:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **kanálu ServiceChannel**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/servicechannel-tutorial/tutorial-servicechannel_000.png)

1. Na panelu výsledků vyberte **kanálu ServiceChannel**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí podle testovacího uživatele nazývá "Britta Simon" kanálu ServiceChannel.

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v kanálu ServiceChannel je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatel v kanálu ServiceChannel.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v kanálu ServiceChannel.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s kanálu ServiceChannel, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele kanálu ServiceChannel](#creating-a-servicechannel-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure Management portal a konfigurace jednotného přihlašování v aplikaci kanálu ServiceChannel.

**Ke konfiguraci Azure AD jednotné přihlašování s kanálu ServiceChannel, proveďte následující kroky:**

1. V portálu pro správu Azure na **kanálu ServiceChannel** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** chcete povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/servicechannel-tutorial/tutorial-servicechannel_01.png)

1. Na **kanálu ServiceChannel domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. V **identifikátor** textového pole zadejte hodnotu jako: `http://adfs.<domain>.com/adfs/service/trust`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE] 
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Tady doporučujeme používat jedinečnou hodnotu řetězce v identifikátoru. Kontakt [tým podpory kanálu ServiceChannel](https://servicechannel.zendesk.com/hc/en-us) k získání těchto hodnot.

1. Vaše aplikace kanálu ServiceChannel očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto. **NameIdentifier (identifikátor uživatele)** je povinné jenom deklarace identity a výchozí hodnota je **user.userprincipalname** ale kanálu ServiceChannel očekává, že to být namapována na žádnou **user.mail**. Pokud máte v úmyslu povolit zřizování uživatelů JIT, by měl jak je znázorněno níže přidejte následující deklarace identity. **Role** deklarace identity musí být namapovaný na **user.assignedroles** obsahující roli uživatele.  

    Můžete použít Průvodce kanálu ServiceChannel [tady](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example) další pravidla deklarací identity.
    
    ![Konfigurace jednotného přihlašování](./media/servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Zobrazit [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md) na další informace o konfiguraci **Role** ve službě Azure AD.

1. V **atributy uživatele** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** a nastavte atributy.

    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | Role| user.assignedroles |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Konfigurace jednotného přihlašování](./media/servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **Ok**
    
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/servicechannel-tutorial/tutorial-servicechannel_05.png) 

1. Klikněte na **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/servicechannel-tutorial/tutorial_general_400.png)

1. Na **konfigurace kanálu ServiceChannel** klikněte na tlačítko **konfigurace kanálu ServiceChannel** otevřete **nakonfigurovat přihlašování** okna. Mějte prosím na paměti **SAML Enitity ID** z **Stručná referenční příručka** oddílu.

1. Ke konfiguraci jednotného přihlašování na **kanálu ServiceChannel** straně, je nutné odeslat na stažený **certifikát (Base64)** a **SAML Entity ID** k [kanálu ServiceChannel tým podpory](https://servicechannel.zendesk.com/hc/en-us). To bude nastavené abyste měli správně nastavené na obou stranách připojení SAML SSO.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure Management portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **Azure Management portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/servicechannel-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/servicechannel-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/servicechannel-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/servicechannel-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 

### <a name="creating-a-servicechannel-test-user"></a>Vytvoření kanálu ServiceChannel testovacího uživatele

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele budou vytvořeny v aplikaci automaticky. Úplné uživatelské zřizování, obraťte se prosím na [kanálu ServiceChannel tým podpory](https://servicechannel.zendesk.com/hc/en-us)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do kanálu ServiceChannel.

![Přiřadit uživatele][200] 

**Přiřadit kanálu ServiceChannel Britta Simon, proveďte následující kroky:**

1. Na portálu Azure Management portal, otevřete zobrazení aplikací a přejděte do zobrazení adresáře přejít na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **kanálu ServiceChannel**.

    ![Konfigurace jednotného přihlašování](./media/servicechannel-tutorial/tutorial-servicechannel_app01.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici kanálu ServiceChannel na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci kanálu ServiceChannel.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/servicechannel-tutorial/tutorial_general_203.png
