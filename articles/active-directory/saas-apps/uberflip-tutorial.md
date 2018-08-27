---
title: 'Kurz: Integrace Azure Active Directory se službou Uberflip | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: jeedes
ms.openlocfilehash: bb1f53895fcd91a9474302fcf8c9e0040fe91961
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42920565"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Kurz: Integrace Azure Active Directory se službou Uberflip

V tomto kurzu se dozvíte, jak integrovat Uberflip s Azure Active Directory (Azure AD).

Uberflip integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Uberflip.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Uberflip (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Uberflip, potřebujete následující položky:

- Předplatné Azure AD
- Uberflip jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Uberflip z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-uberflip-from-the-gallery"></a>Přidání Uberflip z Galerie

Konfigurace integrace Uberflip do služby Azure AD, budete muset přidat Uberflip z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Uberflip z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Uberflip**vyberte **Uberflip** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Uberflip v seznamu výsledků](./media/uberflip-tutorial/tutorial_uberflip_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Uberflip podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Uberflip je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Uberflip potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Uberflip, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele Uberflip](#create-an-uberflip-test-user)**  – Pokud chcete mít protějšek Britta Simon Uberflip, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Uberflip.

**Ke konfiguraci Azure AD jednotné přihlašování s Uberflip, proveďte následující kroky:**

1. Na webu Azure Portal na **Uberflip** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/uberflip-tutorial/tutorial_uberflip_samlbase.png)

3. Na **Uberflip domény a adresy URL** části, proveďte následující krok, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Uberflip domény a adresy URL jednotného přihlašování – informace](./media/uberflip-tutorial/tutorial_uberflip_url1.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

    > [!NOTE]
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu Skutečná adresa URL odpovědi. Kontakt [tým podpory Uberflip klienta](mailto:support@uberflip.com) tuto výhodu získáte.

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Uberflip domény a adresy URL jednotného přihlašování – informace](./media/uberflip-tutorial/tutorial_uberflip_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://app.uberflip.com/users/login`

5. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/uberflip-tutorial/tutorial_uberflip_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/uberflip-tutorial/tutorial_general_400.png)

7. Ke konfiguraci jednotného přihlašování na **Uberflip** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory Uberflip](mailto:support@uberflip.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/uberflip-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/uberflip-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/uberflip-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/uberflip-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-an-uberflip-test-user"></a>Vytvořit testovacího uživatele Uberflip

Cílem této části je vytvořte uživatele Britta Simon v Uberflip. Uberflip podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Uberflip, pokud ještě neexistuje.

> [!Note]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Uberflip](mailto:support@uberflip.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Uberflip použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Uberflip Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Uberflip**.

    ![Odkaz Uberflip v seznamu aplikací](./media/uberflip-tutorial/tutorial_uberflip_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Uberflip na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Uberflip.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/uberflip-tutorial/tutorial_general_01.png
[2]: ./media/uberflip-tutorial/tutorial_general_02.png
[3]: ./media/uberflip-tutorial/tutorial_general_03.png
[4]: ./media/uberflip-tutorial/tutorial_general_04.png

[100]: ./media/uberflip-tutorial/tutorial_general_100.png

[200]: ./media/uberflip-tutorial/tutorial_general_200.png
[201]: ./media/uberflip-tutorial/tutorial_general_201.png
[202]: ./media/uberflip-tutorial/tutorial_general_202.png
[203]: ./media/uberflip-tutorial/tutorial_general_203.png