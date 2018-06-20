---
title: 'Kurz: Azure Active Directory integrace s Replicon | Microsoft Docs'
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
ms.openlocfilehash: 70a67f5205ee9b1249d3db2cda3eb4af9acabe31
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230171"
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Kurz: Azure Active Directory integrace s Replicon

V tomto kurzu zjistěte, jak integrovat Replicon s Azure Active Directory (Azure AD).

Integrace Replicon s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Replicon.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Replicon (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Replicon, potřebujete následující položky:

- Předplatné služby Azure AD
- Replicon jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Replicon z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-replicon-from-the-gallery"></a>Přidání Replicon z Galerie
Při konfiguraci integrace Replicon do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Replicon z galerie.

**Pokud chcete přidat Replicon z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Replicon**, vyberte **Replicon** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Replicon v seznamu výsledků](./media/replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Replicon podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Replicon je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Replicon musí navázat.

V Replicon, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Replicon, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Replicon](#create-a-replicon-test-user)**  – Pokud chcete mít protějšek Britta Simon v Replicon propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Replicon.

**Ke konfiguraci Azure AD jednotné přihlašování s Replicon, proveďte následující kroky:**

1. Na portálu Azure na **Replicon** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/replicon-tutorial/tutorial_replicon_samlbase.png)

3. Na **Replicon domény a adresy URL** část, proveďte následující kroky:

    ![Replicon domény a adresy URL jednotné přihlašování informace](./media/replicon-tutorial/tutorial_replicon_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://global.replicon.com/<companyname>`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://global.replicon.com/!/saml2/<companyname>/sso/post`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte s skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory Replicon klienta](https://www.replicon.com/customerzone/contact-support) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/replicon-tutorial/tutorial_replicon_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/replicon-tutorial/tutorial_general_400.png)

6. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Replicon.

7. Pokud chcete konfigurovat SAML 2.0, proveďte následující kroky:

    ![Povolit ověřování SAML](./media/replicon-tutorial/ic777805.png "ověřování povolit SAML")

    a. K zobrazení **EnableSAML Authentication2** dialogové okno, připojte na adresu URL, následující po vašeho klíče společnosti: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Následující obrázek znázorňuje schéma úplnou adresu URL: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klikněte na tlačítko **+** rozšířit **v20Configuration** části.

   c. Klikněte na tlačítko **+** rozšířit **metaDataConfiguration** části.

   d. Klikněte na tlačítko **zvolit soubor**, a vyberte soubor XML identity zprostředkovatele metadat, klikněte na tlačítko **odeslání**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/replicon-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/replicon-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/replicon-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/replicon-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-replicon-test-user"></a>Vytvoření zkušebního uživatele Replicon

Cílem této části je vytvoření uživatele v Replicon nazývá Britta Simon. Replicon podporuje automatické uživatele zřizování, který je ve výchozím nastavení povolené. Můžete najít další podrobnosti o [sem](replicon-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. V okně webového prohlížeče Přihlaste se jako správce k serveru vaší společnosti Replicon.

2. Přejděte na **správy \> uživatelé**.

    ![Uživatelé](./media/replicon-tutorial/ic777806.png "uživatelů")

3. Klikněte na tlačítko **+ přidat uživatele**.

    ![Přidat uživatele](./media/replicon-tutorial/ic777807.png "přidat uživatele")

4. V **profil uživatele** část, proveďte následující kroky:

    ![Profil uživatele](./media/replicon-tutorial/ic777808.png "profil uživatele")

    a. V **přihlašovací jméno** textovému poli, typ Azure AD e-mailovou adresu uživatele Azure AD, které chcete zřídit jako **BrittaSimon@contoso.com**.

    b. Jako **typ ověřování**, vyberte **jednotného přihlašování k**.

    c. V **oddělení** textovému poli, zadejte uživatele oddělení.

    d. Jako **typ zaměstnance**, vyberte **správce**.

    e. Klikněte na tlačítko **uložit profil uživatele**.

>[!NOTE]
>Můžete použít všechny ostatní Replicon uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Replicon ke zřízení uživatelských účtů Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Replicon.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon Replicon, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Replicon**.

    ![V seznamu aplikací na Replicon odkaz](./media/replicon-tutorial/tutorial_replicon_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Replicon na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Replicon.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](replicon-provisioning-tutorial.md)

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
