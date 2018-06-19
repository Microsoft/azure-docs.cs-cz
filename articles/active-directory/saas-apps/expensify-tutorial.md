---
title: 'Kurz: Azure Active Directory integrace s Expensify | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/2/2017
ms.author: jeedes
ms.openlocfilehash: 56093b43bd3b3825f43e4c2310399889cea4fef1
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903161"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Kurz: Azure Active Directory integrace s Expensify

V tomto kurzu zjistěte, jak integrovat Expensify s Azure Active Directory (Azure AD).

Integrace Expensify s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Expensify.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Expensify (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Expensify, potřebujete následující položky:

- Předplatné služby Azure AD
- Expensify jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Expensify z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-expensify-from-the-gallery"></a>Přidání Expensify z Galerie
Při konfiguraci integrace Expensify do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Expensify z galerie.

**Pokud chcete přidat Expensify z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Expensify**, vyberte **Expensify** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Expensify v seznamu výsledků](./media/expensify-tutorial/tutorial_expensify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Expensify podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Expensify je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Expensify musí navázat.

V Expensify, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Expensify, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Expensify](#create-an-expensify-test-user)**  – Pokud chcete mít protějšek Britta Simon v Expensify propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Expensify.

**Ke konfiguraci Azure AD jednotné přihlašování s Expensify, proveďte následující kroky:**

1. Na portálu Azure na **Expensify** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/expensify-tutorial/tutorial_expensify_samlbase.png)

3. Na **Expensify domény a adresy URL** část, proveďte následující kroky:

    ![Expensify domény a adresy URL jeden přihlašování informace](./media/expensify-tutorial/tutorial_expensify_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL jako: `https://www.expensify.com/authentication/saml/login`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.<companyname>.expensify.com`

    > [!NOTE] 
    > Nahraďte `<companyname>` části identifikátoru adresy URL v doméně vaší společnosti. Podívejte se příklad `https://contoso.expensify.com` výše. Obraťte se na [tým podpory Expensify klienta](mailto:help@expensify.com) získat tuto hodnotu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/expensify-tutorial/tutorial_expensify_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/expensify-tutorial/tutorial_general_400.png)

6. Pokud chcete povolit jednotné přihlašování v Expensify, musíte nejprve povolit **domény řízení** v aplikaci. Ovládací prvek domény můžete povolit v aplikaci pomocí kroků uvedených v tomto [zde](http://help.expensify.com/domain-control). Pro další podporu, pracovat s [tým podpory Expensify klienta](mailto:help@expensify.com). Až budete mít povoleno řízení domény, postupujte takto:
   
    ![Konfigurovat jednotné přihlašování](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Přihlásit se k vaší aplikaci Expensify.
    
    b. Na panelu nástrojů v horní části klikněte na tlačítko **správce**.
    
    c. V levém panelu klikněte na **domény**.
    
    d. Klikněte na název ověřené domény.
    
    e. V levém panelu klikněte na **SAML**a potom vyberte **povoleno**.
    
    f. Z Azure AD v poznámkovém bloku otevřete stažené federačních metadat, kopírovat obsah a vložte ji do **metadat zprostředkovatelů Identity** textové pole.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/expensify-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/expensify-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/expensify-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/expensify-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-expensify-test-user"></a>Vytvořit uživatele s Expensify testu

V této části vytvoříte volal Britta Simon v Expensify uživatele. Práce s [tým podpory Expensify klienta](mailto:help@expensify.com) přidat uživatele do Expensify platformy.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Expensify.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Expensify, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Expensify**.

    ![V seznamu aplikací na Expensify odkaz](./media/expensify-tutorial/tutorial_expensify_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Expensify na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Expensify.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/expensify-tutorial/tutorial_general_01.png
[2]: ./media/expensify-tutorial/tutorial_general_02.png
[3]: ./media/expensify-tutorial/tutorial_general_03.png
[4]: ./media/expensify-tutorial/tutorial_general_04.png

[100]: ./media/expensify-tutorial/tutorial_general_100.png

[200]: ./media/expensify-tutorial/tutorial_general_200.png
[201]: ./media/expensify-tutorial/tutorial_general_201.png
[202]: ./media/expensify-tutorial/tutorial_general_202.png
[203]: ./media/expensify-tutorial/tutorial_general_203.png

