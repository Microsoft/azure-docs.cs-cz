---
title: 'Kurz: Azure Active Directory integrace s konzolou pro správu Mimecast | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konzolou pro správu Mimecast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9edadd6462052f82f92c05c1678f845ece856cfb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160665"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Kurz: Integrace Azure Active Directory s konzolou správce Mimecast

V tomto kurzu se dozvíte, jak integrovat konzolu pro správu Mimecast s Azure Active Directory (Azure AD).
Integrace konzoly pro správu Mimecast s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup ke konzole pro správu Mimecast.
* Uživatelům můžete povolit, aby se automaticky přihlásili do konzoly pro správu Mimecast (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s konzolou pro správu Mimecast potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování konzoly správce Mimecast

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Konzola pro správu Mimecast podporuje jednotné přihlašování na více **aktualizacích**

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Přidání konzoly správce Mimecast z Galerie

Pokud chcete nakonfigurovat integraci konzoly správce Mimecast do služby Azure AD, musíte přidat konzolu správce Mimecast z Galerie do svého seznamu spravovaných aplikací SaaS.

**Chcete-li přidat konzolu správce Mimecast z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Mimecast Console admin**, vyberte **konzolu pro správu Mimecast** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Konzola pro správu Mimecast v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí konzoly pro správu Mimecast na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v konzole pro správu Mimecast.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí konzoly pro správu Mimecast, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování konzoly správce Mimecast](#configure-mimecast-admin-console-single-sign-on)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele konzoly pro správu Mimecast](#create-mimecast-admin-console-test-user)** , abyste měli protějšek Britta Simon v konzole pro správu Mimecast, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí konzoly pro správu Mimecast, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **konzolových aplikací Správce Mimecast** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování domény a adresy URL konzoly správce Mimecast](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:
    
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > Adresa URL pro přihlášení je specifická pro oblast.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení konzoly pro správu Mimecast** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-mimecast-admin-console-single-sign-on"></a>Konfigurace jednotného přihlašování konzoly správce Mimecast

1. V jiném okně webového prohlížeče se do konzoly správce Mimecast přihlaste jako správce.

2. Přejít na **aplikaci \> služby**.

    ![Služby](./media/mimecast-admin-console-tutorial/ic794998.png "Služby")

3. Klikněte na **profily ověřování**.

    ![Profily ověřování](./media/mimecast-admin-console-tutorial/ic794999.png "Profily ověřování")
    
4. Klikněte na **Nový profil ověřování**.

    ![Nové profily ověřování](./media/mimecast-admin-console-tutorial/ic795000.png "Nové profily ověřování")

5. V části **profil ověřování** proveďte následující kroky:

    ![Ověřovací profil](./media/mimecast-admin-console-tutorial/ic795015.png "Ověřovací profil")
    
    a. Do textového pole **Popis** zadejte název konfigurace.
    
    b. Vyberte možnost **vyhovět ověřování SAML pro konzolu správce Mimecast**.
    
    c. Jako **zprostředkovatel**vyberte **Azure Active Directory**.
    
    d. Do textového pole **Adresa URL vystavitele** vložte **identifikátor Azure AD**, který jste zkopírovali z Azure Portal.
    
    e. Vložte **adresu URL pro přihlášení**, kterou jste zkopírovali z Azure Portal do textového pole **Adresa URL pro přihlášení** .

    f. Vložte **adresu URL pro přihlášení**, kterou jste zkopírovali z Azure Portal do TEXTOVÉHO pole **URL pro odhlášení** .
    
    >[!NOTE]
    >Hodnota adresy URL pro přihlášení a hodnota adresy URL pro odhlášení jsou pro konzolu správce Mimecast stejné.
    
    g. Otevřete certifikát základní-64 stažený z Azure Portal v poznámkovém bloku, odeberte první řádek (*--*"") a poslední řádek ("*--*"), zkopírujte zbývající obsah do schránky a vložte ho do textového pole **certifikát poskytovatele identity (metadata)** .
    
    h. Vyberte možnost **povolení jednotného přihlašování**.
    
    i. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure udělením přístupu ke konzole pro správu Mimecast.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Konzola pro správu Mimecast**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **konzolu pro správu Mimecast**.

    ![Odkaz na konzolu správce Mimecast v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-mimecast-admin-console-test-user"></a>Vytvořit testovacího uživatele konzoly pro správu Mimecast

Aby se uživatelé Azure AD mohli přihlašovat do konzoly pro správu Mimecast, musí se zřídit v konzole pro správu Mimecast. V případě konzoly pro správu Mimecast je zřizování ručním úkolem.

* Než budete moct vytvořit uživatele, musíte zaregistrovat doménu.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se ke **konzole správce Mimecast** jako správce.

2. Přejít na **interní \> adresáře**.
   
    ![Adresáře](./media/mimecast-admin-console-tutorial/ic795003.png "Adresáře")

3. Klikněte na **zaregistrovat novou doménu**.
   
    ![Zaregistrovat novou doménu](./media/mimecast-admin-console-tutorial/ic795004.png "Zaregistrovat novou doménu")

4. Po vytvoření nové domény klikněte na **Nová adresa**.
   
    ![Nová adresa](./media/mimecast-admin-console-tutorial/ic795005.png "Nová adresa")

5. V dialogovém okně Nová adresa proveďte následující kroky:
   
    ![Uložit](./media/mimecast-admin-console-tutorial/ic795006.png "Uložit")
   
    a. Zadejte **e-mailovou adresu**, **globální název**, **heslo**a **POTVRĎte atributy hesla** platného účtu Azure AD, který chcete zřídit v souvisejících textových polích.

    b. Klikněte na **Uložit**.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API konzoly pro správu Mimecast, které poskytuje konzola pro správu Mimecast. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici konzoly pro správu Mimecast na přístupovém panelu byste měli být automaticky přihlášeni ke konzole pro správu Mimecast, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

