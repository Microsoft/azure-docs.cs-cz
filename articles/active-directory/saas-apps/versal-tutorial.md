---
title: 'Kurz: Azure Active Directory integrace s Versal odkazů | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Versal odkazů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeedes
ms.openlocfilehash: 0f8621ec55ee58e818a60dc74ea5c4f111909b8a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210271"
---
# <a name="tutorial-azure-active-directory-integration-with-versal"></a>Kurz: Azure Active Directory integrace s Versal odkazů

V tomto kurzu zjistěte, jak integrovat Versal odkazů v Azure Active Directory (Azure AD).

Integrace Versal odkazů s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Versal odkazů.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Versal odkazů (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Versal odkazů, potřebujete následující položky:

- Předplatné služby Azure AD
- Verzál jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Versal odkazů z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-versal-from-the-gallery"></a>Přidání Versal odkazů z Galerie
Pokud chcete nakonfigurovat integraci Versal odkazů do služby Azure AD, potřebujete přidat Versal odkazů z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Versal odkazů z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Versal odkazů**, vyberte **Versal odkazů** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Versal odkazů v seznamu výsledků](./media/versal-tutorial/tutorial_versal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Versal odkazů na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Versal odkazů je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v Versal odkazů.

V Versal odkazů, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Versal odkazů, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření verzál zkušebního uživatele](#create-a-versal-test-user)**  – Pokud chcete mít protějšek Britta Simon v Versal odkazů, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci verzál.

**Ke konfiguraci Azure AD jednotné přihlašování s Versal odkazů, proveďte následující kroky:**

1. Na portálu Azure na **Versal odkazů** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/versal-tutorial/tutorial_versal_samlbase.png)

3. Na **Versal odkazů domény a adresy URL** část, proveďte následující kroky:

    ![Verzál domény a adresy URL jednotné přihlašování informace](./media/versal-tutorial/tutorial_versal_url.png)

    a. V **identifikátor** textovému poli, zadejte hodnotu: `VERSAL`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE] 
    > Hodnota adresy URL a odpovědi není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL odpovědi. Obraťte se na [tým podpory verzál](https://support.versal.com/hc/) získat tuto hodnotu.
    
4. Vaše aplikace očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tento. Výchozí hodnota **uživatelský identifikátor** je **user.userprincipalname** ale **Versal odkazů** očekává to nejde mapovat pomocí e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu, nebo použijte hodnotu odpovídajícího atributu na základě konfigurace vaší organizace.
    
    ![Rozevírací nabídce identifikátor uživatele](./media/versal-tutorial/tutorial_versal_attribute.png)

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/versal-tutorial/tutorial_versal_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/versal-tutorial/tutorial_general_400.png)
    
7. Konfigurace jednotného přihlašování na **Versal odkazů** straně, budete muset odeslat stažené **soubor XML s metadaty** a **SAML podpisový certifikát** k [tým verzál podpory ](https://support.versal.com/hc/). Nakonfiguruje organizaci verzál tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/versal-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/versal-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/versal-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/versal-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-versal-test-user"></a>Vytvoření verzál zkušebního uživatele

V této části vytvoříte uživatele volal Britta Simon v Versal odkazů. Postupujte podle [vytváření SAML testovací uživatel](https://support.versal.com/hc/en-us/articles/115011672887-Creating-a-SAML-test-user) podpora průvodce pro vytvoření uživatele Britta Simon v rámci vaší organizace. Uživatelé musí být vytvořen a za Versal odkazů aktivuje před použitím jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Versal odkazů.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Versal odkazů, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Versal odkazů**.

    ![Odkaz Versal odkazů v seznamu aplikací](./media/versal-tutorial/tutorial_versal_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí verzál kurzu vložených v rámci vašeho webu.
Najdete v tématu [vložení organizační kurzy](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses) **SAML Single Sign-On** podpora příručka pokyny o tom, jak se podpora pro Azure AD pro vložení verzál během jednotného přihlašování. 

Musíte vytvořit kurzu, sdílet s vaší organizace a publikovat účely otestování kurzu vložení. Najdete v tématu [vytváření kurzu](https://support.versal.com/hc/en-us/articles/203722528-Create-a-course), [publikování kurzu](https://support.versal.com/hc/en-us/articles/203753398-Publishing-a-course), a [správu kurzu a Student](https://support.versal.com/hc/en-us/articles/206029467-Course-and-learner-management) Další informace.  
                     

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/versal-tutorial/tutorial_general_01.png
[2]: ./media/versal-tutorial/tutorial_general_02.png
[3]: ./media/versal-tutorial/tutorial_general_03.png
[4]: ./media/versal-tutorial/tutorial_general_04.png

[100]: ./media/versal-tutorial/tutorial_general_100.png

[200]: ./media/versal-tutorial/tutorial_general_200.png
[201]: ./media/versal-tutorial/tutorial_general_201.png
[202]: ./media/versal-tutorial/tutorial_general_202.png
[203]: ./media/versal-tutorial/tutorial_general_203.png

