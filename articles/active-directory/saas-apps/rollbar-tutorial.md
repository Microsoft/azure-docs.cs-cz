---
title: 'Kurz: Integrace Azure Active Directory s oblouk | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a oblouk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: d92be4d72936664f3839d7798e13e66d11b61b8e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155625"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Kurz: Integrace Azure Active Directory s oblouk

V tomto kurzu se dozvíte, jak integrovat oblouk s Azure Active Directory (Azure AD).

Integrace oblouk s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k oblouk.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k oblouk (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s oblouk, potřebujete následující položky:

- Předplatné Azure AD
- Oblouk jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání oblouk z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-rollbar-from-the-gallery"></a>Přidání oblouk z Galerie
Konfigurace integrace oblouk do služby Azure AD, budete muset přidat oblouk na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat oblouk z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **oblouk**vyberte **oblouk** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![V seznamu výsledků oblouk](./media/rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí oblouk na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v oblouk je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v oblouk musí být navázáno.

V oblouk, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s oblouk, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele oblouk](#create-a-rollbar-test-user)**  – Pokud chcete mít protějšek Britta Simon oblouk, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci oblouk.

**Ke konfiguraci Azure AD jednotné přihlašování s oblouk, proveďte následující kroky:**

1. Na webu Azure Portal na **oblouk** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/rollbar-tutorial/tutorial_rollbar_samlbase.png)

1. Na **oblouk domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Oblouk domény a adresy URL jednotného přihlašování – informace](./media/rollbar-tutorial/tutorial_rollbar_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://saml.rollbar.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://rollbar.com/<accountname>/saml/sso/azure/`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Oblouk domény a adresy URL jednotného přihlašování – informace](./media/rollbar-tutorial/tutorial_rollbar_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory oblouk klienta](mailto:support@rollbar.com) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/rollbar-tutorial/tutorial_rollbar_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/rollbar-tutorial/tutorial_general_400.png)
    
1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti oblouk jako správce.

1. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení název účtu**.
    
    ![Konfigurace](./media/rollbar-tutorial/general.png)

1. Klikněte na tlačítko **zprostředkovatele Identity** v části zabezpečení.

    ![Konfigurace](./media/rollbar-tutorial/configure1.png)

1. V **zprostředkovatele Identity SAML** části, proveďte následující kroky:
    
    ![Konfigurace](./media/rollbar-tutorial/configure2.png)

    a. Vyberte **AZURE** z **zprostředkovatele Identity SAML** rozevíracího seznamu.

    b. V poznámkovém bloku otevřete soubor metadat, zkopírujte obsah ho do schránky a vložte ho do **metadat SAML** textového pole.

    c. Klikněte na **Uložit**.

1. Po kliknutí na toto tlačítko, na obrazovce bude takto:
    
    ![Konfigurace](./media/rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > Aby bylo možné dokončit následující krok, sami nejprve musíte přidat jako uživatel na oblouk aplikaci v Azure.
    a. Pokud chcete, aby všichni uživatelé k ověřování prostřednictvím Azure a pak klikněte na **přihlásit pomocí vašeho zprostředkovatele identity** opakované ověření přes Azure.  

    b.  Jakmile se vrátíte na obrazovku, vyberte **vyžadují přihlášení pomocí zprostředkovatele Identity SAML** zaškrtávací políčko.

    b. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/rollbar-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/rollbar-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/rollbar-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/rollbar-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-rollbar-test-user"></a>Vytvoření zkušebního uživatele oblouk

Umožňuje uživatelům Azure AD k přihlášení na oblouk musí být poskytnuty do oblouk. V případě oblouk zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Připojte se k webu společnosti oblouk jako správce.

1. Klikněte na **nastavení profilu** v pravém horním rohu a pak klikněte na tlačítko **nastavení název účtu**.

    ![Uživatel](./media/rollbar-tutorial/general.png)

1. Klikněte na tlačítko **uživatelé**.
    
    ![Přidat zaměstnance](./media/rollbar-tutorial/user1.png)

1. Klikněte na tlačítko **Přizvěte členy týmu**.

    ![Pozvat](./media/rollbar-tutorial/user2.png)

1. V textovém poli zadejte jméno uživatele, jako je **brittasimon@contoso.com** a kliknutím na **Add/pozvání**.

    ![Pozvat](./media/rollbar-tutorial/user3.png)

1. Uživatel dostane pozvánku a po jeho přijetí učitelského vytvořené v systému.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k oblouk.

![Přiřazení role uživatele][200] 

**Přiřadit oblouk Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **oblouk**.

    ![V seznamu aplikací na odkaz oblouk](./media/rollbar-tutorial/tutorial_rollbar_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici oblouk na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci oblouk.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rollbar-tutorial/tutorial_general_01.png
[2]: ./media/rollbar-tutorial/tutorial_general_02.png
[3]: ./media/rollbar-tutorial/tutorial_general_03.png
[4]: ./media/rollbar-tutorial/tutorial_general_04.png

[100]: ./media/rollbar-tutorial/tutorial_general_100.png

[200]: ./media/rollbar-tutorial/tutorial_general_200.png
[201]: ./media/rollbar-tutorial/tutorial_general_201.png
[202]: ./media/rollbar-tutorial/tutorial_general_202.png
[203]: ./media/rollbar-tutorial/tutorial_general_203.png

