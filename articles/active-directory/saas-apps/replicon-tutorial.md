---
title: 'Kurz: Integrace Azure Active Directory se službou Replicon | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 10f4749524c4d4b35b83e5637d35f4ef9a8f9ab6
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041326"
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Kurz: Integrace Azure Active Directory se službou Replicon

V tomto kurzu se dozvíte, jak integrovat Replicon s Azure Active Directory (Azure AD).

Replicon integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Replicon.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Replicon (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Replicon, potřebujete následující položky:

- S předplatným služby Azure AD
- Replicon jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Replicon z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-replicon-from-the-gallery"></a>Přidání Replicon z Galerie
Konfigurace integrace Replicon do služby Azure AD, budete muset přidat Replicon z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Replicon z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Replicon**vyberte **Replicon** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Replicon v seznamu výsledků](./media/replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Replicon podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Replicon je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Replicon potřeba navázat.

V Replicon, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Replicon, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Replicon](#create-a-replicon-test-user)**  – Pokud chcete mít protějšek Britta Simon Replicon, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Replicon.

**Ke konfiguraci Azure AD jednotné přihlašování s Replicon, proveďte následující kroky:**

1. Na webu Azure Portal na **Replicon** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/replicon-tutorial/tutorial_replicon_samlbase.png)

3. Na **Replicon domény a adresy URL** části, proveďte následující kroky:

    ![Replicon domény a adresy URL jednotného přihlašování – informace](./media/replicon-tutorial/tutorial_replicon_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://global.replicon.com/<companyname>`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://global.replicon.com/!/saml2/<companyname>/sso/post`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory Replicon klienta](https://www.replicon.com/customerzone/contact-support) k získání těchto hodnot. 

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/replicon-tutorial/tutorial_replicon_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/replicon-tutorial/tutorial_general_400.png)

6. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Replicon.

7. Konfigurace SAML 2.0, proveďte následující kroky:

    ![Povolit ověřování SAML](./media/replicon-tutorial/ic777805.png "ověřování povolit SAML")

    a. Chcete-li zobrazit **EnableSAML Authentication2** dialogového okna, připojte k adrese URL, po vašeho klíče společnosti: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Následuje schéma úplnou adresu URL: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klikněte na tlačítko **+** rozšířit **v20Configuration** oddílu.

   c. Klikněte na tlačítko **+** rozšířit **metaDataConfiguration** oddílu.

   d. Klikněte na tlačítko **zvolit soubor**, a vyberte soubor XML metadat zprostředkovatele identity, klikněte na tlačítko **odeslat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/replicon-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/replicon-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/replicon-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/replicon-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-replicon-test-user"></a>Vytvoření zkušebního uživatele Replicon

Cílem této části je vytvořte uživatele Britta Simon v Replicon. Replicon podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](replicon-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. V okně webového prohlížeče přihlaste jako správce serveru vaší společnosti Replicon.

2. Přejděte na **správu \> uživatelé**.

    ![Uživatelé](./media/replicon-tutorial/ic777806.png "uživatelů")

3. Klikněte na tlačítko **+ přidat uživatele**.

    ![Přidání uživatele](./media/replicon-tutorial/ic777807.png "přidat uživatele")

4. V **profilu uživatele** části, proveďte následující kroky:

    ![Profil uživatele](./media/replicon-tutorial/ic777808.png "profilu uživatele")

    a. V **přihlašovací jméno** textové pole, typ služby Azure AD e-mailová adresa uživatele Azure AD, které chcete zřídit jako **BrittaSimon@contoso.com**.

    b. Jako **typ ověřování**vyberte **jednotného přihlašování**.

    c. V **oddělení** textového pole zadejte oddělení uživatele.

    d. Jako **typ zaměstnance**vyberte **správce**.

    e. Klikněte na tlačítko **profil uživatele uložit**.

>[!NOTE]
>Můžete použít jakékoli jiné Replicon uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Replicon zřízení uživatelských účtů služby Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Replicon použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200]

**Přiřadit Replicon Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Replicon**.

    ![Odkaz Replicon v seznamu aplikací](./media/replicon-tutorial/tutorial_replicon_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Replicon na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Replicon.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](replicon-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/replicon-tutorial/tutorial_general_01.png
[2]: ./media/replicon-tutorial/tutorial_general_02.png
[3]: ./media/replicon-tutorial/tutorial_general_03.png
[4]: ./media/replicon-tutorial/tutorial_general_04.png

[100]: ./media/replicon-tutorial/tutorial_general_100.png

[200]: ./media/replicon-tutorial/tutorial_general_200.png
[201]: ./media/replicon-tutorial/tutorial_general_201.png
[202]: ./media/replicon-tutorial/tutorial_general_202.png
[203]: ./media/replicon-tutorial/tutorial_general_203.png
