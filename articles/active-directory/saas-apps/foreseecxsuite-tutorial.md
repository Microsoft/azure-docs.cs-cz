---
title: 'Kurz: Integrace Azure Active Directory s přijetím CX Suite | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přijetím CX Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: 34de89123e75f0c760fbcefa633b9aadf8d2b997
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52878600"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Kurz: Integrace Azure Active Directory s přijetím Suite CX

V tomto kurzu se dozvíte, jak integrovat přijetím CX Suite s Azure Active Directory (Azure AD).

Integrace přijetím CX Suite s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k sadě CX přijetím.
- Uživatele, aby automaticky získat přihlášení k přijetím Suite CX (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s přijetím CX sady, potřebujete následující položky:

- Předplatné Azure AD
- Přijetím Suite CX jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání přijetím Suite CX z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Přidání přijetím Suite CX z Galerie
Pokud chcete nakonfigurovat integraci sady CX přijetím do služby Azure AD, budete muset přidat přijetím Suite CX z Galerie na váš seznam spravovaných aplikací SaaS.

**Přijetím Suite CX přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **přijetím Suite CX**vyberte **přijetím Suite CX** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Přijetím CX Suite v seznamu výsledků](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s přijetím CX sady založené na testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek sady CX přijetím je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské sady CX přijetím.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s přijetím CX sady, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele přijetím Suite CX](#create-a-foresee-cx-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon přijetím CX Suite, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci přijetím CX Suite.

**Ke konfiguraci Azure AD jednotné přihlašování s přijetím CX Suite, proveďte následující kroky:**

1. Na webu Azure Portal na **přijetím Suite CX** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

1. Na **přijetím CX sada domény a adresy URL** části, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    ![Přijetím CX sada domény a adresy URL jednotné přihlašování – informace](./media/foreseecxsuite-tutorial/upload.png)

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![Přijetím CX sada domény a adresy URL jednotné přihlašování – informace](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    c. Po úspěšném dokončení nahrávání **soubor metadat poskytovatele služeb** **identifikátor** hodnotu get automaticky vyplní v **přijetím CX sada domény a adresy URL** oddílu textové pole, jak je znázorněno níže:

    ![Přijetím CX sada domény a adresy URL jednotné přihlašování – informace](./media/foreseecxsuite-tutorial/urlupload.png)

1. Pokud nemáte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    ![Přijetím CX sada domény a adresy URL jednotné přihlašování – informace](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://cxsuite.foresee.com/`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > Hodnota identifikátoru není skutečný. Aktualizujte tuto hodnotu skutečné identifikátor. Kontakt [tým podpory přijetím klienta Suite CX](mailto:support@foresee.com) tuto výhodu získáte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **přijetím Suite CX** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory přijetím Suite CX](mailto:support@foresee.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Vytvoření zkušebního uživatele přijetím Suite CX

V této části vytvořte uživatele Britta Simon sady CX přijetím. Práce s [tým podpory přijetím Suite CX](mailto:support@foresee.com) k přidání uživatele nebo domény, který je nutný na seznamu povolených na platformě přijetím CX Suite. Pokud je doména přidána týmem, bude získat uživatelé automaticky přiřazeni k přijetím Suite CX platformy. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k sadě CX přijetím.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit přijetím CX Suite, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **přijetím Suite CX**.

    ![Přijetím Suite CX odkaz v seznamu aplikací](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici přijetím Suite CX na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci přijetím CX Suite.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

