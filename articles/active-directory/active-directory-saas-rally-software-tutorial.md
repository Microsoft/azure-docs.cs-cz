---
title: 'Kurz: Azure Active Directory integrace s technologie Rally softwarem | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a technologie Rally softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: f257ebe03129501034cca3b3e54e8fc796c357fc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352867"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Kurz: Azure Active Directory integrace s technologie Rally softwaru

V tomto kurzu zjistěte, jak integrovat technologie Rally softwaru s Azure Active Directory (Azure AD).

Integrace technologie Rally softwaru s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k technologie Rally softwaru.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k technologie Rally softwaru (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s technologie Rally softwaru, potřebujete následující položky:

- Předplatné služby Azure AD
- Technologie Rally softwaru jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání technologie Rally softwaru z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-rally-software-from-the-gallery"></a>Přidání technologie Rally softwaru z Galerie
Při konfiguraci integrace technologie Rally softwaru do služby Azure AD, musíte přidat technologie Rally Software z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat technologie Rally softwaru z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **technologie Rally softwaru**, vyberte **technologie Rally softwaru** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![V seznamu výsledků technologie Rally softwaru](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí technologie Rally Software založený na testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v technologie Rally softwaru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v technologie Rally softwaru je nutné stanovit.

V softwaru technologie Rally přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s technologie Rally softwaru, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele technologie Rally softwaru](#create-a-rally-software-test-user)**  – Pokud chcete mít protějšek Britta Simon v technologie Rally Software, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci technologie Rally softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s technologie Rally softwarem, proveďte následující kroky:**

1. Na portálu Azure na **technologie Rally softwaru** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

3. Na **technologie Rally softwaru domény a adresy URL** část, proveďte následující kroky:

    ![Technologie Rally softwaru adresy URL jeden přihlašování informace o doméně a](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.rally.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory technologie Rally klientský Software](https://help.rallydev.com/) k získání těchto hodnot. 
 


4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-rally-software-tutorial/tutorial_general_400.png)

6. Na **technologie Rally softwarové konfigurace** klikněte na tlačítko **konfigurace technologie Rally softwaru** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresy URL a SAML Entity ID** z **Stručná referenční příručka části.**

    ![Technologie Rally konfigurace softwaru](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_configure.png) 

7. Přihlaste se k vaší **technologie Rally softwaru** klienta.

8. Na panelu nástrojů v horní části klikněte na tlačítko **instalace**a potom vyberte **předplatné**.
   
    ![Předplatné](./media/active-directory-saas-rally-software-tutorial/ic769531.png "předplatného")

9. Klikněte **akce** tlačítko. Vyberte **upravit předplatné** v pravé horní části panelu nástrojů.

10. Na **předplatné** dialogové okno stránky, proveďte následující kroky a pak klikněte na **uložit a zavřít**:
   
    ![Ověřování](./media/active-directory-saas-rally-software-tutorial/ic769542.png "ověřování")
   
    a. Vyberte **technologie Rally nebo jednotného přihlašování k ověřování** z rozevíracího seznamu ověřování.

    b. V **adresa URL poskytovatele Identity** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure. 

    c. V **jednotného přihlašování k odhlášení** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-rally-software-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-rally-software-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-rally-software-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-rally-software-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-rally-software-test-user"></a>Vytvoření zkušebního uživatele technologie Rally softwaru

Azure AD uživatelé moci přihlásit musí být zřízená do technologie Rally softwaru aplikace pomocí jejich názvy uživatele Azure Active Directory.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se ke klientovi technologie Rally softwaru.

2. Přejděte na **instalace \> uživatelé**a potom klikněte na **+ přidat nový**.
   
    ![Uživatelé](./media/active-directory-saas-rally-software-tutorial/ic781039.png "uživatelů")

3. Zadejte název do textového pole nového uživatele a pak klikněte na tlačítko **přidat s podrobnostmi**.

4. V **vytvořit uživatele** část, proveďte následující kroky:
   
    ![Vytvoření uživatele](./media/active-directory-saas-rally-software-tutorial/ic781040.png "vytvoření uživatele")

    a. V **uživatelské jméno** textovému poli, zadejte jméno uživatele jako **Brittsimon**.
   
    b. V **e-mailovou adresu** textovému poli, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

    c. V **křestní jméno** textové pole, zadejte jméno uživatele jako **Britta**.

    d. V **příjmení** text zadejte příjmení uživatele jako **Simon**.

    e. Klikněte na tlačítko **uložit a zavřít**.

   >[!NOTE]
   >Další nástroje pro tvorbu technologie Rally softwaru uživatelského účtu nebo rozhraní API poskytovaných technologie Rally softwaru můžete použít ke zřízení uživatelských účtů Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu technologie Rally softwaru.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon technologie Rally softwaru, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **technologie Rally softwaru**.

    ![V seznamu aplikací na odkaz technologie Rally softwaru](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici technologie Rally softwaru na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci technologie Rally softwaru.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_203.png

