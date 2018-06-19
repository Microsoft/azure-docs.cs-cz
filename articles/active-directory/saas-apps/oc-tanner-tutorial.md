---
title: 'Kurz: Azure Active Directory integrace s O.C. Nováková - AppreciateHub | Microsoft Docs'
description: Naučte se konfigurovat jednotné přihlašování mezi Azure Active Directory a O.C. Nováková - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: d2ef323ca2fd3b863ba03a109bf10a43810cf9e8
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901767"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Kurz: Azure Active Directory integrace s O.C. Nováková - AppreciateHub

V tomto kurzu zjistíte, jak integrovat O.C. Nováková - AppreciateHub s Azure Active Directory (Azure AD).

Integrace O.C. Nováková - AppreciateHub s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k O.C. Nováková - AppreciateHub
- Můžete povolit uživatelům, aby automaticky získat přihlášení k O.C. Nováková - AppreciateHub (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s O.C. Nováková - AppreciateHub, potřebujete následující položky:

- Předplatné služby Azure AD
- O.C. Nováková - AppreciateHub jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání O.C. Nováková - AppreciateHub z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Přidání O.C. Nováková - AppreciateHub z Galerie
Konfigurace integrace O.C. Nováková - AppreciateHub do služby Azure AD, je nutné přidat O.C. Nováková - AppreciateHub z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat O.C. Nováková - AppreciateHub z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **O.C. Nováková - AppreciateHub**.

    ![Vytváření testovacího uživatele Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

5. Na panelu výsledků vyberte **O.C. Nováková - AppreciateHub**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s O.C. Nováková - AppreciateHub podle testovacího uživatele názvem "Britta Simon".

Pro jednotné přihlašování pro práci musí vědět, jaké příslušného uživatele v O.C. Azure AD Nováková - AppreciateHub je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v O.C. Nováková - AppreciateHub musí být vytvořeno.

V O.C. Nováková - AppreciateHub, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s O.C. Nováková - AppreciateHub, je potřeba provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření O.C. Nováková - AppreciateHub testovacího uživatele](#creating-a-oc-tanner---appreciatehub-test-user)**  – Pokud chcete mít protějšek Britta Simon v O.C. Nováková - AppreciateHub propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší O.C. Nováková - AppreciateHub aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s O.C. Nováková - AppreciateHub, proveďte následující kroky:**

1. Na portálu Azure na **O.C. Nováková - AppreciateHub** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

3. Na **O.C. Nováková - AppreciateHub domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL odpovědi. Obraťte se na [O.C. Nováková - tým podpory AppreciateHub](mailto:sso@octanner.com) získat tuto hodnotu.

    b. Otevřete soubor metadat pomocí následujícího odkazu: [ https://fed.appreciatehub.com/fed/sp/metadata ](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Vyhledejte **md:AssertionConsumerService** uzlu. 
   
    d. Zkopírujte hodnotu **umístění** atribut. 
   
    ![Konfigurovat nastavení aplikace][12]
   
    e. V **přihlašovací adresa URL** textovému poli, po hodnotě jste získali v předchozím kroku.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/oc-tanner-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **O.C. Nováková - AppreciateHub** straně, budete muset odeslat stažené **soubor XML s metadaty** k [O.C. Nováková - tým podpory AppreciateHub](mailto:sso@octanner.com).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/oc-tanner-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/oc-tanner-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/oc-tanner-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/oc-tanner-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Vytváření O.C. Nováková - AppreciateHub testovacího uživatele

Cílem této části je vytvoření uživatele v O.C. nazývá Britta Simon Nováková - AppreciateHub.

**Chcete-li vytvořit uživatele s názvem Britta Simon v O.C. Nováková - AppreciateHub, proveďte následující kroky:**

Požádejte vaše [O.C. Nováková - tým podpory AppreciateHub](mailto:sso@octanner.com) vytvoření uživatele, která má jako atribut nameID stejnou hodnotu jako uživatelské jméno Britta Simon ve službě Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu O.C. Nováková - AppreciateHub.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit O.C. Nováková - AppreciateHub, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **O.C. Nováková - AppreciateHub**.

    ![Konfigurovat jednotné přihlašování](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.  
Když kliknete O.C. Nováková - AppreciateHub dlaždice na přístupovém panelu jste měli získat automaticky přihlášení k vaší O.C. Nováková - AppreciateHub aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/oc-tanner-tutorial/tutorial_general_203.png

