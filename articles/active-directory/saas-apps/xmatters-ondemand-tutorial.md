---
title: 'Kurz: Azure Active Directory integrace s xMatters OnDemand | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 7d68864a6712823ca1d4a55691b54e8fbb9c968f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903212"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Kurz: Azure Active Directory integrace s xMatters OnDemand

V tomto kurzu zjistěte, jak integrovat xMatters OnDemand s Azure Active Directory (Azure AD).

Integrace xMatters OnDemand s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k xMatters OnDemand
- Můžete povolit uživatelům, aby automaticky získat přihlášení k xMatters OnDemand (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s xMatters OnDemand, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné povolené xMatters OnDemand jednotné přihlašování

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání xMatters OnDemand z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Přidání xMatters OnDemand z Galerie
Při konfiguraci integrace xMatters OnDemand do služby Azure AD potřebujete přidat xMatters OnDemand z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat xMatters OnDemand z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **xMatters OnDemand**.

    ![Vytváření testovacího uživatele Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

5. Na panelu výsledků vyberte **xMatters OnDemand**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování s xMatters OnDemand založené na testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, jaké příslušného uživatele v xMatters OnDemand je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v xMatters OnDemand musí být vytvořeno.

V xMatters OnDemand přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s xMatters OnDemand, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele OnDemand xMatters](#creating-a-xmatters-ondemand-test-user)**  – Pokud chcete mít protějšek Britta Simon v xMatters OnDemand propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší xMatters OnDemand aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s xMatters OnDemand, proveďte následující kroky:**

1. Na portálu Azure na **xMatters OnDemand** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Konfigurovat jednotné přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

3. Na **xMatters OnDemand domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Obraťte se na [tým podpory xMatters OnDemand](https://www.xmatters.com/company/contact-us/) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu místně jako **c:\\XMatters OnDemand.cer**.

    ![Konfigurovat jednotné přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > Je třeba předávat certifikát, který chcete [tým podpory xMatters OnDemand](https://www.xmatters.com/company/contact-us/). Certifikát musí být odeslaný tým podpory xMatters předtím, než může dokončení konfigurace přihlášení. 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

6. Na **xMatters OnDemand konfigurace** klikněte na tlačítko **konfigurace xMatters OnDemand** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se na váš web společnosti XMatters OnDemand jako správce.

8. Na panelu nástrojů v horní části klikněte na tlačítko **správce**a potom klikněte na **podrobnosti o společnosti** na navigačním panelu na levé straně.

    ![Správce](./media/xmatters-ondemand-tutorial/IC776795.png "správce")

9. Na **konfigurace SAML** proveďte následující kroky:

    ![Konfigurace SAML](./media/xmatters-ondemand-tutorial/IC776796.png "konfigurace SAML")

    a. Vyberte **povolit SAML**.

    b. V **ID zprostředkovatele Identity** textovému poli, vložte **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure.

    c. V **jeden přihlašovací adresa URL** textovému poli, vložte **SAML jeden přihlašování adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure.

    d. V **jednu adresu URL odhlašovací** textovému poli, vložte **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    e. Na stránce Podrobnosti o společnosti v horní části, klikněte na **uložit změny**.

    ![Podrobnosti o společnosti](./media/xmatters-ondemand-tutorial/IC776797.png "společnosti podrobnosti")

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    ![Vytváření testovacího uživatele Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.

    ![Vytváření testovacího uživatele Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:

    ![Vytváření testovacího uživatele Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-xmatters-ondemand-test-user"></a>Vytvoření zkušebního uživatele OnDemand xMatters

Cílem této části je vytvoření uživatele volal Britta Simon v xMatters OnDemand. xMatters OnDemand podporuje automatické uživatele zřizování, který je ve výchozím nastavení povolené. Můžete najít další podrobnosti o [sem](xmatters-ondemand-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k vaší **XMatters OnDemand** klienta.

2.  Klikněte na tlačítko **uživatelé** kartu a pak klikněte na tlačítko **přidat uživatele**.

    ![Uživatelé](./media/xmatters-ondemand-tutorial/IC781048.png "uživatelů")

3. V **přidat uživatele** část, proveďte následující kroky:

    ![Přidat uživatele](./media/xmatters-ondemand-tutorial/IC781049.png "přidat uživatele")

    a. Vyberte **Active**.

    b. V **ID uživatele** jako typ id uživatele uživatele k textovému poli, Brittasimon@contoso.com.

    c. V **křestní jméno** textovému poli, typ křestní jméno uživatele, jako je Britta.

    d. V **příjmení** textovému poli, zadejte příjmení uživatele jako Simon.

    e. V **lokality** textovému poli, zadejte platnou lokalitou platný Azure AD účet chcete zřídit.

    f. Klikněte na **Uložit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k xMatters OnDemand.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon xMatters OnDemand, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **xMatters OnDemand**.

    ![Konfigurovat jednotné přihlašování](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko xMatters OnDemand dlaždice na přístupovém panelu, jste měli získat automaticky přihlášení k vaší xMatters OnDemand aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](xmatters-ondemand-provisioning-tutorial.md)

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
