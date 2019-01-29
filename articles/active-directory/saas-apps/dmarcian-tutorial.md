---
title: 'Kurz: Integrace Azure Active Directory s dmarcian | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: jeedes
ms.openlocfilehash: 197e0cceb7457ffdd96ada7d4561de56c7ed10fc
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095647"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Kurz: Integrace Azure Active Directory s dmarcian

V tomto kurzu se dozvíte, jak integrovat dmarcian s Azure Active Directory (Azure AD).

Dmarcian integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k dmarcian.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k dmarcian (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s dmarcian, potřebujete následující položky:

- Předplatné Azure AD
- Dmarcian jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání dmarcian z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-dmarcian-from-the-gallery"></a>Přidání dmarcian z Galerie
Konfigurace integrace dmarcian do služby Azure AD, budete muset přidat dmarcian z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat dmarcian z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **dmarcian**vyberte **dmarcian** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![dmarcian v seznamu výsledků](./media/dmarcian-tutorial/tutorial_dmarcian_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí dmarcian podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v dmarcian je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v dmarcian potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s dmarcian, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele dmarcian](#create-a-dmarcian-test-user)**  – Pokud chcete mít protějšek Britta Simon v dmarcian, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci dmarcian.

**Ke konfiguraci Azure AD jednotné přihlašování s dmarcian, proveďte následující kroky:**

1. Na webu Azure Portal na **dmarcian** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/dmarcian-tutorial/tutorial_dmarcian_samlbase.png)

3. Na **dmarcian domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![dmarcian domény a adresy URL jednotné přihlašování – informace](./media/dmarcian-tutorial/tutorial_dmarcian_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![dmarcian domény a adresy URL jednotné přihlašování – informace](./media/dmarcian-tutorial/tutorial_dmarcian_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizuje o skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL, která je vysvětlen později v tomto kurzu. 

5. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/dmarcian-tutorial/tutorial_dmarcian_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/dmarcian-tutorial/tutorial_general_400.png)
    
7. V okně jiné webové prohlížeče Přihlaste se k dmarcian jako správce zabezpečení.

8. Klikněte na **profilu** v pravém horním rohu a přejít na **Předvolby**.

    ![Předvolby ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

9. Posuňte se dolů a klikněte na **Single Sign-On** části a potom klikněte na **konfigurovat**.

    ![Jedné ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

10. Na **SAML jednotného přihlašování** stránce nastavení **stav** jako **povoleno** a proveďte následující kroky:

    ![Ověřování ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * V části **přidat dmarcian ke zprostředkovateli Identity** klikněte na tlačítko **kopírování** zkopírovat **adresa URL služby Assertion příjemce** pro vaši instanci a vložte ji  **Adresy URL odpovědi** textového pole v **dmarcian části domény a adresy URL** na portálu Azure portal.

    * V části **přidat dmarcian ke zprostředkovateli Identity** klikněte na tlačítko **kopírování** zkopírovat **Entity ID** pro vaši instanci a vložte ji ** Identifikátor** textového pole v **dmarcian části domény a adresy URL** na portálu Azure portal.

    * V části **nastavení ověřování** sekci **metadat zprostředkovatele Identity** vložení textového pole **adresa Url federačních metadat aplikace**, který jste zkopírovali z portálu Azure portal.

    * V části **nastavení ověřování** sekci **příkazy atributů** textového pole vložte adresu url `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    * V části **nastavit adresu URL pro přihlášení** tématu, zkopírujte **přihlašovací adresa URL** pro vaši instanci a vložte ji **přihlašovací adresa URL** textového pole v **dmarcian části domény a adresy URL** na portálu Azure portal.

        > [!Note]
        > Můžete upravit **přihlašovací adresa URL** podle vaší organizace.

    * Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/dmarcian-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/dmarcian-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/dmarcian-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/dmarcian-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-dmarcian-test-user"></a>Vytvoření zkušebního uživatele dmarcian

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k dmarcian, musí být poskytnuty do dmarcian. Zřizování v dmarcian, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k dmarcian jako správce zabezpečení.

2. Klikněte na **profilu** v pravém horním rohu a přejít na **spravovat uživatele**.

    ![Uživatel ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Na pravé straně **jednotné přihlašování uživatelů** části, klikněte na **Add New User**.

    ![Přidat uživatele ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Na **Add New User** automaticky otevírané okno, proveďte následující kroky:

    ![Nový uživatel ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. V **novou e-mailu uživatele** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    b. Pokud chcete udělit práva správce uživateli, vyberte **vytvořit uživatele správcem**.

    c. Klikněte na tlačítko **přidat uživatele**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k dmarcian.

![Přiřazení role uživatele][200] 

**Přiřadit dmarcian Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **dmarcian**.

    ![Odkaz dmarcian v seznamu aplikací](./media/dmarcian-tutorial/tutorial_dmarcian_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici dmarcian na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci dmarcian.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dmarcian-tutorial/tutorial_general_01.png
[2]: ./media/dmarcian-tutorial/tutorial_general_02.png
[3]: ./media/dmarcian-tutorial/tutorial_general_03.png
[4]: ./media/dmarcian-tutorial/tutorial_general_04.png

[100]: ./media/dmarcian-tutorial/tutorial_general_100.png

[200]: ./media/dmarcian-tutorial/tutorial_general_200.png
[201]: ./media/dmarcian-tutorial/tutorial_general_201.png
[202]: ./media/dmarcian-tutorial/tutorial_general_202.png
[203]: ./media/dmarcian-tutorial/tutorial_general_203.png

