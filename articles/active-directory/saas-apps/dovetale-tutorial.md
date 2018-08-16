---
title: 'Kurz: Integrace Azure Active Directory se službou Dovetale | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dovetale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81da50c3-df94-458a-8b6a-a30827ee6358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: dd2aa699c22518ebbe7f29ba8dfd296da7385476
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161974"
---
# <a name="tutorial-azure-active-directory-integration-with-dovetale"></a>Kurz: Integrace Azure Active Directory se službou Dovetale

V tomto kurzu se dozvíte, jak integrovat Dovetale s Azure Active Directory (Azure AD).

Dovetale integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Dovetale.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Dovetale (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Dovetale, potřebujete následující položky:

- Předplatné Azure AD
- Dovetale jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Dovetale z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-dovetale-from-the-gallery"></a>Přidání Dovetale z Galerie

Konfigurace integrace Dovetale do služby Azure AD, budete muset přidat Dovetale z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Dovetale z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Dovetale**vyberte **Dovetale** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Dovetale v seznamu výsledků](./media/dovetale-tutorial/tutorial_dovetale_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Dovetale podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Dovetale je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Dovetale potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Dovetale, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Dovetale](#create-a-dovetale-test-user)**  – Pokud chcete mít protějšek Britta Simon Dovetale, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Dovetale.

**Ke konfiguraci Azure AD jednotné přihlašování s Dovetale, proveďte následující kroky:**

1. Na webu Azure Portal na **Dovetale** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/dovetale-tutorial/tutorial_dovetale_samlbase.png)

3. Na **Dovetale domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, uživatel nebude muset provést všechny kroky, protože aplikace je už předem integrované se službou Azure:

    ![Dovetale domény a adresy URL jednotného přihlašování – informace](./media/dovetale-tutorial/tutorial_dovetale_url1.png)

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Dovetale domény a adresy URL jednotného přihlašování – informace](./media/dovetale-tutorial/tutorial_dovetale_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `<COMPANYNAME>.dovetale.com`

    > [!NOTE]
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Dovetale klienta](mailto:support@dovetale.com) tuto výhodu získáte.

5. Dovetale aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/dovetale-tutorial/tutorial_attribute.png)

6. Klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtávací políčko ve **atributy uživatele** rozbalte atributy. Proveďte následující kroky na všechny zobrazené atributy-

    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |
    | e-mail | User.Mail |
    | křestní_jméno | user.givenname |
    | jméno | user.userprincipalname |
    | Příjmení | user.surname |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/dovetale-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/dovetale-tutorial/tutorial_attribute_05.png)

    b. V **název** textové pole, typ **název atributu** zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Ponechte **obor názvů** prázdnou hodnotu.

    e. Klikněte na tlačítko **OK**.

7. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/dovetale-tutorial/tutorial_dovetale_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/dovetale-tutorial/tutorial_general_400.png)

9. Na **Dovetale konfigurace** klikněte na tlačítko **nakonfigurovat Dovetale** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Dovetale](./media/dovetale-tutorial/tutorial_dovetale_configure.png)

10. Ke konfiguraci jednotného přihlašování na **Dovetale** straně, je nutné odeslat **adresa Url federačních metadat aplikace, SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** k [tým podpory Dovetale](mailto:support@dovetale.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/dovetale-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/dovetale-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/dovetale-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/dovetale-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-dovetale-test-user"></a>Vytvoření zkušebního uživatele Dovetale

Cílem této části je vytvořte uživatele Britta Simon v Dovetale. Dovetale podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Dovetale, pokud ještě neexistuje.

> [!Note]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Dovetale](mailto:support@dovetale.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Dovetale použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200]

**Přiřadit Dovetale Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Dovetale**.

    ![Odkaz Dovetale v seznamu aplikací](./media/dovetale-tutorial/tutorial_dovetale_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Dovetale na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Dovetale.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dovetale-tutorial/tutorial_general_01.png
[2]: ./media/dovetale-tutorial/tutorial_general_02.png
[3]: ./media/dovetale-tutorial/tutorial_general_03.png
[4]: ./media/dovetale-tutorial/tutorial_general_04.png

[100]: ./media/dovetale-tutorial/tutorial_general_100.png

[200]: ./media/dovetale-tutorial/tutorial_general_200.png
[201]: ./media/dovetale-tutorial/tutorial_general_201.png
[202]: ./media/dovetale-tutorial/tutorial_general_202.png
[203]: ./media/dovetale-tutorial/tutorial_general_203.png