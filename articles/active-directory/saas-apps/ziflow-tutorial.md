---
title: 'Kurz: Azure Active Directory integrace s Ziflow | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: 5a50c578bd2b8a0479365f1543b9db741080f19f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228954"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Kurz: Azure Active Directory integrace s Ziflow

V tomto kurzu zjistěte, jak integrovat Ziflow s Azure Active Directory (Azure AD).

Integrace Ziflow s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Ziflow.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Ziflow (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Ziflow, potřebujete následující položky:

- Předplatné služby Azure AD
- Ziflow jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Ziflow z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ziflow-from-the-gallery"></a>Přidání Ziflow z Galerie
Při konfiguraci integrace Ziflow do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Ziflow z galerie.

**Pokud chcete přidat Ziflow z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Ziflow**, vyberte **Ziflow** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Ziflow v seznamu výsledků](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Ziflow podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Ziflow je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Ziflow musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Ziflow, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Ziflow](#create-a-ziflow-test-user)**  – Pokud chcete mít protějšek Britta Simon v Ziflow propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Ziflow.

**Ke konfiguraci Azure AD jednotné přihlašování s Ziflow, proveďte následující kroky:**

1. Na portálu Azure na **Ziflow** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. Na **Ziflow domény a adresy URL** část, proveďte následující kroky:

    ![Ziflow domény a adresy URL jednotné přihlašování informace](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. V **přihlásit na adrese URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.ziflow.io/#/login-sso/<Unique ID>`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `urn:auth0:ziflow-production:<Unique ID>`

    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné. Aktualizujte hodnotě jedinečné ID v identifikátor a adresa URL přihlašování se skutečnou hodnotu, která se vysvětluje dále v tomto kurzu. Obraťte se na [tým podpory Ziflow](mailto:support@ziflow.com) pro hodnotu subdomény v adrese URL přihlašování.
    
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/ziflow-tutorial/tutorial_general_400.png)

6. Na **Ziflow konfigurace** klikněte na tlačítko **konfigurace Ziflow** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace Ziflow](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. V okně prohlížeče jiný web a přihlášení k Ziflow jako správce zabezpečení.


8. Klikněte na miniaturu v pravém horním rohu a pak klikněte na tlačítko **spravovat účet**.

    ![Spravovat konfiguraci Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. V levé horní části, klikněte na **jednotné přihlašování**.

    ![Ziflow konfigurace přihlášení](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. Na **jednotné přihlašování** proveďte následující kroky:

    ![Konfigurace jednoho Ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Vyberte **typ** jako **SAML2.0**.

    b.In **přihlášení v adrese URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    c. Nahrát kódování base-64 kódovaného certifikátu, kterou jste si stáhli z portálu Azure do **X509 podpisový certifikát**.

    d. V **přihlašovací adresy URL odhlašovací stránky** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    e. Z **nastavení konfigurace pro váš poskytovatel identifikátor** , zkopírovat zvýrazněná jedinečnou hodnotu ID a připojit se identifikátor a adresa URL v přihlašování **Ziflow domény a adresy URL části** na Portál Azure.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/ziflow-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/ziflow-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/ziflow-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/ziflow-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-ziflow-test-user"></a>Vytvoření zkušebního uživatele Ziflow

Pokud chcete povolit uživatelům Azure AD přihlášení k Ziflow, musí být zřízená do Ziflow. V Ziflow zřizování je ruční úloha.

K poskytnutí uživatelského účtu, proveďte následující kroky:

1. Přihlaste se k Ziflow jako správce zabezpečení.

2. Přejděte na **osoby** nahoře.

    ![Konfigurace Ziflow osoby](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klikněte na tlačítko **přidat** a pak klikněte na **přidat uživatele**.

    ![Přidání uživatele Ziflow konfigurace](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Na **přidat uživatele** místní, proveďte následující kroky:

    ![Přidání uživatele Ziflow konfigurace](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. V **e-mailu** textové pole, zadejte e-mailu uživatele jako brittasimon@contoso.com.

    b. V **křestní jméno** textové pole, zadejte jméno uživatele jako Britta.

    c. V **příjmení** text zadejte příjmení uživatele jako Simon.

    d. Vyberte vaši Ziflow roli.

    e. Klikněte na tlačítko **uživatele přidat 1**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a dodržuje odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Ziflow.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Ziflow, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Ziflow**.

    ![V seznamu aplikací na Ziflow odkaz](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Ziflow na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Ziflow.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

