---
title: 'Kurz: Integrace Azure Active Directory s Wizergos kancelářský Software | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Wizergos kancelářský Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: jeedes
ms.openlocfilehash: c59bacb9559239c572925ee73303bbb6a0a06353
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188826"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Kurz: Integrace Azure Active Directory s Wizergos kancelářský Software

V tomto kurzu se dozvíte, jak integrovat Wizergos kancelářský Software s Azure Active Directory (Azure AD).

Integrace Wizergos kancelářský Software s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Wizergos kancelářský Software.
- Uživatele, aby automaticky získat přihlášení k Wizergos kancelářský Software (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Wizergos kancelářský Software, potřebujete následující položky:

- Předplatné Azure AD
- Kancelářský Software Wizergos jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Wizergos kancelářský Software z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Přidání Wizergos kancelářský Software z Galerie
Pokud chcete nakonfigurovat integraci Wizergos kancelářský Software do služby Azure AD, budete muset přidat Wizergos kancelářský Software na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Wizergos kancelářský Software z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Wizergos kancelářský Software**vyberte **Wizergos kancelářský Software** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Wizergos kancelářský Software v seznamu výsledků](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Wizergos kancelářský Software na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Wizergos kancelářský Software je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Wizergos kancelářský Software.

V Wizergos kancelářský Software, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Wizergos kancelářský Software, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Wizergos kancelářský Software](#create-a-wizergos-productivity-software-test-user)**  – Pokud chcete mít protějšek Britta Simon Wizergos kancelářský Software, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Wizergos kancelářský Software.

**Ke konfiguraci Azure AD jednotné přihlašování s Wizergos kancelářský Software, proveďte následující kroky:**

1. Na webu Azure Portal na **Wizergos kancelářský Software** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_samlbase.png)

1. Na **Wizergos produktivitu softwaru domény a adresy URL** části, proveďte následující kroky:

    ![Wizergos produktivitu softwaru domény a adresy URL jednotného přihlašování – informace](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_url.png)

    V **identifikátor** textového pole zadejte adresu URL: `https://www.wizergos.net`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/wizergosproductivitysoftware-tutorial/tutorial_general_400.png)

1. Na **konfigurace softwaru produktivitu Wizergos** klikněte na **konfigurace Wizergos kancelářský Software** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace softwaru Wizergos produktivitu](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_configure.png) 

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi Wizergos kancelářský Software jako správce.

1. Hamburgerová nabídka vyberte **správce**.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

1. Na stránce správce v nabídce vlevo vyberte **ověřování** a klikněte na **Azure AD**.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

1. Následující postup proveďte **ověřování** oddílu.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Klikněte na tlačítko **NAHRÁT** tlačítko Nahrát staženého certifikátu ze služby Azure AD.
    
    b. V **URL vystavitele** vložit do textového pole **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure portal.
    
    c. V **přihlašovací adresu URL jednotného** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure portal.
    
    d. V **adresu URL jednotného odhlašování** vložit do textového pole **odhlašování URL** hodnotu, která jste zkopírovali z portálu Azure portal.
    
    e. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/wizergosproductivitysoftware-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/wizergosproductivitysoftware-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/wizergosproductivitysoftware-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/wizergosproductivitysoftware-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-wizergos-productivity-software-test-user"></a>Vytvoření zkušebního uživatele Wizergos kancelářský Software

V této části vytvořte uživatele Britta Simon v Wizergos kancelářský Software. Spojte se prosím s [tým podpory Wizergos kancelářský Software](mailTo:support@wizergos.com) přidat uživatele na platformě Wizergos kancelářský Software.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Wizergos kancelářský Software.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Wizergos kancelářský Software, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Wizergos kancelářský Software**.

    ![Odkaz Wizergos kancelářský Software v seznamu aplikací](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Wizergos kancelářský Software na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Wizergos kancelářský Software.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[100]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[202]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_202.png
[203]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_203.png

