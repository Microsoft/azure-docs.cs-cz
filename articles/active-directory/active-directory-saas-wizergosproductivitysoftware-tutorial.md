---
title: 'Kurz: Azure Active Directory integrace s Wizergos produkční Software | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi softwarem Wizergos produktivitu a Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: jeedes
ms.openlocfilehash: 1b78b0f2df6211626fd9b4d0cecf9f67d7f47d87
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354482"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Kurz: Azure Active Directory integrace s Wizergos produktivitu softwaru

V tomto kurzu zjistěte, jak integrovat Wizergos produkční Software s Azure Active Directory (Azure AD).

Integrace Wizergos produkční Software s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k softwaru Wizergos produktivitu.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k softwaru produktivitu Wizergos (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Wizergos produkční Software, potřebujete následující položky:

- Předplatné služby Azure AD
- Software pro zvýšení produktivity Wizergos jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání softwaru produktivitu Wizergos z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Přidání softwaru produktivitu Wizergos z Galerie
Při konfiguraci integrace Wizergos produktivitu softwaru do služby Azure AD, potřebujete přidat Wizergos produktivitu softwaru z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat Wizergos produktivitu softwaru z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Wizergos produktivitu softwaru**, vyberte **Wizergos produktivitu softwaru** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Software pro zvýšení produktivity Wizergos v seznamu výsledků](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Wizergos produktivitu Software založený na testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v Wizergos produktivitu softwaru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Wizergos produktivitu softwaru je nutné stanovit.

V softwaru Wizergos produktivitu, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Wizergos produktivitu softwaru, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele softwaru produktivitu Wizergos](#create-a-wizergos-productivity-software-test-user)**  – Pokud chcete mít protějšek Britta Simon v Wizergos produktivitu Software, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Wizergos produktivitu softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s Wizergos produkční Software, proveďte následující kroky:**

1. Na portálu Azure na **Wizergos produktivitu softwaru** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_samlbase.png)

3. Na **Wizergos produktivitu softwaru domény a adresy URL** část, proveďte následující kroky:

    ![Wizergos produktivitu softwaru domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_url.png)

    V **identifikátor** textovému poli, zadejte adresu URL: `http://www.wizergos.net`

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikát** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_400.png)

6. Na **Wizergos produktivitu softwarové konfigurace** klikněte na tlačítko **konfigurace softwaru produktivitu Wizergos** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Wizergos produktivitu softwarové konfigurace](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_configure.png) 

7. V okně prohlížeče jiných webových přihlašování ke klientovi Wizergos produktivitu softwaru jako správce.

8. Vyberte z nabídky hamburger **správce**.

    ![Konfigurace straně jediné přihlášení na aplikaci](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

9. Správce stránce v nabídce vlevo vyberte **ověřování** a klikněte na **Azure AD**.

    ![Konfigurace straně jediné přihlášení na aplikaci](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

10. Proveďte následující kroky na **ověřování** části.

    ![Konfigurace straně jediné přihlášení na aplikaci](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Klikněte na tlačítko **NAHRÁT** tlačítko, na kterou odešlete certifikát stažený z Azure AD.
    
    b. V **URL vystavitele** textovému poli, Vložit **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure.
    
    c. V **adresy jednotného přihlašování** textovému poli, Vložit **SAML jeden přihlašování adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure.
    
    d. V **jednu adresu URL Sign-Out** textovému poli, Vložit **Sign-Out URL** hodnotu, která jste zkopírovali z portálu Azure.
    
    e. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-wizergos-productivity-software-test-user"></a>Vytvoření zkušebního uživatele Wizergos produktivitu softwaru

V této části vytvoříte volal Britta Simon v softwaru Wizergos produktivitu uživatele. Spojte se s [tým podpory Wizergos produktivitu softwaru](mailTo:support@wizergos.com) přidat uživatele do platformy Wizergos produktivitu softwaru.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k softwaru Wizergos produktivitu.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Wizergos produkční Software, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Wizergos produktivitu softwaru**.

    ![Odkaz Wizergos produktivitu softwaru v seznamu aplikací](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Wizergos produktivitu softwaru na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Wizergos produktivitu softwaru.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png

