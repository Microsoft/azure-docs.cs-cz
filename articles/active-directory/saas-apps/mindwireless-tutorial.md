---
title: 'Kurz: Azure Active Directory integrace s mindWireless | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a mindWireless.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd00a339-27c9-4904-b66f-a95bf597ac3c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: 293a8921e64bae03d036f847d2f9138d88ebef4a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210254"
---
# <a name="tutorial-azure-active-directory-integration-with-mindwireless"></a>Kurz: Azure Active Directory integrace s mindWireless

V tomto kurzu zjistěte, jak integrovat mindWireless s Azure Active Directory (Azure AD).

Integrace mindWireless s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k mindWireless.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k mindWireless (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s mindWireless, potřebujete následující položky:

- Předplatné služby Azure AD
- MindWireless jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání mindWireless z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-mindwireless-from-the-gallery"></a>Přidání mindWireless z Galerie
Při konfiguraci integrace mindWireless do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS mindWireless z galerie.

**Pokud chcete přidat mindWireless z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **mindWireless**, vyberte **mindWireless** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![mindWireless v seznamu výsledků](./media/mindwireless-tutorial/tutorial_mindwireless_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s mindWireless podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v mindWireless je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v mindWireless musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s mindWireless, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele mindWireless](#create-a-mindwireless-test-user)**  – Pokud chcete mít protějšek Britta Simon v mindWireless propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci mindWireless.

**Ke konfiguraci Azure AD jednotné přihlašování s mindWireless, proveďte následující kroky:**

1. Na portálu Azure na **mindWireless** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/mindwireless-tutorial/tutorial_mindwireless_samlbase.png)

3. Na **mindWireless domény a adresy URL** část, proveďte následující kroky:

    ![jednotné přihlašování informace mindWireless domény a adresy URL](./media/mindwireless-tutorial/tutorial_mindwireless_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.mwsmart.com/`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Obraťte se na [tým podpory mindWireless](mailto:sdulloor@mindwireless.com) k získání těchto hodnot.

4. Aplikace mindWireless očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML.

5. Následující snímek obrazovky ukazuje příklad pro ni. Název deklarace identity se vždycky být **ID zaměstnance** a hodnoty, které jsme jsou namapovány user.employeeid, který obsahuje EmployeeID uživatele. Zde namapování uživatele z Azure AD na mindWireless probíhá na EmployeeID ale můžete ji namapovat na jinou hodnotu také podle nastavení aplikace. Můžete pracovat [tým podpory mindWireless](mailto:sdulloor@mindwireless.com) nejprve k použijte správný identifikátor uživatele a mapování danou hodnotu s **ID zaměstnance** deklarací identity.

    ![Konfigurovat jednotné přihlašování](./media/mindwireless-tutorial/tutorial_attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je vidět na předchozím obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu | Hodnota Namespace |
    | -------------- | --------------- | ----------------|
    | ID zaměstnance | User.EmployeeID | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/mindwireless-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/mindwireless-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. V **Namespace** textovému poli, zadejte hodnotu oboru názvů, který je uvedený na příslušném řádku.
    
    e. Klikněte na tlačítko **OK**.
    
7. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/mindwireless-tutorial/tutorial_mindwireless_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/mindwireless-tutorial/tutorial_general_400.png)

9. Na **mindWireless konfigurace** klikněte na tlačítko **konfigurace mindWireless** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![mindWireless konfigurace](./media/mindwireless-tutorial/tutorial_mindwireless_configure.png) 

10. Konfigurace jednotného přihlašování na **mindWireless** straně, budete muset odeslat stažené **Certificate(Base64), SAML jeden přihlašování adresa URL služby**, a **SAML Entity ID** na [tým podpory mindWireless](mailto:sdulloor@mindwireless.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/mindwireless-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/mindwireless-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/mindwireless-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/mindwireless-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-mindwireless-test-user"></a>Vytvoření zkušebního uživatele mindWireless

V této části vytvoříte volal Britta Simon v mindWireless uživatele. Práce s [tým podpory mindWireless](mailto:sdulloor@mindwireless.com) přidat uživatele do mindWireless platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k mindWireless.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon mindWireless, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **mindWireless**.

    ![V seznamu aplikací na mindWireless odkaz](./media/mindwireless-tutorial/tutorial_mindwireless_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici mindWireless na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci mindWireless.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mindwireless-tutorial/tutorial_general_01.png
[2]: ./media/mindwireless-tutorial/tutorial_general_02.png
[3]: ./media/mindwireless-tutorial/tutorial_general_03.png
[4]: ./media/mindwireless-tutorial/tutorial_general_04.png

[100]: ./media/mindwireless-tutorial/tutorial_general_100.png

[200]: ./media/mindwireless-tutorial/tutorial_general_200.png
[201]: ./media/mindwireless-tutorial/tutorial_general_201.png
[202]: ./media/mindwireless-tutorial/tutorial_general_202.png
[203]: ./media/mindwireless-tutorial/tutorial_general_203.png

