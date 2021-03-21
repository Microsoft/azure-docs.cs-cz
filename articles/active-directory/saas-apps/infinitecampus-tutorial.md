---
title: 'Kurz: Azure Active Directory integrace s nekonečným areálem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a nekonečné areály.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 08cee87d94b20e99bcc46d42cbac7eb4272561bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460224"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Kurz: Azure Active Directory integrace s nekonečným areálem

V tomto kurzu se dozvíte, jak integrovat nekonečný areál práce s Azure Active Directory (Azure AD).
Integrování nekonečné školních areálů pomocí Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k nekonečné Campus.
* Uživatelům můžete povolit, aby se automaticky přihlásili k nekonečnému areálu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s neomezeným areálem, budete potřebovat tyto položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Neomezené předplatné s povoleným jednotným přihlašováním
* Aby se konfigurace dokončila, musíte být správcem Azure Active Directory a máte k dispozici roli zabezpečení školních produktů (SIS).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Neomezený areál, který podporuje jednotné přihlašování v **SP**

## <a name="adding-infinite-campus-from-the-gallery"></a>Přidání nekonečného areálu z Galerie

Pokud chcete nakonfigurovat integraci nekonečného areálu do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat nekonečný areál z galerie.

**Chcete-li přidat nekonečný areál z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **infinité areály**, v panelu výsledek vyberte **nekonečná areály** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Neomezený areál v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s neomezeným areálem na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nekonečném areálu.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí nekonečné školních areálů, je potřeba, abyste dokončili tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace neomezeného jednotného přihlašování](#configure-infinite-campus-single-sign-on)** – pro konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření nekonečného školního testovacího uživatele](#create-infinite-campus-test-user)** – Pokud chcete mít protějšek Britta Simon v nekonečném areálu, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí nekonečné školních areálů, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **nekonečná integrace aplikace Campus** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části základní konfigurační oddíl SAML proveďte následující kroky (Všimněte si, že se doména bude lišit v případě hostujícího modelu, ale hodnota **plně kvalifikované domény** musí odpovídat vaší nekonečné instalaci areálu):

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Nekonečná doména školního areálu a adresy URL jednotného přihlašování](common/sp-identifier-reply.png)

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Konfigurace nekonečně jednoduchého areálu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k nekonečnému areálu jako správce zabezpečení.

2. Na levé straně nabídky klikněte na **Správa systému**.

    ![Správce](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Přejděte na **zabezpečení uživatele**  >    >  **Konfigurace poskytovatele služeb pro jednotné přihlašování** v nástroji SAML.

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Na stránce **Konfigurace poskytovatele služeb jednotného přihlašování** proveďte následující kroky:

    ![Jednotné přihlašování](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Vyberte **Povolit jednotné přihlašování SAML**.

    b. Upravte **volitelný název atributu** tak, aby obsahoval **název** .

    c. V části **vybrat možnost načtení dat serveru zprostředkovatele identity (IDP)** vyberte **URL metadata**, vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal v poli a pak klikněte na **synchronizovat**.

    d. Po kliknutí na **synchronizovat** se hodnoty získá automaticky vyplněné na stránce **Konfigurace poskytovatele služeb jednotného přihlašování** . Tyto hodnoty lze ověřit tak, aby odpovídaly hodnotám zobrazeným v kroku 4 výše.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například, BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

> [!NOTE]
> Pokud chcete, aby všichni uživatelé Azure měli přístup s jednotným přihlašováním k nekonečnému areálu a při řízení přístupu nemusíte spoléhat na nekonečné interní systémové oprávnění, můžete nastavit vlastnost **požadovaná pro přiřazení uživatele** u aplikace na ne a přeskočit následující kroky.

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k nekonečnému areálu.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **nekonečné areály**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **nekonečná areály**.

    ![Odkaz nekonečné Campus v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-infinite-campus-test-user"></a>Vytvořit neomezený uživatel pro testování Campus

Nekonečné areály mají demografické architektury s centry. Obraťte se prosím na [neomezený tým podpory pro vysokoškolské pracovníky](mailto:sales@infinitecampus.com) a přidejte uživatele do nekonečné školní platformy.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici nekonečné Campy na přístupovém panelu, měli byste se automaticky přihlásit k nekonečnému areálu, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)