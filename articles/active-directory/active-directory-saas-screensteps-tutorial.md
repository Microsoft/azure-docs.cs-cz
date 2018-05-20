---
title: 'Kurz: Azure Active Directory integrace s ScreenSteps | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 444bc2d0724f8e7efc453c9efa577908822a4df2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Kurz: Azure Active Directory integrace s ScreenSteps

V tomto kurzu zjistěte, jak integrovat ScreenSteps s Azure Active Directory (Azure AD).

Integrace ScreenSteps s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k ScreenSteps.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ScreenSteps (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ScreenSteps, potřebujete následující položky:

- Předplatné služby Azure AD
- ScreenSteps jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ScreenSteps z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-screensteps-from-the-gallery"></a>Přidání ScreenSteps z Galerie
Při konfiguraci integrace ScreenSteps do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS ScreenSteps z galerie.

**Pokud chcete přidat ScreenSteps z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ScreenSteps**, vyberte **ScreenSteps** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![ScreenSteps v seznamu výsledků](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ScreenSteps podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ScreenSteps je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v ScreenSteps musí navázat.

V ScreenSteps, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ScreenSteps, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ScreenSteps](#create-a-screensteps-test-user)**  – Pokud chcete mít protějšek Britta Simon v ScreenSteps propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci ScreenSteps.

**Ke konfiguraci Azure AD jednotné přihlašování s ScreenSteps, proveďte následující kroky:**

1. Na portálu Azure na **ScreenSteps** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. Na **ScreenSteps domény a adresy URL** část, proveďte následující kroky:

    ![ScreenSteps domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu skutečné přihlašování adresu URL, která se vysvětluje dále v tomto kurzu. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-screensteps-tutorial/tutorial_general_400.png)

6. Na **ScreenSteps konfigurace** klikněte na tlačítko **konfigurace ScreenSteps** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti ScreenSteps.

8. Klikněte na tlačítko **nastavení účtu**.

    ![Správa účtů](./media/active-directory-saas-screensteps-tutorial/ic778523.png "Správa účtů")

9. Klikněte na tlačítko **jednotného přihlašování**.

    ![Vzdálené ověření](./media/active-directory-saas-screensteps-tutorial/ic778524.png "vzdáleného ověřování")

10. Klikněte na tlačítko **vytvořit Endpoint přihlašování**.

    ![Vzdálené ověření](./media/active-directory-saas-screensteps-tutorial/ic778525.png "vzdáleného ověřování")

11. V **vytvořit jeden přihlašování koncový bod** část, proveďte následující kroky:

    ![Vytvořit koncový bod ověřování](./media/active-directory-saas-screensteps-tutorial/ic778526.png "vytvořit koncový bod ověřování")
    
    a. V **název** textovému poli, zadejte název.
    
    b. Z **režimu** seznamu, vyberte **SAML**.
    
    c. Klikněte na možnost **Vytvořit**.

12. **Upravit** nový koncový bod.

    ![Upravit koncový bod](./media/active-directory-saas-screensteps-tutorial/ic778528.png "upravit koncový bod")

13. V **upravit jeden přihlašování koncový bod** část, proveďte následující kroky:

    ![Koncový bod ověřování. vzdálený](./media/active-directory-saas-screensteps-tutorial/ic778527.png "koncový bod vzdálené ověřování.")

    a. Klikněte na tlačítko **nový soubor certifikátu SAML nahrávání**a pak nahrajte certifikát, který jste si stáhli z portálu Azure.
    
    b. Vložení **SAML jeden přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure do **vzdálené adresy URL pro přihlášení** textové pole.
    
    c. Vložení **Sign-Out URL** hodnotu, kterou jste zkopírovali z portálu Azure do **URL odhlašovací stránky** textové pole.
    
    d. Vyberte **skupiny** přiřazovat uživatele k když jsou zřízené.
    
    e. Klikněte na tlačítko **aktualizace**.

    f. Kopírování **SAML příjemce URL** do schránky a vložte do **přihlašovací adresa URL** textového pole v **ScreenSteps domény a adresy URL** části.
    
    g. Vraťte se na **upravit Endpoint přihlašování**.
    
    h. Klikněte **nastavit jako výchozí pro účet** tlačítko použít tento koncový bod pro všechny uživatele, kteří se připojují do ScreenSteps. Případně můžete kliknout na **přidat na web** tlačítko použít tento koncový bod pro určité weby v **ScreenSteps**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-screensteps-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-screensteps-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-screensteps-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-screensteps-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-screensteps-test-user"></a>Vytvoření zkušebního uživatele ScreenSteps

V této části vytvoříte volal Britta Simon v ScreenSteps uživatele. Práce s [tým podpory ScreenSteps klienta](https://www.screensteps.com/contact) přidat uživatele do ScreenSteps platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ScreenSteps.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon ScreenSteps, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ScreenSteps**.

    ![V seznamu aplikací na ScreenSteps odkaz](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici ScreenSteps na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci ScreenSteps.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_203.png

