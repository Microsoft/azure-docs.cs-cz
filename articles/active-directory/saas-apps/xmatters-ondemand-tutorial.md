---
title: 'Kurz: Integrace Azure Active Directory se službou xMatters OnDemand | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: d140cbdb0548de869c7aab6ba5da281046a215f9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434108"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Kurz: Integrace Azure Active Directory se službou xMatters OnDemand.

V tomto kurzu se dozvíte, jak integrovat xMatters OnDemand s Azure Active Directory (Azure AD).

XMatters OnDemand integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k xMatters OnDemand.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k xMatters OnDemand (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s xMatters OnDemand, potřebujete následující položky:

- S předplatným služby Azure AD
- XMatters OnDemand jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání xMatters OnDemand z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Přidání xMatters OnDemand z Galerie
Konfigurace integrace xMatters OnDemand do služby Azure AD, budete muset přidat xMatters OnDemand z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat xMatters OnDemand z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **xMatters OnDemand**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

1. Na panelu výsledků vyberte **xMatters OnDemand**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s xMatters OnDemand založená na uživateli test "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co protějšek uživatel v xMatters OnDemand je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v xMatters OnDemand musí být vytvořeno.

V xMatters OnDemand, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s xMatters OnDemand, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele OnDemand xMatters](#creating-a-xmatters-ondemand-test-user)**  – Pokud chcete mít protějšek Britta Simon v xMatters OnDemand, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich xMatters OnDemand aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s xMatters OnDemand, proveďte následující kroky:**

1. Na webu Azure Portal na **xMatters OnDemand** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

1. Na **xMatters OnDemand domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory xMatters OnDemand](https://www.xmatters.com/company/contact-us/) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a potom certifikát místně uložte soubor jako **c:\\XMatters OnDemand.cer**.

    ![Konfigurace jednotného přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > Je potřeba směrovat certifikát, který [tým podpory xMatters OnDemand](https://www.xmatters.com/company/contact-us/). Certifikát se musí být odeslán tým podpory xMatters předtím, než může dokončení konfigurace jednotného přihlašování. 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

1. Na **xMatters OnDemand konfigurace** klikněte na tlačítko **konfigurace xMatters OnDemand** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti XMatters OnDemand jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **správce**a potom klikněte na tlačítko **údaje o společnosti** na navigačním panelu na levé straně.

    ![Správce](./media/xmatters-ondemand-tutorial/IC776795.png "správce")

1. Na **konfigurace SAML** stránce, proveďte následující kroky:

    ![Konfigurace SAML](./media/xmatters-ondemand-tutorial/IC776796.png "konfigurace SAML")

    a. Vyberte **povolit SAML**.

    b. V **ID zprostředkovatele Identity** vložit do textového pole **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. V **adresy jednotného přihlašování** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. V **adresu URL jednotného odhlašování** vložit do textového pole **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    e. Na stránce s podrobnostmi společnosti, v horní části stránky, klikněte na tlačítko **uložit změny**.

    ![Podrobnosti o společnosti](./media/xmatters-ondemand-tutorial/IC776797.png "společnosti podrobnosti")

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Vytváří se testovací uživatele služby Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-xmatters-ondemand-test-user"></a>Vytvoření zkušebního uživatele OnDemand xMatters

Cílem této části je vytvořte uživatele Britta Simon v xMatters OnDemand. xMatters OnDemand podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](xmatters-ondemand-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k vaší **XMatters OnDemand** tenanta.

1.  Klikněte na tlačítko **uživatelé** kartu a pak klikněte na tlačítko **přidat uživatele**.

    ![Uživatelé](./media/xmatters-ondemand-tutorial/IC781048.png "uživatelů")

1. V **přidání uživatele** části, proveďte následující kroky:

    ![Přidání uživatele](./media/xmatters-ondemand-tutorial/IC781049.png "přidat uživatele")

    a. Vyberte **aktivní**.

    b. V **ID uživatele** , jako je textové pole, typ id uživatele uživatele Brittasimon@contoso.com.

    c. V **křestní jméno** textového pole zadejte jméno uživatele, jako je Britta.

    d. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.

    e. V **lokality** textového pole zadejte platnou lokalitou Azure platná účet AD, které chcete zřídit.

    f. Klikněte na **Uložit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k xMatters OnDemand.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit xMatters OnDemand, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **xMatters OnDemand**.

    ![Konfigurace jednotného přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na xMatters OnDemand dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší xMatters OnDemand aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](xmatters-ondemand-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/xmatters-ondemand-tutorial/tutorial_general_203.png
