---
title: 'Kurz: Integrace Azure Active Directory s praktickými cvičeními úpravu – mobilní a webové testování | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a úpravu Labs – mobilní a webové testování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.openlocfilehash: 55d84256f408e80600308ede22dbaa903b070d90
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265557"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Kurz: Integrace Azure Active Directory s praktickými cvičeními úpravu - Mobile a testování webů

V tomto kurzu se dozvíte, jak integrovat úpravu Labs – mobilní a webové testování pomocí služby Azure Active Directory (Azure AD).

Integrace úpravu Labs – mobilní a webové testování pomocí služby Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k testovacím prostředím úpravu - Mobile a testování webů.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k co Labs – mobilní a webové testování (Single Sign-On) pomocí svých účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s praktickými cvičeními úpravu – mobilní a webové testování, potřebujete následující položky:

- S předplatným služby Azure AD
- Co Labs – mobilní a webové testování jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání úpravu Labs – mobilní a webové testy z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Přidání úpravu Labs – mobilní a webové testy z Galerie
Konfigurace integrace úpravu Labs – mobilní a webové testování do služby Azure AD, budete muset přidat úpravu Labs – mobilní a webové testy z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat úpravu Labs – mobilní a webové testy z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **úpravu Labs – mobilní a webové testování**vyberte **úpravu Labs – mobilní a webové testování** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Co Labs – mobilní zařízení a v seznamu výsledků testování webů](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s praktickými cvičeními úpravu – mobilní a webové testování založené na testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v úpravu Labs – mobilní a webové testování je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v co Labs – mobilní a webové testování.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s praktickými cvičeními úpravu – mobilní zařízení a testování webů, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvářet testovací prostředí úpravu – mobilní a webové testování testovací uživatele](#create-a-sauce-labs---mobile-and-web-testing-test-user)**  – Pokud chcete mít protějšek Britta Simon úpravu Labs – mobilní a webové testování, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v co Labs – mobilní a webové testování aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s praktickými cvičeními úpravu - Mobile a testování webů, postupujte následovně:**

1. Na webu Azure Portal na **úpravu Labs – mobilní a webové testování** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. Na **úpravu Labs – mobilní a webové testování domény a adresy URL** části uživatel nemá k provedení všech kroků jako aplikace je už předem integrováno s Azure.

    ![Co Labs – mobilní a webové testování domény a adresy URL jednotné přihlašování – informace](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. V okně jiné webové prohlížeče Přihlaste se k co Labs – mobilní a webové testování webu společnosti jako správce.

7. Klikněte na **ikonu uživatele** a vyberte **Správa týmu** kartu.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. Zadejte vaše **název domény** do textového pole.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. Klikněte na tlačítko **konfigurovat** kartu.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. V **konfigurovat Single Sign On** části, proveďte následující kroky.

    ![Konfigurace jednotného přihlašování](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klikněte na tlačítko **Procházet** a nahrajte soubor stažený metadat ze služby Azure AD.

    b. Vyberte **povolit zřizování za běhu** zaškrtávací políčko.

    c. Projekt **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Vytvářet testovací prostředí úpravu – mobilní a webové testování testovací uživatele

Cílem této části je vytvořte uživatele Britta Simon v co Labs – mobilní a webové testování. Sauce Labs – mobilní zařízení a testování webů podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k co Labs – mobilních a webových testů, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [úpravu Labs – mobilní a webové testování tým podpory](mailto:support@saucelabs.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tak, že udělíte přístup k testovacím prostředím úpravu – mobilní a webové testování.

![Přiřazení role uživatele][200]

**Přiřadit Britta Simon úpravu Labs – mobilní zařízení a testování webů, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **úpravu Labs – mobilní a webové testování**.

    ![Co Labs – mobilní a webové testování na odkaz v seznamu aplikací](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Při kliknutí na úpravu Labs – mobilní a webové testování dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k co Labs – mobilní a webové testování aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png