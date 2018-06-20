---
title: 'Kurz: Azure Active Directory integrace s Neota logiku Studio | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Neota logiku Studio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 842605e6-a91d-42cc-a0bb-e23e67173ae2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: jeedes
ms.openlocfilehash: 5eb56460356267d3676361797ed8eeff7aa57b80
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210288"
---
# <a name="tutorial-azure-active-directory-integration-with-neota-logic-studio"></a>Kurz: Azure Active Directory integrace s Neota logiku Studio

V tomto kurzu zjistěte, jak integrovat Studio logiku Neota s Azure Active Directory (Azure AD).

Integrace s Azure AD Neota logiku Studio poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Neota logiku Studio
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Neota logiku Studio (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, přečtěte si téma. [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Neota logiku Studio, potřebujete následující položky:

- Předplatné služby Azure AD
- Neota logiku Studio jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání logiky Studio Neota z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-neota-logic-studio-from-the-gallery"></a>Přidání logiky Studio Neota z Galerie

Při konfiguraci integrace Neota logiku Studio do služby Azure AD potřebujete přidat logiku Studio Neota z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat logiku Studio Neota z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Neota logiku Studio**.

    ![Vytváření testovacího uživatele Azure AD](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_search.png)

5. Na panelu výsledků vyberte **Neota logiku Studio**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Neota Studio logiku podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v Neota logiku Studio je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Neota logiku Studio musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Neota logiku Studio.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Neota logiku Studio, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Studio logiku Neota](#creating-a-neota-logic-studio-test-user)**  – Pokud chcete mít protějšek Britta Simon v Studio Neota logiku, která je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci logiky Studio Neota.

**Ke konfiguraci Azure AD jednotné přihlašování s Studio Neota logiku, proveďte následující kroky:**

1. Na portálu Azure na **Neota logiku Studio** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_samlbase.png)

3. Na **Neota logiku Studio domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<sub domain>.neotalogic.com/a/<sub application>`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<sub domain>.neotalogic.com/wb`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujte s skutečná adresa URL přihlašování & identifikátor. Zde, doporučujeme vám použít jedinečnou hodnotu řetězce v identifikátoru. Obraťte se na [tým podpory klienta studia logiku Neota](https://www.neotalogic.com/contact-us/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/neotalogicstudio-tutorial/tutorial_general_400.png)

6. K získání jednotného přihlašování nakonfigurovaný pro vaše aplikace, obraťte se [Neota logiku Studio podpora](https://www.neotalogic.com/contact-us/) týmu a poskytněte s Stáhnout **soubor XML s metadaty** souboru.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/neotalogicstudio-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/neotalogicstudio-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/neotalogicstudio-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/neotalogicstudio-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-neota-logic-studio-test-user"></a>Vytvoření zkušebního uživatele Neota logiku Studio

V této části vytvoříte uživatele volal Britta Simon v Neota logiku Studio. Práce s [tým podpory klienta studia logiku Neota](https://www.neotalogic.com/contact-us/) v platformě Neota logiku Studio přidejte uživatele. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Neota logiku Studio.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Studio Neota logiku, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Neota logiku Studio**.

    ![Konfigurovat jednotné přihlašování](./media/neotalogicstudio-tutorial/tutorial_neotalogicstudio_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Klikněte na dlaždici Neota logiku Studio na přístupovém panelu, budete přesměrováni na stránku přihlášení organizace. Po úspěšném přihlášení můžete se být přihlášení k aplikaci logiky Studio Neota. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](https://msdn.microsoft.com/library/dn308586).

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/neotalogicstudio-tutorial/tutorial_general_01.png
[2]: ./media/neotalogicstudio-tutorial/tutorial_general_02.png
[3]: ./media/neotalogicstudio-tutorial/tutorial_general_03.png
[4]: ./media/neotalogicstudio-tutorial/tutorial_general_04.png

[100]: ./media/neotalogicstudio-tutorial/tutorial_general_100.png

[200]: ./media/neotalogicstudio-tutorial/tutorial_general_200.png
[201]: ./media/neotalogicstudio-tutorial/tutorial_general_201.png
[202]: ./media/neotalogicstudio-tutorial/tutorial_general_202.png
[203]: ./media/neotalogicstudio-tutorial/tutorial_general_203.png

