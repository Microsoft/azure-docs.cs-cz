---
title: 'Kurz: Azure Active Directory integrace s Trisotech digitální Enterprise Server | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trisotech digitální Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 75a5713af0f5c6bbfca95bca2a345509cc154f4c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224952"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Kurz: Azure Active Directory integrace s Trisotech digitální Enterprise Server

V tomto kurzu zjistěte, jak integrovat Trisotech digitální Enterprise Server se službou Azure Active Directory (Azure AD).

Integrace Trisotech digitální Enterprise Server s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Trisotech digitální Enterprise Server.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Trisotech digitální Server Enterprise (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Trisotech digitální Enterprise Server, potřebujete následující položky:

- Předplatné služby Azure AD
- Trisotech digitální Enterprise Server jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Trisotech digitální Enterprise Server z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Přidání Trisotech digitální Enterprise Server z Galerie
Při konfiguraci integrace Trisotech digitální Enterprise Server do služby Azure AD musíte přidat Server podnikové digitální Trisotech z Galerie si na seznam spravovaných aplikací SaaS.

**Přidat Server podnikové digitální Trisotech z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Trisotech digitální Enterprise Server**, vyberte **Trisotech digitální Enterprise Server** z panelu výsledků klikněte **přidat** tlačítko přidáte aplikace.

    ![Trisotech digitální Enterprise Server v seznamu výsledků](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Trisotech digitální Enterprise Server podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Trisotech digitální Enterprise Server je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Trisotech digitální Enterprise Server je potřeba vytvořit.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Trisotech digitální Enterprise Server, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Trisotech digitální Enterprise Server](#create-a-trisotech-digital-enterprise-server-test-user)**  – Pokud chcete mít protějšek Britta Simon v Trisotech digitální Enterprise Server, který je připojen k reprezentaci Azure AD uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Trisotech digitální Enterprise Server.

**Ke konfiguraci Azure AD jednotné přihlašování s Trisotech digitální Enterprise Server, proveďte následující kroky:**

1. Na portálu Azure na **Trisotech digitální Enterprise Server** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

3. Na **Trisotech digitální Enterprise Server domény a adresy URL** část, proveďte následující kroky:

    ![Trisotech digitální Enterprise Server domény a adresy URL jednotné přihlašování informace](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.trisotech.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Trisotech digitální Enterprise Server Client](mailto:support@trisotech.com) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku. 

    ![Odkaz ke stažení certifikátu](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

6. V okně prohlížeče jiný web Přihlaste se na váš web společnosti Trisotech digitální konfigurace podnikového serveru jako správce.

7. Klikněte na **ikonu nabídky** a pak vyberte **správy**.

    ![Konfigurovat jednotné přihlašování](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

8. Vyberte **poskytovatel uživatele**.

    ![Konfigurovat jednotné přihlašování](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

9. V **uživatele zprostředkovatele konfigurace** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Vyberte **zabezpečené Assertion Markup Language 2 (SAML 2)** z rozevíracího seznamu v **metodu ověřování**.

    b. V **adresu URL metadat** textovému poli, Vložit **adresu Url aplikace federační Metadata** hodnotu, kterou jste zkopírovali formuláři na portálu Azure.

    c. V **ID aplikace** textovému poli, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.trisotech.com`.

    d. Klikněte na **Uložit**.

    e. Zadejte název domény v **povolené domény (prázdný znamená everyone)** textovému poli, automaticky přiřadí licence pro uživatele odpovídající povolené domény

    f. Klikněte na **Uložit**.

 ### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Vytvoření zkušebního uživatele Trisotech digitální Enterprise Server

Cílem této části je vytvoření uživatele volal Britta Simon v Trisotech digitální Enterprise Server. Trisotech digitální Enterprise Server podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Trisotech digitální Enterprise Server, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory Trisotech digitální Enterprise Server](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Trisotech digitální Enterprise Server.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Trisotech digitální Enterprise Server, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Trisotech digitální Enterprise Server**.

    ![V seznamu aplikací na odkaz Trisotech digitální Enterprise Server](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Trisotech digitální Enterprise Server na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Trisotech digitální Enterprise Server.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

