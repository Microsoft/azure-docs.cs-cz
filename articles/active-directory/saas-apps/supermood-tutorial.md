---
title: 'Kurz: Azure Active Directory integrace s Supermood | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Supermood.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 9cd6a373f23c69f920d0e46bad368f17c8d21035
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214991"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>Kurz: Azure Active Directory integrace s Supermood

V tomto kurzu zjistěte, jak integrovat Supermood s Azure Active Directory (Azure AD).

Integrace Supermood s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Supermood.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Supermood (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Supermood, potřebujete následující položky:

- Předplatné služby Azure AD
- Supermood jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Supermood z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-supermood-from-the-gallery"></a>Přidání Supermood z Galerie
Chcete-li nakonfigurovat integraci Supermood do služby Azure AD, přidejte Supermood z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Supermood z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Supermood**, vyberte **Supermood** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Supermood v seznamu výsledků](./media/supermood-tutorial/tutorial_supermood_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Supermood podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Supermood je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Supermood musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Supermood, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Supermood](#create-a-supermood-test-user)**  – Pokud chcete mít protějšek Britta Simon v Supermood propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Supermood.

**Ke konfiguraci Azure AD jednotné přihlašování s Supermood, proveďte následující kroky:**

1. Na portálu Azure na **Supermood** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/supermood-tutorial/tutorial_supermood_samlbase.png)

3. Na **Supermood domény a adresy URL** část, proveďte následující kroky:

    ![Supermood domény a adresy URL jednotné přihlašování informace](./media/supermood-tutorial/tutorial_supermood_url.png)

    a. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**.

    b. Pokud chcete nakonfigurovat aplikace **IDP** spustil v režimu **předávání stavu** textovému poli, zadejte adresu URL: `https://supermood.co/auth/sso/saml20`

    c. Pokud chcete nakonfigurovat aplikace **SP** spustil v režimu **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://supermood.co/app/#!/loginv2`

4. Aplikace supermood očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování](./media/supermood-tutorial/tutorial_supermood_attribute.png)

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | FirstName | user.givenname |
    | Příjmení | user.surname |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/supermood-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/supermood-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    d. Klikněte na tlačítko **Ok**

6. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/supermood-tutorial/tutorial_supermood_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/supermood-tutorial/tutorial_general_400.png)

8. Přejděte na panelem správce pro vaše Supermood.co jako správce zabezpečení.

9. Klikněte na **Můj účet** (dolní část vlevo) a **jednotné přihlašování na (SSO)**.

    ![Jeden certifikát](./media/supermood-tutorial/tutorial_supermood_single.png)
10. Na **vaše SAML 2.0 konfigurace**, klikněte na tlačítko **přidat konfigurace aplikace SAML 2.0 pro e-mailovou doménu**.

    ![Přidat certifikát](./media/supermood-tutorial/tutorial_supermood_add.png)

11. Na **přidat konfigurace aplikace SAML 2.0 pro e-mailovou doménu**. část, proveďte následující kroky:

    ![Certifikát saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. V **e-mailovou doménu pro tohoto zprostředkovatele Identity** textovému poli, zadejte doménu.

    b. V **použijte adresu URL metadat** textovému poli, Vložit **adresu Url aplikace federační Metadata** který jste zkopírovali z portálu Azure.

    c. Klikněte na tlačítko **Add** (Přidat).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/supermood-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/supermood-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/supermood-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/supermood-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-supermood-test-user"></a>Vytvoření zkušebního uživatele Supermood

V této části vytvoříte volal Britta Simon v Supermood uživatele. Supermood podporuje za běhu zřizování, který je ve výchozím nastavení povolena pro uživatele, jejichž e-mailů patří do domén, které jsou přidány během konfigurace na konci Supermood. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Supermood, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory Supermood](mailto:hello@supermood.fr).


### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Supermood.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Supermood, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Supermood**.

    ![V seznamu aplikací na Supermood odkaz](./media/supermood-tutorial/tutorial_supermood_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Supermood na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Supermood.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/supermood-tutorial/tutorial_general_01.png
[2]: ./media/supermood-tutorial/tutorial_general_02.png
[3]: ./media/supermood-tutorial/tutorial_general_03.png
[4]: ./media/supermood-tutorial/tutorial_general_04.png

[100]: ./media/supermood-tutorial/tutorial_general_100.png

[200]: ./media/supermood-tutorial/tutorial_general_200.png
[201]: ./media/supermood-tutorial/tutorial_general_201.png
[202]: ./media/supermood-tutorial/tutorial_general_202.png
[203]: ./media/supermood-tutorial/tutorial_general_203.png

