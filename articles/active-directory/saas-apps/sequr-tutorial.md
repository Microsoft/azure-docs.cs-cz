---
title: 'Kurz: Integrace Azure Active Directory s Sequr | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sequr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a491e2ce-b4e8-41b8-8f4a-a2e263e462c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 007989d51ad111fb6a3ef21daee6a7c484bd154d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184360"
---
# <a name="tutorial-azure-active-directory-integration-with-sequr"></a>Kurz: Integrace Azure Active Directory s Sequr

V tomto kurzu se dozvíte, jak integrovat Sequr s Azure Active Directory (Azure AD).

Sequr integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Sequr.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Sequr (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sequr, potřebujete následující položky:

- Předplatné Azure AD
- Sequr jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Sequr z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sequr-from-the-gallery"></a>Přidání Sequr z Galerie
Konfigurace integrace Sequr do služby Azure AD, budete muset přidat Sequr z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Sequr z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Sequr**vyberte **Sequr** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Sequr v seznamu výsledků](./media/sequr-tutorial/tutorial_sequr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Sequr podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Sequr je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Sequr potřeba navázat.

V Sequr, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sequr, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Sequr](#create-a-sequr-test-user)**  – Pokud chcete mít protějšek Britta Simon Sequr, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Sequr.

**Ke konfiguraci Azure AD jednotné přihlašování s Sequr, proveďte následující kroky:**

1. Na webu Azure Portal na **Sequr** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/sequr-tutorial/tutorial_sequr_samlbase.png)

1. Na **Sequr domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Sequr domény a adresy URL jednotného přihlašování – informace](./media/sequr-tutorial/tutorial_sequr_url.png)

    V **identifikátor** textového pole zadejte adresu URL: `https://login.sequr.io`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Sequr domény a adresy URL jednotného přihlašování – informace](./media/sequr-tutorial/tutorial_sequr_url1.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://login.sequr.io`

    b. V **stav přenosu** textové pole, zobrazí se tato hodnota, který je vysvětlen později v tomto kurzu.
     
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/sequr-tutorial/tutorial_sequr_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/sequr-tutorial/tutorial_general_400.png)
    
1. Na **Sequr konfigurace** klikněte na tlačítko **nakonfigurovat Sequr** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Sequr](./media/sequr-tutorial/tutorial_sequr_configure.png)

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Sequr jako správce.

1. Klikněte na **integrace** z levého navigačního panelu.

    ![Konfigurace Sequr](./media/sequr-tutorial/configure1.png)

1. Přejděte dolů k položce **Single Sign-On** části a klikněte na tlačítko **spravovat**.

    ![Konfigurace Sequr](./media/sequr-tutorial/configure2.png)

1. V **spravovat Single Sign-On** části, proveďte následující kroky:

    ![Konfigurace Sequr](./media/sequr-tutorial/configure3.png)

    a. V **jednotné přihlašování – adresa URL zprostředkovatele Identity** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    b. Přetáhnout myší **certifikát** soubor, který jste si stáhli z webu Azure portal nebo ručně zadat obsahu certifikátu.

    c. Po uložení konfigurace se vygeneruje hodnota stavu relay. Kopírování **propojení stavu** hodnotu a vložte ji **stav přenosu** textové pole z **Sequr domény a adresy URL** části webu Azure Portal.

    d. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/sequr-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/sequr-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/sequr-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/sequr-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-sequr-test-user"></a>Vytvoření zkušebního uživatele Sequr

V této části vytvoříte uživatele v Sequr jako Britta Simon. Práce s [tým podpory Sequr klienta](mailto:support@sequr.io) přidat uživatele na platformě Sequr. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Sequr použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Sequr Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Sequr**.

    ![Odkaz Sequr v seznamu aplikací](./media/sequr-tutorial/tutorial_sequr_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Sequr na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Sequr.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sequr-tutorial/tutorial_general_01.png
[2]: ./media/sequr-tutorial/tutorial_general_02.png
[3]: ./media/sequr-tutorial/tutorial_general_03.png
[4]: ./media/sequr-tutorial/tutorial_general_04.png

[100]: ./media/sequr-tutorial/tutorial_general_100.png

[200]: ./media/sequr-tutorial/tutorial_general_200.png
[201]: ./media/sequr-tutorial/tutorial_general_201.png
[202]: ./media/sequr-tutorial/tutorial_general_202.png
[203]: ./media/sequr-tutorial/tutorial_general_203.png

