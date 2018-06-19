---
title: 'Kurz: Azure Active Directory integrace s OpenAthens | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: f0dfdad16eab6dcedf05ff93130050021d065024
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901118"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Kurz: Azure Active Directory integrace s OpenAthens

V tomto kurzu zjistěte, jak integrovat OpenAthens s Azure Active Directory (Azure AD).

Integrace OpenAthens s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k OpenAthens.
- Můžete povolit uživatelům automaticky se přihlásit k OpenAthens (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s OpenAthens, potřebujete následující položky:

- Předplatné služby Azure AD
- OpenAthens jednotné přihlašování povolené předplatné

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání OpenAthens z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-openathens-from-the-gallery"></a>Přidání OpenAthens z Galerie
Při konfiguraci integrace OpenAthens do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS OpenAthens z galerie.

**Chcete-li přidat OpenAthens z Galerie**

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace, které**a pak přejděte na **všechny aplikace**.

    ![V podokně podnikových aplikací][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **OpenAthens**, vyberte **OpenAthens** z panelu výsledky a potom vyberte **přidat** tlačítko.

    ![OpenAthens v seznamu výsledků](./media/openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s OpenAthens podle testovacího uživatele s názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v OpenAthens je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení vztah mezi uživatele Azure AD a související uživatelské v OpenAthens.

V OpenAthens, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s OpenAthens, je třeba dokončit následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on), aby uživatelé používali tuto funkci.
2. [Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user), Azure AD jednotné přihlašování s Britta Simon otestovat.
3. [Vytvořit testovací uživatele s OpenAthens](#create-a-openathens-test-user), tak, aby měl protějšek Britta Simon v OpenAthens propojeném s Azure AD reprezentace uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user), aby Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#test-single-sign-on), chcete-li ověřit, zda funguje konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci OpenAthens.

**Ke konfiguraci Azure AD jednotné přihlašování s OpenAthens**

1. Na portálu Azure na **OpenAthens** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Pro povolení jednotného přihlašování, v **jednotného přihlašování** dialogové okno, vyberte **na základě SAML přihlašování** jako **režimu**.
 
    ![Jediné přihlášení dialogové okno](./media/openathens-tutorial/tutorial_openathens_samlbase.png)

3. V **OpenAthens domény a adresy URL** části, zadejte hodnotu `https://login.openathens.net/saml/2/metadata-sp` v **identifikátor** textové pole.

    ![OpenAthens domény a adresy URL jednotné přihlašování informace](./media/openathens-tutorial/tutorial_openathens_url.png)

4. V **SAML podpisový certifikát** vyberte **soubor XML s metadaty**a potom uložte soubor metadat ve vašem počítači.

    ![Certifikát pro podpis AMSL stáhnout odkaz](./media/openathens-tutorial/tutorial_openathens_certificate.png) 

5. Vyberte tlačítko **Uložit**.

    ![Jednotné přihlašování tlačítko Uložit](./media/openathens-tutorial/tutorial_general_400.png)

6. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti OpenAthens jako správce.

7. Vyberte **připojení** ze seznamu v části **správy** kartě. 

    ![Konfigurovat jednotné přihlašování](./media/openathens-tutorial/tutorial_openathens_application1.png)

8. Vyberte **SAML 1.1 nebo 2.0**a pak vyberte **konfigurace** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
9. Chcete-li přidat konfiguraci, vyberte **Procházet** tlačítko odeslat metadata soubor .xml, který jste si stáhli z portálu Azure a pak vyberte **přidat**.

    ![Konfigurovat jednotné přihlašování](./media/openathens-tutorial/tutorial_openathens_application3.png)

10. Proveďte následující kroky v části **podrobnosti** kartě.

    ![Konfigurovat jednotné přihlašování](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. V **mapování názvu zobrazení**, vyberte **použít atribut**.

    b. V **zobrazovaný název atributu** textové pole, zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. V **jedinečná uživatelská mapování**, vyberte **použít atribut**.

    d. V **atribut jedinečný uživatele** textové pole, zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. V **stav**, zaškrtněte všechny tři políčka.

    f. V **místní účty**, vyberte **automaticky**.

    g. Vyberte **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco k nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a přístup k vložený dokumentace prostřednictvím **konfigurace** v dolní části. Další informace o funkci embedded dokumentaci najdete v tématu [Azure AD vložených dokumentaci](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem "Britta Simon."

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD**

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](./media/openathens-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom vyberte **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/openathens-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/openathens-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/openathens-tutorial/create_aaduser_04.png)

    a. V **název** textového pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textového pole, zadejte e-mailovou adresu pro Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** textové pole.

    d. Vyberte **Vytvořit**.
  
### <a name="create-an-openathens-test-user"></a>Vytvořit uživatele s OpenAthens testu

OpenAthens podporuje zřizování za běhu, a uživatelé se vytvoří automaticky po úspěšném ověření. Nemusíte provádět veškeré akce v této části.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k OpenAthens Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Britta Simon přiřadit OpenAthens**

1. Na portálu Azure, otevřete zobrazit aplikace, přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které**a potom vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V **aplikace** seznamu, vyberte **OpenAthens**.

    ![V seznamu aplikací na OpenAthens odkaz](./media/openathens-tutorial/tutorial_openathens_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** podokně.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** seznamu, vyberte **Britta Simon**.

6. Vyberte **vyberte** v tlačítko **uživatelů a skupin** seznamu.

7. Vyberte **přiřadit** v tlačítko **přidat přiřazení** podokně.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete **OpenAthens** dlaždici na přístupovém panelu můžete by měl být automaticky přihlášeni do vaší aplikace OpenAthens.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* Seznam kurzů k integraci aplikací SaaS v Azure Active Directory, naleznete v části [kurzy integraci aplikací SaaS pro použití s Azure AD](tutorial-list.md).
* Další informace o přístup k aplikaci a jednotné přihlašování s Azure Active Directory najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

<!--Image references-->

[1]: ./media/openathens-tutorial/tutorial_general_01.png
[2]: ./media/openathens-tutorial/tutorial_general_02.png
[3]: ./media/openathens-tutorial/tutorial_general_03.png
[4]: ./media/openathens-tutorial/tutorial_general_04.png

[100]: ./media/openathens-tutorial/tutorial_general_100.png

[200]: ./media/openathens-tutorial/tutorial_general_200.png
[201]: ./media/openathens-tutorial/tutorial_general_201.png
[202]: ./media/openathens-tutorial/tutorial_general_202.png
[203]: ./media/openathens-tutorial/tutorial_general_203.png

