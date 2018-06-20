---
title: 'Kurz: Azure Active Directory integrace s QPrism | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a QPrism.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 9b46eaa83e8e3fab7ca4e2755c48074db1559760
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226965"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Kurz: Azure Active Directory integrace s QPrism

V tomto kurzu zjistěte, jak integrovat QPrism s Azure Active Directory (Azure AD).

Integrace QPrism s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k QPrism.
- Můžete povolit uživatelům automaticky získat přihlášený k QPrism (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě: portál Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s QPrism, potřebujete následující položky:

- Předplatné služby Azure AD
- QPrism jednotné přihlašování povolené předplatné

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkční prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání QPrism z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-qprism-from-the-gallery"></a>Přidat QPrism z Galerie
Při konfiguraci integrace QPrism do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS QPrism z galerie.

**Chcete-li přidat QPrism z galerie:**

1. V [portál Azure](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. V horní části dialogového okna Přidat novou aplikaci, vyberte **novou aplikaci**.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **QPrism**a vyberte **QPrism** z panelu výsledek. Pak klikněte na tlačítko **přidat** tuto aplikaci přidat.

    ![QPrism v seznamu výsledků](./media/qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s QPrism, podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, kdo příslušného uživatele v QPrism je pro uživatele ve službě Azure AD. Jinými slovy musí být propojená vztah mezi uživatele Azure AD a související uživatelské v QPrism.

Chcete-li vytvořit tento vztah v QPrism, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno**.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s QPrism, proveďte následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on) umožňující uživatelům používat tuto funkci.
2. [Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user) k testování Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření zkušebního uživatele QPrism](#create-a-qprism-test-user) tak, aby měl protějšek Britta Simon v QPrism, který je spojen s Azure AD reprezentace daného uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user) povolit Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#test-single-sign-on) ověřit, zda funguje konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci QPrism.

1. Na portálu Azure na **QPrism** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogové okno, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/qprism-tutorial/tutorial_qprism_samlbase.png)

3. V **QPrism domény a adresy URL** část, postupujte takto:

    ![QPrism domény a adresy URL jednotné přihlašování informace](./media/qprism-tutorial/tutorial_qprism_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL, která používá následující vzorec: `https://<customer domain>.qmyzone.com/login`

    b. V **identifikátor** textového pole zadejte adresu URL, která používá následující vzorec: `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat skutečným identifikátorem a adresa URL přihlašování. Obraťte se na [tým podpory QPrism klienta](mailto:qsupport-ce@quatrro.com) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.

     ![Odkaz ke stažení certifikátu](./media/qprism-tutorial/tutorial_qprism_certificate.png)

5. Vyberte **Uložit**.

    ![Konfigurovat jednotné přihlašování tlačítko Uložit](./media/qprism-tutorial/tutorial_general_400.png)
    
6. Konfigurace jednotného přihlašování na **QPrism** straně, budete muset odeslat **adresu Url aplikace federační Metadata** k [tým podpory QPrism](mailto:qsupport-ce@quatrro.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD:**

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](./media/qprism-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/qprism-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, v horní části **všichni uživatelé** dialogové okno, vyberte **přidat**.

    ![Tlačítko Přidat](./media/qprism-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, postupujte takto:

    ![Dialogové okno uživatele](./media/qprism-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-qprism-test-user"></a>Vytvoření zkušebního uživatele QPrism

V této části vytvoříte volal Britta Simon v QPrism uživatele. Pracovat [tým podpory QPrism](mailto:qsupport-ce@quatrro.com) pro přidání uživatelů QPrism platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu QPrism.

![Přiřadit role uživatele][200] 

**Chcete-li přiřadit Britta Simon QPrism:**

1. Na portálu Azure otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře. Přejděte na **podnikové aplikace, které**a vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **QPrism**.

    ![V seznamu aplikací na QPrism odkaz](./media/qprism-tutorial/tutorial_qprism_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **Přidat**. Potom v části **přidat přiřazení**, vyberte **uživatelů a skupin**.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogové okno, vyberte **Britta Simon** v **uživatelé** seznamu.

6. Na **uživatelů a skupin** dialogové okno, vyberte **vyberte**.

7. V části **přidat přiřazení**, vyberte **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Na přístupovém panelu když vyberete dlaždici QPrism můžete by měl získat automaticky přihlášeni do vaší aplikace QPrism.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/qprism-tutorial/tutorial_general_01.png
[2]: ./media/qprism-tutorial/tutorial_general_02.png
[3]: ./media/qprism-tutorial/tutorial_general_03.png
[4]: ./media/qprism-tutorial/tutorial_general_04.png

[100]: ./media/qprism-tutorial/tutorial_general_100.png

[200]: ./media/qprism-tutorial/tutorial_general_200.png
[201]: ./media/qprism-tutorial/tutorial_general_201.png
[202]: ./media/qprism-tutorial/tutorial_general_202.png
[203]: ./media/qprism-tutorial/tutorial_general_203.png

