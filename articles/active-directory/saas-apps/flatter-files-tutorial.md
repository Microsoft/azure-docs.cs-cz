---
title: 'Kurz: Azure Active Directory integrace se soubory plošší | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi soubory Azure Active Directory a plošší.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: fae21c98c7340614a96b6e85aa79bdea7889a807
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453210"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Kurz: Azure Active Directory integrace se soubory plošší

V tomto kurzu se naučíte, jak integrovat soubory plošší s Azure Active Directory (Azure AD).
Integrace souborů plošší do Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k souborům plošší.
* Uživatelům můžete povolit, aby se automaticky přihlásili k souborům plošší (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD se soubory plošší potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování souborů plošší

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Soubory plošší podporují jednotné přihlašování **IDP** .

## <a name="adding-flatter-files-from-the-gallery"></a>Přidávání souborů plošší z Galerie

Pokud chcete nakonfigurovat integraci souborů plošší do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat soubory plošší z galerie.

**Chcete-li přidat soubory plošší z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **plošší Files**, vyberte **soubory plošší** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Soubory plošší v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí souborů plošší na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v souborech plošší.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí souborů plošší, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování souborů plošší](#configure-flatter-files-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření plošší souborů testovacího uživatele](#create-flatter-files-test-user)** – pro Britta Simon v souborech plošší, které jsou propojené s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí souborů plošší, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **souborů plošší** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V **základní části Konfigurace SAML** nemusí uživatel provádět žádný krok, protože aplikace už je předem integrovaná s Azure.

    ![Informace o jednotném přihlašování plošší souborů a adres URL](common/preintegrated.png)

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení souborů plošší** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-flatter-files-single-sign-on"></a>Konfigurace plošší souborů s jedním Sign-On

1. Přihlaste se k vaší aplikaci plošší Files jako správce.

2. Klikněte na **řídicí panel**. 
   
    ![Snímek obrazovky, který zobrazuje řídicí panel vybraný v aplikaci plošší Files](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Klikněte na **Nastavení** a potom na kartě **Společnost** proveďte následující kroky: 
   
    ![Snímek obrazovky, který zobrazuje kartu společnost s zaškrtnutým políčkem pro použití S A M L 2,0 pro ověřování a vybráním tlačítka konfigurovat S A M L.](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. **Pro ověřování vyberte použít SAML 2,0**.
    
    b. Klikněte na **Konfigurovat SAML**.

4. V dialogovém okně **Konfigurace SAML** proveďte následující kroky: 
   
    ![Konfigurace jednoho Sign-On](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Do textového pole **doména** zadejte registrovanou doménu.
   
   > [!NOTE]
   > Pokud ještě nemáte registrovanou doménu, obraťte se na tým podpory souborů plošší prostřednictvím [support@flatterfiles.com](mailto:support@flatterfiles.com) . 
    
    b. Do textového pole **Adresa URL poskytovatele identity** vložte hodnotu **přihlašovací adresa URL** , kterou jste zkopírovali Azure Portal formuláře.
   
    c.  Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát poskytovatele identity** .

    d. Klikněte na **Aktualizovat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k souborům plošší.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte **soubory plošší**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **soubory plošší**.

    ![Odkaz soubory plošší v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-flatter-files-test-user"></a>Vytvořit testovacího uživatele pro soubory plošší

Cílem této části je vytvořit uživatele s názvem Britta Simon v souborech plošší.

**Pokud chcete v souborech plošší vytvořit uživatele s názvem Britta Simon, proveďte následující kroky:**

1. Přihlaste se ke svému webu **plošší Files** jako správce.

2. V navigačním podokně na levé straně klikněte na **Nastavení** a pak klikněte na kartu **Uživatelé** .
   
    ![Snímek obrazovky zobrazující stránku nastavení s vybranou kartou uživatelé](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Klikněte na **Add User** (Přidat uživatele). 

4. V dialogovém okně **Přidat uživatele** proveďte následující kroky:
   
    ![Vytvoření uživatele souborů plošší](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Do textového pole **jméno v prvním** poli zadejte **Britta**.
   
    b. Do textového pole **příjmení** zadejte **Simon**. 
   
    c. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu Britta v Azure Portal.
   
    d. Klikněte na **Odeslat**.   


### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici soubory plošší na přístupovém panelu, měli byste se automaticky přihlásit k plošší souborům, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)