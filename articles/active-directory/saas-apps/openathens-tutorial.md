---
title: 'Kurz: Integrace Azure Active Directory se službou OpenAthens | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 269b216a94b1233c5f9f9a634fda3c05e46cac90
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435898"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Kurz: Integrace Azure Active Directory se službou OpenAthens

V tomto kurzu se dozvíte, jak integrovat OpenAthens s Azure Active Directory (Azure AD).

OpenAthens integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k OpenAthens.
- Můžete povolit uživatelům se automaticky se přihlásit k OpenAthens (jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s OpenAthens, potřebujete následující položky:

- S předplatným služby Azure AD
- OpenAthens jednotného přihlašování povolená předplatného

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat bezplatnou zkušební verzi měsíčního](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání OpenAthens z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-openathens-from-the-gallery"></a>Přidání OpenAthens z Galerie
Konfigurace integrace OpenAthens do služby Azure AD, budete muset přidat OpenAthens z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat OpenAthens z Galerie**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**a pak přejděte na **všechny aplikace**.

    ![V podokně podnikových aplikací][2]
    
1. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko nahoře v dialogovém okně.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **OpenAthens**vyberte **OpenAthens** z panel výsledků a pak vyberte **přidat** tlačítko.

    ![OpenAthens v seznamu výsledků](./media/openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s OpenAthens podle testovacího uživatele s názvem "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v OpenAthens je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v OpenAthens.

V OpenAthens, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s OpenAthens, které potřebujete k dokončení následujících stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on), aby uživatelé mohli tuto funkci používat.
1. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user), k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. [Vytvořit testovacího uživatele OpenAthens](#create-a-openathens-test-user), mít protějšek Britta Simon OpenAthens, který je propojený s Azure AD zastoupení uživatele.
1. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user), abyste umožnili Britta Simon používat Azure AD jednotného přihlašování.
1. [Otestovat jednotné přihlašování](#test-single-sign-on), chcete-li ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci OpenAthens.

**Ke konfiguraci Azure AD jednotné přihlašování s OpenAthens**

1. Na webu Azure Portal na **OpenAthens** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Konfigurace odkazu jednotné přihlašování][4]

1. Pro povolení jednotného přihlašování, v **jednotného přihlašování** dialogu **přihlašování na základě SAML** jako **režimu**.
 
    ![Jednotné přihlašování – dialogové okno](./media/openathens-tutorial/tutorial_openathens_samlbase.png)

1. V **OpenAthens domény a adresy URL** části, zadejte hodnotu `https://login.openathens.net/saml/2/metadata-sp` v **identifikátor** textového pole.

    ![OpenAthens domény a adresy URL jednotného přihlašování – informace](./media/openathens-tutorial/tutorial_openathens_url.png)

1. V **podpisový certifikát SAML** vyberte **soubor XML s metadaty**a poté uložte soubor metadat ve vašem počítači.

    ![Certifikát pro podpis AMSL odkaz ke stažení](./media/openathens-tutorial/tutorial_openathens_certificate.png) 

1. Vyberte tlačítko **Uložit**.

    ![Jednotné přihlašování tlačítko Uložit](./media/openathens-tutorial/tutorial_general_400.png)

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti OpenAthens jako správce.

1. Vyberte **připojení** ze seznamu **správu** kartu. 

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Vyberte **SAML 1.1 nebo 2.0**a pak vyberte **konfigurovat** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
1. Chcete-li přidat konfiguraci, vyberte **Procházet** tlačítko Nahrát soubor metada .xml, který jste si stáhli z webu Azure portal a potom vyberte **přidat**.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Proveďte následující kroky v části **podrobnosti** kartu.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. V **zobrazovaný název mapování**vyberte **použít atribut**.

    b. V **zobrazovaný název atributu** textové pole, zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. V **mapování jedinečných uživatelů**vyberte **použít atribut**.

    d. V **jedinečný uživatelský atribut** textové pole, zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. V **stav**, zaškrtněte všechna tři políčka.

    f. V **místní účty**vyberte **automaticky**.

    g. Vyberte **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a přístup k vložený dokumentace ke službě prostřednictvím **konfigurace** oblast v dolní části. Další informace o funkci vložený dokumentaci najdete v tématu [dokumentace ke službě Azure AD embedded](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal nazývá "Britta Simon."

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD**

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](./media/openathens-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/openathens-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogovém okně vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/openathens-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/openathens-tutorial/create_aaduser_04.png)

    a. V **název** textového pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, zadejte e-mailovou adresu pro Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** textového pole.

    d. Vyberte **Vytvořit**.
  
### <a name="create-an-openathens-test-user"></a>Vytvořit testovacího uživatele OpenAthens

Podporuje OpenAthens just-in-time zřizování a uživatelé jsou automaticky vytvořeny po úspěšném ověření. Není nutné provádět žádnou akci v této části.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k OpenAthens.

![Přiřazení role uživatele][200] 

**Chcete-li přiřadit Britta Simon OpenAthens**

1. Na webu Azure Portal, otevřete zobrazení aplikací, přejděte do zobrazení adresáře a přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V **aplikací** seznamu vyberte **OpenAthens**.

    ![Odkaz OpenAthens v seznamu aplikací](./media/openathens-tutorial/tutorial_openathens_app.png)  

1. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** podokně.

    ![Podokno Přidat přiřazení][203]

1. V **uživatelů a skupin** seznamu vyberte **Britta Simon**.

1. Vyberte **vyberte** tlačítko **uživatelů a skupin** seznamu.

1. Vyberte **přiřadit** tlačítko **přidat přiřazení** podokně.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete **OpenAthens** dlaždici na přístupovém panelu, můžete by měl být automaticky přihlášeni OpenAthens aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory najdete v tématu [kurzy integrace aplikací SaaS pro použití se službou Azure AD](tutorial-list.md).
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

