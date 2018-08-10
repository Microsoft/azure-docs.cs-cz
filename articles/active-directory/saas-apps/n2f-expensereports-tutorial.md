---
title: 'Kurz: Integrace Azure Active Directory s N2F - výdajů sestavy | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a N2F - sestavy výdajů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 27fb299bc3bbbbf75bdf40ae02eac627763ce6d4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006326"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Kurz: Integrace Azure Active Directory s N2F - výdajů sestavy

V tomto kurzu se dozvíte, jak integrovat N2F - sestavy výdajů se službou Azure Active Directory (Azure AD).

Integrace N2F - vyúčtování s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k N2F - sestavy výdajů.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k N2F - vyúčtování (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s N2F - vyúčtování, potřebujete následující položky:

- Předplatné Azure AD
- N2F - vyúčtování jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání N2F - vyúčtování z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Přidání N2F - vyúčtování z Galerie

Konfigurace integrace N2F - vyúčtování do služby Azure AD, budete muset přidat N2F - výdajů v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat N2F - vyúčtování z galerie, postupujte následovně:**

1. V ** [webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **N2F - vyúčtování**vyberte **N2F - vyúčtování** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![N2F - sestavy výdajů v seznamu výsledků](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s N2F - vyúčtování podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co protějšek uživatel v N2F – vyúčtování je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v N2F - výdajů sestavy musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s N2F - sestavy výdajů, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on) ** – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) ** – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření N2F – vyúčtování testovací uživatele](#create-a-n2f---expense-reports-test-use) ** – aby protějšek Britta Simon v N2F - sestavy výdajů připojený k Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) ** – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on) ** – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich N2F – aplikace sestavy výdajů.

**Ke konfiguraci Azure AD jednotné přihlašování s N2F - vyúčtování, proveďte následující kroky:**

1. Na webu Azure Portal na **N2F - vyúčtování** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. Na **domény a adresy URL sestavy výdajů N2F -** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, uživatel nebude muset provést všechny kroky, protože aplikace je už předem integrované se službou Azure.

    ![N2F - vyúčtování domény a adresy URL jednotného přihlašování – informace](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![N2F - vyúčtování domény a adresy URL jednotného přihlašování – informace](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://www.n2f.com/app/`

5. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. Na **N2F - sestavy výdajů konfigurace** klikněte na tlačítko **konfigurace N2F - vyúčtování** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID** z **Stručná referenční příručka oddílu.**

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. V okně jiné webové prohlížeče Přihlaste se k vaší N2F - webu společnosti sestavy výdajů jako správce.

9. Klikněte na **nastavení** a pak vyberte **rozšířená nastavení** z rozevíracího seznamu.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure1.png)

10. Vyberte **nastavení účtu** kartu.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure2.png)

11. Vyberte **ověřování** a pak vyberte **+ přidat metodu ověřování** kartu.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure3.png)

12. Vyberte **SAML Microsoft Office 365** jako metodu ověřování.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure4.png)

13. Na **metodu ověřování** části, proveďte následující kroky:

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure5.png)

    a. V **Entity ID** vložit do textového pole **SAML Entity ID** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    b. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-n2f---expense-reports-test-user"></a>Vytvoření N2F – vyúčtování testovací uživatele

Přihlaste se k N2F - vyúčtování, Azure AD uživatelům umožnit, musí být zřízená do N2F – sestavy výdajů. V případě N2F - vyúčtování, zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší N2F - webu společnosti sestavy výdajů jako správce.

2. Klikněte na **nastavení** a pak vyberte **rozšířená nastavení** z rozevíracího seznamu.

   ![N2F - výdajů přidat uživatele](./media/n2f-expensereports-tutorial/configure1.png)

3. Vyberte **uživatelé** kartu v levém navigačním panelu.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/user1.png)

4. Vyberte **+ nový uživatel** kartu.

   ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/user2.png)

5. Na **uživatele** části, proveďte následující kroky:

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/user3.png)

    a. V **e-mailová adresa** textového pole zadejte e-mailovou adresu uživatele, jako je ** brittasimon@contoso.com **.

    b. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    c. V **název** textového pole zadejte jméno uživatele, jako je **BrittaSimon**.

    d. Zvolte **Role, správce s přímým přístupem (N + 1)**, a **dělení** podle požadavků vaší organizace.

    e. Klikněte na tlačítko **ověřením a odeslat pozvánky**.

    > [!NOTE]
    > Pokud máte jakékoli problémy při přidávání uživatele, obraťte se prosím [N2F - vyúčtování tým podpory](mailto:support@n2f.com)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k N2F – sestavy výdajů.

![Přiřazení role uživatele][200]

**Přiřadit Britta Simon N2F - vyúčtování, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **N2F - vyúčtování**.

    ![Sestavy výdajů N2F - odkaz v seznamu aplikací](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete N2F – vyúčtování dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší N2F – aplikace sestavy výdajů.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

