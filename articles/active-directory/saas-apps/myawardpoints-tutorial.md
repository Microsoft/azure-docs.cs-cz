---
title: 'Kurz: Integrace Azure Active Directory s Můj tým Sub/horní horní ocenění body | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Můj tým Sub/horní horní ocenění body.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeedes
ms.openlocfilehash: 9e3e3ff05836cb49d46a25ef3fa55d40a0bd134f
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184745"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Kurz: Integrace Azure Active Directory s Můj tým Sub/horní horní ocenění body

V tomto kurzu se dozvíte, jak integrovat Můj tým Sub/horní horní ocenění body se službou Azure Active Directory (Azure AD).

Můj tým Sub/horní horní ocenění bodů integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Můj tým Sub/horní horní ocenění body.
- Uživatele, aby automaticky získat přihlášeného Moje ocenění body horní Sub/horní týmu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Můj tým Sub/horní horní ocenění body, potřebujete následující položky:

- Předplatné Azure AD
- Můj tým Sub/horní horní ocenění body jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Můj tým Sub/horní horní ocenění body z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Přidání Můj tým Sub/horní horní ocenění body z Galerie

Pokud chcete nakonfigurovat integrace Můj tým Sub/horní horní ocenění body do služby Azure AD, potřebujete přidat Můj tým Sub/horní horní ocenění body z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Můj tým Sub/horní horní ocenění body z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Můj tým Sub/horní horní ocenění body**vyberte **Můj tým Sub/horní horní ocenění body** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Sub/horní týmu ocenění body nahoru v seznamu výsledků](./media/myawardpoints-tutorial/tutorial_myawardpoints_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Moje ocenění body horní Sub/horní týmu na základě testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Můj tým Sub/horní horní ocenění body je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatel v mé ocenění body horní Sub/horní týmu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Můj tým Sub/horní horní ocenění body, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Můj tým Sub/horní horní ocenění body](#create-a-my-award-points-top-subtop-team-test-user)**  – Pokud chcete mít protějšek Britta Simon v mé ocenění body horní Sub/horní tým, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Můj tým Sub/horní horní ocenění body.

**Ke konfiguraci Azure AD jednotné přihlašování s Můj tým Sub/horní horní ocenění body, proveďte následující kroky:**

1. Na webu Azure Portal na **Můj tým Sub/horní horní ocenění body** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/myawardpoints-tutorial/tutorial_myawardpoints_samlbase.png)

3. Na **Moje ocenění body horní Sub/horní týmu domény a adresy URL** části, proveďte následující kroky:

    ![Moje ocenění body horní Sub/horní týmu domény a adresy URL jednotného přihlašování – informace](./media/myawardpoints-tutorial/tutorial_myawardpoints_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<SAMLENTITYID>`

    > [!NOTE]
    > Přihlašování byste použili hodnotu URL není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Moje ocenění body horní Sub/horní týmu klienta](mailto:myawardpoints@biworldwide.com) tuto výhodu získáte.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/myawardpoints-tutorial/tutorial_myawardpoints_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/myawardpoints-tutorial/tutorial_general_400.png)

6. Ke konfiguraci jednotného přihlašování na **Můj tým Sub/horní horní ocenění body** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory Můj tým Sub/horní horní ocenění body](mailto:myawardpoints@biworldwide.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/myawardpoints-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/myawardpoints-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/myawardpoints-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/myawardpoints-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-my-award-points-top-subtop-team-test-user"></a>Vytvoření zkušebního uživatele Můj tým Sub/horní horní ocenění body

V této části vytvořte uživatele Britta Simon v Můj tým Sub/horní horní ocenění body. Práce s [tým podpory Můj tým Sub/horní horní ocenění body](mailto:myawardpoints@biworldwide.com) přidat uživatele na platformě Můj tým Sub/horní horní ocenění body. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu Můj tým Sub/horní horní ocenění body.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit Můj tým Sub/horní horní ocenění body, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Můj tým Sub/horní horní ocenění body**.

    ![Odkaz Můj tým Sub/horní horní ocenění body v seznamu aplikací](./media/myawardpoints-tutorial/tutorial_myawardpoints_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Můj tým Sub/horní horní ocenění body na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Můj tým Sub/horní horní ocenění body.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/myawardpoints-tutorial/tutorial_general_01.png
[2]: ./media/myawardpoints-tutorial/tutorial_general_02.png
[3]: ./media/myawardpoints-tutorial/tutorial_general_03.png
[4]: ./media/myawardpoints-tutorial/tutorial_general_04.png

[100]: ./media/myawardpoints-tutorial/tutorial_general_100.png

[200]: ./media/myawardpoints-tutorial/tutorial_general_200.png
[201]: ./media/myawardpoints-tutorial/tutorial_general_201.png
[202]: ./media/myawardpoints-tutorial/tutorial_general_202.png
[203]: ./media/myawardpoints-tutorial/tutorial_general_203.png